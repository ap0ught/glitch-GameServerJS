# Glitch Server Architecture

This document provides a deep technical dive into the architecture of the Glitch game server, explaining how the various components work together to create a complete MMO experience.

## Overview

The Glitch server is built as a **message-driven JavaScript application** running on the Rhino JavaScript engine within a Java environment, designed to work seamlessly with the [Glitch Flash Client](https://github.com/ap0ught/glitch-client). The architecture emphasizes:

- **Event-driven processing** for all player interactions - *Essential for real-time MMO responsiveness*
- **Modular component design** with clear separation of concerns - *Enables independent development and testing*
- **Data-driven configuration** for different environments - *Supports dev/test/production deployments*
- **Extensible scripting system** for content creation - *Allows rapid content iteration without core changes*
- **Client-server state synchronization** - *Maintains consistent game world across all players*

### Architectural Significance

This design demonstrates several key principles crucial for MMO development:

1. **Authoritative Server Architecture**: All game logic runs server-side, with the Flash client serving as a presentation layer
2. **Message-Based Communication**: Enables loose coupling between client and server systems
3. **Horizontal Scalability**: Event-driven processing allows for distributed server architectures
4. **Security by Design**: Client actions are validated server-side, preventing common exploits
5. **Operational Excellence**: Built-in administrative tools and monitoring capabilities

## Core Architecture Components

### Message Processing System

The heart of the server is the message processing system in `main.js`:

```javascript
function processMessage(pc, msg){
    processMessageInner(pc, msg);
    pc.performPostProcessing(msg);
}
```

**Message Types Handled:**
- **Movement**: `move_vec`, `move_xy`, `signpost_move_start/end`, `door_move_start/end` - *Real-time position synchronization*
- **Authentication**: `login_start/end`, `relogin_start/end` - *Secure player session management*
- **Item Interaction**: `itemstack_verb`, `itemstack_menu_up`, `itemstack_inspect` - *Rich object interaction system*
- **Communication**: `local_chat`, `local_chat_start` - *Social interaction and community features*
- **Administration**: `edit_location` (god mode only) - *Live world editing and content management*

### Client-Server Communication Architecture

The server's message processing system is specifically designed to work with the ActionScript 3 Flash client, demonstrating a complete MMO communication protocol:

```javascript
// Example: Item interaction flow from client to server
case 'itemstack_verb':
    // 1. Validate client message structure
    if (!msg.itemstack_tsid || !msg.verb) return;
    
    // 2. Server-side permission checking
    var item = pc.getInventoryItem(msg.itemstack_tsid);
    if (!item || !item.hasVerb(msg.verb)) return;
    
    // 3. Execute authoritative game logic
    var result = item.performVerb(pc, msg.verb);
    
    // 4. Broadcast state changes to relevant clients
    pc.sendItemstackUpdate(item);
    return result;
```

**Key Architectural Benefits:**
- **Security**: All game logic validation happens server-side
- **Consistency**: Single source of truth for game state
- **Scalability**: Stateless message processing enables horizontal scaling
- **Debugging**: Complete audit trail of all player actions

### Player Context (PC) System

Every player interaction is processed through a Player Context object that maintains:

- **State Management**: Current location, inventory, buffs, statistics
- **Capability Checks**: Permissions, god mode status, skill levels
- **Post-Processing**: Cleanup tasks after message handling

### Configuration Management

The server uses a multi-tier configuration system:

```
config_base.js     - Shared settings for all environments
config_dev.js      - Development-specific overrides  
config_prod.js     - Production environment settings
config_test_prod.js - Test environment settings
```

**Key Configuration Areas:**
- **Physics Settings**: Movement speeds, gravity, friction
- **Game Constants**: Timeouts, limits, scaling factors
- **Environment URLs**: API endpoints, asset servers
- **Feature Flags**: Development vs production feature toggles

## Data Architecture

### Item System Architecture

Items follow a class-based inheritance model using JavaScript includes:

```javascript
//#include include/takeable.js
//#include include/food.js

var label = "Apple";
var parent_classes = ["apple", "food", "takeable"];
```

**Item Class Hierarchy:**
- **Base Classes**: `takeable`, `furniture`, `npc_ai`
- **Behavior Mixins**: `food`, `drink`, `tool`, `container`
- **Specialized Classes**: `cooking`, `mining`, `cultivation`

### Achievement System Architecture

Achievements use a declarative condition/reward system:

```javascript
var conditions = {
    42 : {
        type    : "counter",
        group   : "making_known_tool", 
        label   : "awesome_pot",
        value   : "11"
    }
};

var rewards = {
    "xp"    : 75,
    "favor" : { "giant": "pot", "points": 10 },
    "recipes": { "0": { "recipe_id": "30" } }
};
```

**Achievement Components:**
- **Conditions**: Counter-based, skill-based, item-based, location-based
- **Rewards**: Experience points, favor points, recipes, items
- **Multipliers**: Based on buffs, level, completion bonuses

### Quest System Architecture

Quests are implemented as self-contained JavaScript modules with:

```javascript
// Quest state management
function onStarted(pc) { /* initialization */ }
function onCompleted(pc) { /* cleanup and rewards */ }

// Conversation trees
var conversations = {
    "intro": { /* branching dialogue */ },
    "progress": { /* status updates */ },
    "completion": { /* ending dialogue */ }
};
```

## Data Storage Patterns

### Massive Include System

The server uses an extensive `#include` system for data organization:

```javascript
//#include inc_data_clothing.js     // 1.2MB of clothing definitions
//#include inc_data_faces.js        // 77KB of facial features  
//#include inc_data_skills.js       // 141KB of skill definitions
//#include inc_data_imagination.js  // 185KB of currency/imagination data
```

### Environment-Specific Data

Data files are split by environment to support different content:

- `inc_data_maps_dev.js` vs `inc_data_maps_prod.js` 
- `inc_data_homes_dev.js` vs `inc_data_homes_prod.js`
- `inc_data_feats_dev.js` vs `inc_data_feats_prod.js`

This allows testing new content without affecting production.

## API Integration Layer

### External API Communication

The server integrates with external web APIs through `utils.js`:

```javascript
function http_get(url, args) {
    var full = config.web_api_url + url + '?' + querystring;
    apiAsyncHttpCall(full, {});
}
```

**API Integration Points:**
- **Web Dashboard**: Player statistics and game state
- **Analytics**: Event tracking and metrics collection  
- **Social Features**: Friend lists and external sharing
- **Payment Systems**: Premium currency and transactions

### Administrative APIs

Administrative functions in `admin.js` provide:

- **Player Management**: Online status, forced actions
- **Content Management**: Item spawning, location editing
- **Group Administration**: Group creation and management
- **Instance Population**: Dynamic content generation

## Performance Patterns

### Lazy Loading

Many systems use lazy initialization to improve startup performance:

```javascript
// Items are only fully loaded when first accessed
function getItemDefinition(class_id) {
    if (!loaded_items[class_id]) {
        loaded_items[class_id] = loadItemClass(class_id);
    }
    return loaded_items[class_id];
}
```

### Caching Strategies

- **Static Data Caching**: Item definitions, recipes, achievement data
- **Player State Caching**: Recently accessed player data
- **Location Caching**: Frequently visited location state

### Memory Management

The server implements several memory management patterns:

- **Object Pooling**: Reuse of common objects like messages
- **Garbage Collection Hints**: Manual cleanup of large temporary objects
- **Reference Counting**: Careful management of cross-references

## Security Architecture

### Permission System

The server implements a role-based permission system:

```javascript
if (pc.is_god) {
    // Administrative functions available
    return doCreateItem(pc, msg);
}

if (pc.hasSkillLevel('cooking', 5)) {
    // Advanced cooking recipes available
}
```

### Input Validation

All player inputs are validated before processing:

- **Message Structure**: Required fields and data types
- **Range Checking**: Coordinates, quantities, identifiers
- **Permission Checks**: Action authorization before execution
- **Rate Limiting**: Prevention of spam and abuse

### Anti-Cheat Measures

- **Server Authority**: All game state maintained server-side
- **Action Validation**: Physics checks for movement and interaction
- **Inventory Tracking**: Complete audit trail for item transactions
- **Stat Verification**: Server-side calculation of all progression

## Scalability Considerations

### Horizontal Scaling Patterns

While this codebase represents a single server instance, it includes patterns for scaling:

- **Stateless Message Processing**: Each message can be processed independently
- **Location Partitioning**: Different areas can run on different servers
- **Player Sharding**: Player data can be distributed across databases

### Resource Management

- **Connection Pooling**: Efficient database connection usage
- **Asset Streaming**: Dynamic loading of game assets
- **Memory Profiling**: Tools for monitoring server resource usage

## Development Patterns

### Modular Design

The codebase emphasizes modularity:

- **Functional Modules**: Each system in its own file/directory
- **Shared Utilities**: Common functions in `utils.js` and `common.js`
- **Environment Separation**: Clear development vs production boundaries

### Code Organization

```
Core Systems:     main.js, common.js, utils.js
Configuration:    config_*.js
Game Content:     items/, achievements/, quests/
Data Definitions: inc_data_*.js  
Utilities:        utils/, admin.js
```

### Extension Points

The architecture provides clear extension points for:

- **New Item Types**: Adding item behavior classes
- **Custom NPCs**: Implementing AI behaviors and conversations  
- **Quest Scripting**: Creating new quest types and mechanics
- **Achievement Design**: Defining new progression systems

This architecture enabled Glitch to support hundreds of thousands of players with rich, interactive content while maintaining code clarity and extensibility for rapid content development.