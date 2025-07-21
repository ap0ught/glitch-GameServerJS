# Development Guide

This guide helps developers explore, understand, and learn from the Glitch server codebase. Whether you're studying MMO architecture, game design patterns, or just trying to understand how your favorite game worked, this document will help you navigate the code effectively.

## 🚀 Getting Started

### Prerequisites

The Glitch server was designed to run on:
- **Rhino JavaScript Engine** (Mozilla's Java-based JavaScript runtime)
- **Java Virtual Machine** (for hosting Rhino)
- **Custom Native APIs** (for database access, networking, etc.)

While you can't run this server directly without the original infrastructure, you can:
- Analyze the code structure and patterns for modern applications
- Extract reusable components and architectural ideas  
- Study the game design implementations for your own projects
- Learn from the architecture decisions made for a production MMO
- Understand how this server communicates with the [Glitch Flash Client](https://github.com/ap0ught/glitch-client)

### Repository Navigation

Start exploring with these key entry points that show how the complete system works:

```bash
# Core server logic - shows the heart of the MMO
main.js                 # Message processing and game loop
common.js              # Shared utilities and functions  
utils.js               # External integrations and helpers

# Configuration system - demonstrates environment management
config_base.js         # Shared settings
config_dev.js          # Development environment
config_prod.js         # Production environment

# Game content - the actual gameplay systems
items/                 # 1,288+ item definitions with behaviors
achievements/          # 665 achievement implementations  
quests/                # 448+ quest scripts with narrative content

# Client relationship files - show how server and client interact
inc_data_*.js          # Data synchronized with Flash client
admin.js               # Tools for managing live game operations
```

**Why This Structure Matters**: This organization demonstrates how to separate concerns in a large game codebase - core logic, content, configuration, and client integration are cleanly separated, making the system maintainable and scalable.

## 🔍 Code Exploration Strategies

### Understanding the Message Flow

The core of Glitch's architecture is message-driven communication between client and server. This pattern is essential for real-time multiplayer games:

1. **Entry Point**: `processMessage(pc, msg)` in `main.js` - *All player actions start here*
2. **Message Types**: Switch statement handling different action types - *Shows how to organize different game systems*
3. **Player Context**: How `pc` object maintains player state - *Demonstrates state management in multiplayer games*
4. **Post-Processing**: Cleanup and secondary effects - *Shows how to handle cascading game events*

```javascript
// Example: Tracing item interaction from client to server
case 'itemstack_verb': return doVerb(pc, msg);
// This shows how a Flash client action becomes a server-side verb execution
```

**Why This Matters**: This message-driven architecture solves several critical MMO challenges:
- **Security**: All actions are validated server-side
- **Consistency**: Game state is authoritative on the server
- **Scalability**: Messages can be processed asynchronously
- **Debugging**: Every player action is traceable through the system

**Modern Applications**: This pattern directly applies to:
- WebSocket-based multiplayer games
- Real-time collaborative applications
- Mobile game backends
- Any system requiring authoritative state management

### Exploring Item Systems

Items are the core of Glitch's gameplay and demonstrate sophisticated object-oriented design patterns. Understanding this system is crucial for anyone building interactive game objects:

1. **Start with Simple Items**: Look at `items/apple.js` or `items/banana.js` - *Shows basic item structure*
2. **Understand Inheritance**: Check `items/include/` for base classes - *Demonstrates component-based design*
3. **Complex Examples**: Examine cooking tools or furniture - *Shows advanced interaction patterns*
4. **Data Relationships**: See how items reference recipes, skills, etc. - *Illustrates game system integration*

```bash
# Find all food items - understand how eating mechanics work
grep -r "food" items/ | grep "parent_classes"

# Discover item verbs (actions) - see how players interact with objects
grep -r "verbs" items/ | head -20

# Find items with special properties - learn about unique mechanics
grep -r "has_info.*true" items/
```

**Why This System Is Significant**:
- **Modularity**: Each item can mix and match behaviors through inheritance
- **Extensibility**: New items can be created by combining existing behaviors
- **Consistency**: All items follow the same interaction patterns
- **Richness**: Items have state, respond to actions, and affect the world

**Modern Applications**:
- Unity's component system follows similar patterns
- React's composition patterns mirror the item inheritance structure
- Game frameworks like Phaser can implement similar item behavior systems
- The verb-based interaction model applies to any interactive object system

**Client-Server Integration**: Items demonstrate how the server maintains authoritative state while the Flash client handles presentation:
- Server validates all item interactions
- Client receives item state updates and renders appropriately
- Verbs (actions) are defined server-side but triggered by client input
- Item data flows from server to client through synchronized data structures

### Achievement Analysis

Achievements show how progression systems work in practice and demonstrate data-driven player engagement mechanics:

```bash
# Find cooking achievements - see how skill progression works
ls achievements/*cuisine* achievements/*cook*

# See achievement conditions - understand trigger systems
grep -r "conditions" achievements/ | head -10

# Check reward systems - learn about player motivation
grep -r "rewards" achievements/ | head -10
```

**Why This System Matters**:
- **Player Retention**: Achievements provide long-term goals that keep players engaged
- **Progress Tracking**: Demonstrates how to monitor player accomplishments across multiple game systems
- **Motivation Design**: Shows how to create meaningful milestones that feel rewarding
- **Social Recognition**: Many achievements are public, encouraging community engagement

**Architecture Insights**:
- **Event-Driven**: Achievements trigger based on player actions and state changes
- **Flexible Conditions**: Complex Boolean logic determines when achievements unlock
- **Cross-System Integration**: Achievements can track progress across items, quests, social interactions, etc.
- **Scalable Design**: New achievements can be added without changing core systems

**Modern Applications**:
- Mobile game achievement systems use similar patterns
- Learning platforms implement progress tracking using these concepts
- Social media platforms use achievement-like systems for engagement
- Gamification frameworks employ similar motivation mechanics

### Quest System Deep Dive

Quests demonstrate narrative and progression design in an MMO context, showing how to deliver story content in a persistent multiplayer world:

```bash
# Find tutorial quests - see how new players are onboarded
ls quests/*tutorial* quests/*newbie*

# Look for conversation systems - understand dialogue mechanics
grep -r "conversation" quests/

# Find quest requirements - see how progression gates work
grep -r "require" quests/
```

**Why Quest Design Is Critical**:
- **Player Guidance**: Quests teach players how to use game systems effectively
- **Narrative Delivery**: Stories provide context and meaning to player actions
- **Progression Structure**: Quests create clear paths through complex game content
- **Content Pacing**: Designers can control how quickly players access new features

**Technical Implementation Insights**:
- **State Management**: Quests track complex progress across multiple player sessions
- **Branching Logic**: Conditional quest paths based on player choices and state
- **Integration**: Quests tie together items, achievements, locations, and social systems
- **Scripting System**: Flexible quest scripting allows for rich narrative experiences

**Client-Server Coordination**: Quests show how narrative content works across client and server:
- Server tracks quest state and validates progress
- Client displays quest UI, dialogue, and narrative elements
- Synchronized quest data ensures consistent experience across sessions
- Quest completion triggers both client celebration and server-side rewards

**Modern Applications**:
- Educational platforms use quest-like progression systems
- Onboarding systems in apps follow similar tutorial quest patterns
- Interactive fiction uses similar branching narrative structures
- Training simulations employ quest-like scenario systems

## 🔗 Understanding Client-Server Integration

### The Complete Glitch Architecture

To fully understand this server, you need to see how it worked with the [Glitch Flash Client](https://github.com/ap0ught/glitch-client). Together, they demonstrate a complete MMO architecture:

#### Communication Protocol
- **ActionScript 3 to JavaScript**: Client sends JSON messages to server
- **Real-time bidirectional**: WebSocket-like communication for immediate response
- **Message validation**: Server validates all client actions for security
- **State synchronization**: Client and server maintain consistent world state

#### Data Flow Examples

**Item Interaction Flow**:
1. Player clicks item in Flash client
2. Client sends `itemstack_verb` message to server
3. Server validates permissions and item state
4. Server executes item behavior and updates world state
5. Server sends results back to client
6. Client updates visual representation

**Avatar Customization Flow**:
1. Flash client provides avatar editor interface
2. Client sends customization data to server
3. Server validates choices and updates player record
4. Server broadcasts avatar changes to other players
5. All clients update their visual representation of the player

#### Why This Relationship Matters

**For Modern Developers**:
- Shows how to maintain authoritative server state while providing responsive client experience
- Demonstrates message-based communication patterns applicable to modern WebSocket APIs
- Illustrates how to synchronize complex game state across multiple clients
- Provides examples of client-side prediction and server reconciliation

**For Security**:
- All game logic runs server-side, preventing cheating
- Client is purely a presentation layer with input handling
- Server validates every action before applying changes
- Demonstrates proper separation of concerns for multiplayer games

**For Performance**:
- Message batching and optimization techniques
- Efficient state synchronization strategies
- Asset loading coordination between client and server
- Bandwidth optimization for real-time multiplayer

### Analyzing Cross-Repository Patterns

To get the full picture, examine these relationships:

```bash
# Server data files that sync with client
ls inc_data_*.js

# Server items that have corresponding client assets
grep -r "swf\|png\|jpg" items/

# Admin tools for managing live client connections
grep -r "api.*" admin.js
```

**Key Takeaways for Modern Development**:
1. **Clear API Boundaries**: Server and client communicate through well-defined message protocols
2. **Authoritative State**: Game state lives on the server, client is a view layer
3. **Graceful Degradation**: System handles network issues and client disconnections
4. **Operational Tools**: Administrative interfaces for managing live multiplayer systems

## 🏗️ Architecture Patterns

### Message-Driven Architecture

The server uses event-driven processing for all player actions, demonstrating how to build scalable real-time systems:

```javascript
// Pattern: Message handling with validation
function doVerb(pc, msg) {
    // 1. Validate input
    if (!msg.itemstack_tsid) return;
    
    // 2. Check permissions
    if (!pc.canInteractWith(item)) return;
    
    // 3. Execute action
    return item.performVerb(pc, msg.verb);
}
```

**Key Benefits for MMO Development**:
- **Scalability**: Each message can be processed independently, enabling horizontal scaling
- **Debugging**: Clear trace of all player actions simplifies troubleshooting
- **Security**: All actions validated server-side prevents client-side exploits
- **Extensibility**: Easy to add new message types without changing core architecture
- **Reliability**: Failed messages don't crash the entire system

**Why This Matters for Modern Games**:
This pattern is essential for any real-time multiplayer game and directly applies to:
- WebSocket-based browser games
- Mobile multiplayer backends
- Real-time collaborative applications
- Any system requiring authoritative state management

### Component-Based Item System

Items use mixin-style inheritance for behaviors, demonstrating sophisticated object composition:

```javascript
//#include include/takeable.js    // Can be picked up
//#include include/food.js        // Can be eaten
//#include include/stackable.js   // Can be stacked

var parent_classes = ["apple", "food", "takeable"];
```

**Design Benefits for Game Development**:
- **Reusability**: Behaviors shared across many items reduces code duplication
- **Flexibility**: Mix and match capabilities to create unique item types
- **Maintainability**: Change behavior in one place affects all items using it
- **Extensibility**: Easy to add new behaviors without modifying existing items
- **Testing**: Individual behaviors can be tested independently

**Modern Applications**:
- Unity's component system follows similar composition patterns
- Entity-Component-System (ECS) architectures use these principles
- React's composition model mirrors this approach
- Modern game engines implement similar behavior mixing

**Why This Approach Succeeded**:
With 1,288+ items, traditional inheritance would have created unmaintainable hierarchies. The component approach allowed Glitch to create rich, varied item behaviors while keeping the codebase manageable.

### Data-Driven Configuration

The game uses extensive data files for content, demonstrating how to separate content from code:

```javascript
// Items can reference complex data structures
var stackmax = item_defs['stackmax']['apple'] || 1;
var recipes = recipe_defs['cooking']['advanced'];
```

**Advantages for Production Games**:
- **Artist-Friendly**: Non-programmers can edit content without touching code
- **Environment Separation**: Different data for dev/prod enables safe testing
- **Rapid Iteration**: Content changes without code deploys reduces release cycles
- **Localization**: Easy to support multiple languages and regions
- **A/B Testing**: Different configurations can be tested with different player groups

**Modern Relevance**:
This approach is now standard in game development and applies to:
- JSON-based configuration systems
- Content management systems for games
- Feature flags and gradual rollouts
- Microservice configuration management

**Client-Server Benefits**:
- Server and client can share the same data definitions
- Content updates can be pushed to clients without full rebuilds
- Configuration changes can be made without server restarts

## 🧭 Common Patterns & Conventions

### Error Handling

```javascript
// Pattern: Graceful degradation
function safeOperation(pc, args) {
    try {
        return performComplexOperation(pc, args);
    } catch (e) {
        log.error("Operation failed for " + pc.tsid + ": " + e);
        return false;
    }
}
```

### Permission Checking

```javascript
// Pattern: Layered permission system
if (pc.is_god) {
    // Administrative functions
} else if (pc.hasSkill('cooking', 5)) {
    // Advanced cooking
} else if (pc.location.allows('cooking')) {
    // Basic cooking
}
```

### State Management

```javascript
// Pattern: Explicit state transitions
function startCooking(pc, recipe) {
    pc.setState('cooking', {
        recipe: recipe,
        start_time: getTime(),
        ingredients: recipe.requirements
    });
}
```

## 🔧 Development Tools & Techniques

### Code Search Strategies

```bash
# Find all functions in a file
grep "^function" main.js

# Discover configuration options
grep -r "config\." . | grep -v ".git"

# Find error handling patterns
grep -r "log.error\|log.warn" .

# Locate admin/god mode features
grep -r "is_god" .

# Find skill-related code
grep -r "skill" . | grep -v ".git" | head -20
```

### Understanding Data Flows

Trace how data moves through the system:

1. **Player Input**: Messages from client
2. **Validation**: Permission and sanity checks
3. **Processing**: Core game logic execution
4. **State Updates**: Database and memory changes
5. **Response**: Feedback to client
6. **Side Effects**: Notifications, achievements, etc.

### Performance Analysis

Look for performance patterns in the code:

```bash
# Find caching mechanisms
grep -r "cache\|memoize" .

# Locate optimization patterns  
grep -r "lazy\|defer" .

# Find batch operations
grep -r "bulk\|batch" .
```

## 📚 Learning Opportunities

### For Game Developers

**MMO Architecture Lessons:**
- **Message-driven server design** - *Essential for any real-time multiplayer game*
- **Player state management at scale** - *How to handle thousands of concurrent players*
- **Content delivery and caching strategies** - *Efficient asset and data distribution*
- **Anti-cheat and security measures** - *Server-side validation and protection patterns*

**Game Design Patterns:**
- **Achievement system design** - *Player motivation and long-term engagement techniques*
- **Quest scripting and narrative flow** - *Delivering story content in interactive environments*
- **Item interaction and verb systems** - *Creating rich, meaningful object interactions*
- **Player progression mechanics** - *Balancing challenge, reward, and advancement*

**Modern Applications:**
- Adapt message-driven architecture for Node.js or cloud-native games
- Apply item behavior patterns to Unity, Unreal, or web-based games
- Use achievement system designs in mobile games or modern MMOs
- Implement quest scripting systems for narrative-driven games

### For Software Engineers

**JavaScript Architecture at Scale:**
- **Large-scale JavaScript application structure** - *Pre-framework patterns that still apply*
- **Module organization and dependency management** - *File inclusion and namespace management*
- **Configuration management across environments** - *Deployment and environment-specific settings*
- **Error handling in distributed systems** - *Graceful degradation and fault tolerance*

**System Design Principles:**
- **Event-driven architecture patterns** - *Message processing and system decoupling*
- **Data modeling for complex domains** - *Game world representation and relationships*
- **API design for real-time systems** - *Client-server communication protocols*
- **Performance optimization techniques** - *Caching, batching, and efficiency patterns*

**Modern Relevance:**
- Message-driven patterns apply to microservices and event streaming
- Component-based design principles are core to modern frameworks
- Configuration management techniques scale to cloud deployments
- Real-time communication patterns apply to WebSocket applications

### For Researchers

**Social Systems Research:**
- **Player interaction mechanics** - *How game design influences social behavior*
- **Community formation patterns** - *Virtual world social dynamics and group formation*
- **Virtual economy design** - *Economic modeling and market simulation*
- **Communication system implementation** - *Chat, messaging, and social interaction platforms*

**Virtual World Design Studies:**
- **Geography and space organization** - *Spatial design and player movement patterns*
- **NPC behavior and AI systems** - *Automated character behavior and world simulation*
- **Environmental storytelling** - *Narrative delivery through world design*
- **Player agency and choice systems** - *Decision-making and consequence modeling*

**Data and Insights:**
This codebase represents years of production data and player feedback, making it valuable for:
- Player behavior analysis and modeling
- Virtual community research
- Economic system studies
- Human-computer interaction research

## 🎯 Specific Study Areas

### Item System Deep Dive

Focus on these files for understanding item systems:
```
items/include/food.js           # Food behavior base class
items/include/takeable.js       # Basic item interactions
items/awesome_pot.js            # Complex cooking tool
items/furniture_chair.js        # Furniture placement system
```

### Achievement Engineering

Study progression system design:
```
achievements/1star_cuisinartist.js    # Simple skill achievement
achievements/million_currant_trophy.js # Collection achievement  
achievements/amazing_race.js          # Event achievement
```

### Quest Scripting

Learn narrative implementation:
```
quests/tutorial_cooking.js      # Teaching game mechanics
quests/animal_friend.js         # Character relationship
quests/amazing_race.js          # Event-based quest
```

### Administrative Systems

Understand game operations:
```
admin.js                        # Administrative functions
utils.js                        # External API integration
config_prod.js                  # Production configuration
```

## 🚨 Common Pitfalls & Tips

### Understanding the Environment

**Remember the Context:**
- This ran on Rhino, not Node.js or browsers
- Custom native functions (`api*`) handled database access
- No standard library - everything is custom

**File Organization:**
- `#include` statements create dependencies
- Files aren't modules - they're concatenated
- Global namespace is shared across all files

### Code Reading Tips

**Start Small:**
- Begin with simple items or achievements
- Understand the patterns before tackling complex systems
- Use grep to find examples of specific features

**Follow the Data:**
- Trace how data flows through the system
- Understand the relationship between files
- Look for configuration and data definitions

**Context is Key:**
- Functions often depend on global state
- Player context (`pc`) is central to most operations
- Understanding the game helps understand the code

## 🎮 Putting It Together

To truly understand this codebase and apply its lessons:

1. **Pick a Feature**: Choose something specific (like cooking) that interests you
2. **Find the Entry Point**: Locate where it starts (item verbs, messages) in the server
3. **Trace the Flow**: Follow the code path step by step through the system
4. **Understand the Data**: See how information is stored and processed
5. **Find the Patterns**: Look for similar implementations elsewhere in the codebase
6. **Consider the Design**: Think about why decisions were made this way
7. **Connect to Client**: Understand how the [Flash client](https://github.com/ap0ught/glitch-client) would interact with this system

### Practical Application Examples

**Building a Modern Multiplayer Game**:
- Use the message-driven architecture with WebSockets in Node.js
- Adapt the component-based item system for Unity or Unreal Engine
- Apply the achievement system patterns to mobile game progression
- Implement similar quest scripting for narrative-driven games

**Creating Real-Time Web Applications**:
- Apply message processing patterns to collaborative editing tools
- Use state management techniques for live document editing
- Implement similar permission systems for multi-user applications
- Adapt the configuration management for microservice deployments

**Designing Learning Platforms**:
- Use achievement system patterns for skill tracking and motivation
- Apply quest structures to educational content delivery
- Implement progression systems that guide learner advancement
- Create social features that encourage community learning

### Understanding the Complete Ecosystem

The Glitch server makes the most sense when understood alongside the client:

**Server Responsibilities** (This Repository):
- Authoritative game state management
- Player action validation and processing
- Content and progression system implementation
- Social interaction and communication handling

**Client Responsibilities** ([Glitch Client](https://github.com/ap0ught/glitch-client)):
- User interface and visual presentation
- Player input handling and client-side prediction
- Asset loading and rendering optimization
- Local state management for responsiveness

**Shared Responsibilities**:
- Data synchronization and consistency
- Asset and content coordination
- Communication protocol implementation
- Error handling and recovery

The Glitch codebase represents years of iterative development on a live MMO serving thousands of players. Every system evolved to meet real player needs and operational demands, making it an invaluable resource for understanding practical game development challenges and battle-tested solutions.

Whether you're building your own game, studying software architecture, researching virtual communities, or just satisfying curiosity about how Glitch worked, this codebase offers lessons that extend far beyond its original MMO context. The patterns, architectures, and design decisions demonstrated here apply to modern multiplayer games, real-time web applications, social platforms, and any system that needs to coordinate state across multiple users in real-time.