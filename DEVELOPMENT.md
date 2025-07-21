# Development Guide

This guide helps developers explore, understand, and learn from the Glitch server codebase. Whether you're studying MMO architecture, game design patterns, or just trying to understand how your favorite game worked, this document will help you navigate the code effectively.

## 🚀 Getting Started

### Prerequisites

The Glitch server was designed to run on:
- **Rhino JavaScript Engine** (Mozilla's Java-based JavaScript runtime)
- **Java Virtual Machine** (for hosting Rhino)
- **Custom Native APIs** (for database access, networking, etc.)

While you can't run this server directly without the original infrastructure, you can:
- Analyze the code structure and patterns
- Extract reusable components and ideas  
- Study the game design implementations
- Learn from the architecture decisions

### Repository Navigation

Start exploring with these key entry points:

```bash
# Core server logic
main.js                 # Message processing and game loop
common.js              # Shared utilities and functions
utils.js               # External integrations and helpers

# Configuration system  
config_base.js         # Shared settings
config_dev.js          # Development environment
config_prod.js         # Production environment

# Game content
items/                 # 1,288+ item definitions
achievements/          # 665 achievement implementations  
quests/                # 448+ quest scripts
```

## 🔍 Code Exploration Strategies

### Understanding the Message Flow

Start by tracing how player actions are processed:

1. **Entry Point**: `processMessage(pc, msg)` in `main.js`
2. **Message Types**: Switch statement handling different action types
3. **Player Context**: How `pc` object maintains player state
4. **Post-Processing**: Cleanup and secondary effects

```javascript
// Example: Tracing item interaction
case 'itemstack_verb': return doVerb(pc, msg);
```

### Exploring Item Systems

Items are the core of Glitch's gameplay. To understand them:

1. **Start with Simple Items**: Look at `items/apple.js` or `items/banana.js`
2. **Understand Inheritance**: Check `items/include/` for base classes
3. **Complex Examples**: Examine cooking tools or furniture
4. **Data Relationships**: See how items reference recipes, skills, etc.

```bash
# Find all food items
grep -r "food" items/ | grep "parent_classes"

# Discover item verbs (actions)
grep -r "verbs" items/ | head -20

# Find items with special properties
grep -r "has_info.*true" items/
```

### Achievement Analysis

Achievements show how progression systems work:

```bash
# Find cooking achievements
ls achievements/*cuisine* achievements/*cook*

# See achievement conditions  
grep -r "conditions" achievements/ | head -10

# Check reward systems
grep -r "rewards" achievements/ | head -10
```

### Quest System Deep Dive

Quests demonstrate narrative and progression design:

```bash
# Find tutorial quests
ls quests/*tutorial* quests/*newbie*

# Look for conversation systems
grep -r "conversation" quests/

# Find quest requirements
grep -r "require" quests/
```

## 🏗️ Architecture Patterns

### Message-Driven Architecture

The server uses event-driven processing for all player actions:

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

**Key Benefits:**
- **Scalability**: Each message can be processed independently
- **Debugging**: Clear trace of all player actions
- **Security**: All actions validated server-side
- **Extensibility**: Easy to add new message types

### Component-Based Item System

Items use mixin-style inheritance for behaviors:

```javascript
//#include include/takeable.js    // Can be picked up
//#include include/food.js        // Can be eaten
//#include include/stackable.js   // Can be stacked

var parent_classes = ["apple", "food", "takeable"];
```

**Design Benefits:**
- **Reusability**: Behaviors shared across many items
- **Flexibility**: Mix and match capabilities
- **Maintainability**: Change behavior in one place
- **Extensibility**: Easy to add new behaviors

### Data-Driven Configuration

The game uses extensive data files for content:

```javascript
// Items can reference complex data structures
var stackmax = item_defs['stackmax']['apple'] || 1;
var recipes = recipe_defs['cooking']['advanced'];
```

**Advantages:**
- **Artist-Friendly**: Non-programmers can edit content
- **Environment Separation**: Different data for dev/prod
- **Rapid Iteration**: Content changes without code deploys
- **Localization**: Easy to support multiple languages

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
- Message-driven server design
- Player state management at scale
- Content delivery and caching strategies
- Anti-cheat and security measures

**Game Design Patterns:**
- Achievement system design
- Quest scripting and narrative flow
- Item interaction and verb systems
- Player progression mechanics

### For Software Engineers

**JavaScript Architecture:**
- Large-scale JavaScript application structure
- Module organization and dependency management
- Configuration management across environments
- Error handling in distributed systems

**System Design:**
- Event-driven architecture patterns
- Data modeling for complex domains
- API design for game systems
- Performance optimization techniques

### For Researchers

**Social Systems:**
- Player interaction mechanics
- Community formation patterns
- Virtual economy design
- Communication system implementation

**Virtual World Design:**
- Geography and space organization
- NPC behavior and AI systems
- Environmental storytelling
- Player agency and choice systems

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

To truly understand this codebase:

1. **Pick a Feature**: Choose something specific (like cooking)
2. **Find the Entry Point**: Locate where it starts (item verbs, messages)
3. **Trace the Flow**: Follow the code path step by step
4. **Understand the Data**: See how information is stored and processed
5. **Find the Patterns**: Look for similar implementations elsewhere
6. **Consider the Design**: Think about why decisions were made this way

The Glitch codebase represents years of iterative development on a live MMO. Every system evolved to meet real player needs, making it an invaluable resource for understanding practical game development challenges and solutions.

Whether you're building your own game, studying software architecture, or just satisfying curiosity about how Glitch worked, take your time exploring - there's always more to discover in this remarkably complete implementation of a virtual world.