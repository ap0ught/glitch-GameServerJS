# Copilot Instructions for Glitch Game Server

## Repository Overview

This is the **Glitch Game Server** - a Node.js-based MMO server that powered the Glitch virtual world. It's a comprehensive game backend that demonstrates enterprise-scale JavaScript architecture patterns from a production MMO.

### Key Context for AI Assistance

**Purpose**: Educational and research repository showcasing MMO server architecture
**Scale**: 1,288+ items, 665 achievements, 448+ quests, 280+ locations
**Client**: Works with Flash-based client ([glitch-client](https://github.com/ap0ught/glitch-client))
**Architecture**: Event-driven, message-based communication with authoritative server design

## Codebase Architecture

### Core Systems
- **Items** (`items/` directory): Complex interactive objects with verbs, states, and behaviors
- **Quests** (`quests/` directory): Branching narrative system with prerequisites and rewards  
- **Achievements** (`achievements/` directory): Player progression tracking and unlockables
- **Locations** (`locations/` directory): Game world areas with interactive elements
- **Players** (`players/` directory): User management and character data

### Key Files
- `main.js` - Primary server entry point and core game loop
- `common.js` - Shared utilities and helper functions
- `config_*.js` - Environment-specific configurations (dev/prod)
- `admin.js` - Administrative commands and debugging tools
- `inc_data_*.js` - Large data files for game content (items, maps, etc.)

## Coding Patterns & Conventions

### Message-Driven Architecture
```javascript
// Client-server communication pattern
case 'itemstack_verb':
    // 1. Validate request from client
    // 2. Execute authoritative server logic
    // 3. Update game state
    // 4. Broadcast changes to relevant clients
```

### Item System Pattern
```javascript
// Items are complex objects with:
item.verbs = {}; // Available player actions
item.state = {}; // Current item state  
item.events = {}; // Event handlers
item.onVerb = function(verb, player) { /* logic */ };
```

### Configuration Pattern
- Separate dev/prod configurations
- Large data files for game content
- Environment-aware loading

### Event System
- Heavy use of callbacks and event handlers
- Asynchronous operations with proper error handling
- State management through object properties

## Development Guidelines

### When Suggesting Code Changes
1. **Preserve existing patterns** - Follow established conventions in similar files
2. **Maintain backwards compatibility** - Don't break existing item/quest/achievement definitions
3. **Consider scale** - This system handled thousands of concurrent players
4. **Think about client impact** - Changes may affect Flash client communication

### Common Tasks
- **Adding new items**: Follow patterns in `items/` directory
- **Creating quests**: Use quest scripting patterns from `quests/`
- **Modifying achievements**: Maintain progression logic consistency
- **Server configuration**: Update appropriate `config_*.js` files

### Code Style
- Use existing JavaScript ES5 patterns (original era codebase)
- Maintain consistent indentation and naming conventions
- Preserve comment styles and documentation patterns
- Keep large data structures in separate `inc_data_*.js` files

## Context for Modern Development

### Learning Opportunities
- **MMO Architecture**: Authoritative server with client prediction
- **Scalable JavaScript**: Large-scale Node.js application patterns
- **Game Systems**: Complex item interactions and player progression
- **Data Management**: Handling large game world datasets

### Modern Applications
- WebSocket-based multiplayer games
- Real-time collaborative applications  
- Component-based game engines
- Event-driven microservices

## Important Notes

### Historical Context
- Originally built for production MMO with 10,000+ concurrent users
- Represents mature JavaScript patterns from early Node.js era
- Battle-tested systems for virtual world management

### Educational Value
- Study material for game server architecture
- Examples of complex JavaScript application structure
- Real-world patterns for handling player interactions
- Production-ready error handling and state management

### Research Applications
- Virtual world design analysis
- Player behavior tracking systems
- MMO scalability patterns
- Social interaction mechanics

## Working with This Codebase

When providing assistance:
1. **Understand the game context** - This is a social MMO with crafting, exploration, and quests
2. **Respect the architecture** - Don't suggest major architectural changes
3. **Consider the client relationship** - Server changes must be compatible with Flash client
4. **Think about multiplayer** - All changes should account for concurrent users
5. **Preserve educational value** - Maintain code clarity for learning purposes

This repository serves as both a historical artifact and a learning resource for modern game development. Any suggestions should balance historical preservation with educational clarity.