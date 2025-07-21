# Glitch Game Server JavaScript Repository

> **The complete server-side codebase for Glitch, the beloved MMO that ran from 2009-2012**

This repository contains the entire server-side JavaScript codebase that powered [Glitch](http://glitchthegame.com), one of the most unique and creative MMOs ever created. Developed by Tiny Speck (who later went on to create Slack), Glitch was a browser-based multiplayer game featuring a whimsical world where players could explore, craft, socialize, and complete quests.

## 🌟 What Makes This Special

This isn't just any game server - it's a **complete MMO implementation** written entirely in JavaScript running on Rhino. The codebase represents:

- **677,660+ lines of game logic** across 1,288+ item definitions
- **665 achievements** with complex progression systems  
- **448 quests** with rich narrative content
- **Complete avatar customization** system with clothing, faces, and physics
- **Massive world geography** with interactive locations and street spirits
- **Complex economic systems** including imagination (currency), trading, and crafting
- **Social features** including groups, parties, and local chat
- **Administrative tools** for managing a live MMO

## 🏗️ Architecture Overview

The server is built around a **message-driven architecture** where all player actions are processed through `main.js`. Key components:

- **Message Processing**: All client interactions handled via `processMessage()` 
- **Item System**: 1,288+ interactive items with custom behaviors and verbs
- **Achievement Engine**: Complex progression tracking with 665 unique achievements
- **Quest System**: Story-driven content with 448+ quest implementations
- **Physics Engine**: Custom movement and world interaction systems
- **Configuration Management**: Environment-specific configs (dev/prod/test)

## 📁 Repository Structure

```
├── main.js                    # Core message processing and game loop
├── config_*.js               # Environment configurations
├── common.js                 # Shared utilities and core functions
├── utils.js                  # Helper functions and external integrations
├── admin.js                  # Administrative and god-mode functions
│
├── items/                    # 1,288+ item definitions and behaviors
│   ├── include/             # Shared item behavior classes
│   ├── food.js              # All food items with cooking mechanics
│   ├── tools.js             # Tools, equipment, and interactive objects
│   └── ...                  # Items ranging from simple consumables to complex machines
│
├── achievements/            # 665 achievement definitions with progression logic
├── quests/                  # 448+ quest implementations with narrative content
├── locations/               # Location-specific scripts and interactions
├── players/                 # Player-related functionality and data
├── groups/                  # Group and social system implementations
├── utils/                   # Additional utility functions and systems
│
└── inc_data_*.js           # Large data files for items, maps, avatars, skills
```

## 🎮 Key Game Systems

### Item System (1,288+ Items)
Every item in Glitch has rich behaviors defined in JavaScript:
- **Food items** with nutritional properties and cooking mechanics
- **Tools** with durability, efficiency ratings, and special abilities  
- **Furniture** for home customization with placement rules
- **Collectibles** including artifacts, trophies, and rare items
- **Interactive objects** like teleporters, machines, and magical items

### Achievement System (665 Achievements)
Complex progression tracking across multiple categories:
- **Skill-based achievements** for cooking, mining, farming, etc.
- **Collection achievements** for finding rare items and artifacts
- **Social achievements** for group activities and interactions
- **Exploration achievements** for discovering locations and secrets
- **Meta achievements** for completionist gameplay

### Quest System (448+ Quests)  
Rich narrative content with:
- **Story quests** that advance the main narrative
- **Side quests** with unique characters and rewards
- **Repeatable tasks** for ongoing gameplay
- **Seasonal events** and limited-time content
- **Tutorial quests** for new player onboarding

### Avatar & Customization
Complete character customization:
- **Facial features** with scaling and positioning controls
- **Clothing system** with multiple slots and visual layers
- **Color customization** for hair, skin, and clothing
- **Physics properties** affecting movement and interaction

## 🚀 Getting Started

This codebase was designed to run on **Rhino JavaScript engine** in a Java environment. While it can't be run directly in modern Node.js without significant modification, it's invaluable for:

### For Game Developers
- Study MMO architecture patterns and message handling
- Learn item system design and player progression mechanics  
- Understand quest scripting and narrative integration
- Explore social features and group management systems

### For Researchers & Students
- Analyze a complete commercial MMO implementation
- Study large-scale JavaScript application architecture
- Examine game economy and progression system design
- Research social interaction patterns in virtual worlds

### For Glitch Fans
- Discover hidden content and unreleased features
- Understand how your favorite game mechanics worked
- Find easter eggs and developer comments throughout the code
- Learn the exact requirements for rare achievements

## 💎 Hidden Gems & Features

This codebase is full of discoveries for those who dig deep:

- **Secret items and content** that were never publicly released
- **Developer tools and admin commands** for managing the live game
- **Complex AI systems** for NPCs and street spirits  
- **Sophisticated economics** with supply/demand and market dynamics
- **Weather and time systems** affecting gameplay
- **Detailed crafting recipes** with hundreds of combinations
- **Physics tweaks and experiments** for different gameplay modes
- **Rich dialogue systems** with branching conversations

## 🔍 Exploring the Code

Start with these key files to understand the system:

1. **`main.js`** - Core message processing and game loop
2. **`config_base.js`** - Game constants and shared settings  
3. **`items/include/`** - Base classes for item behaviors
4. **`achievements/`** - Browse achievement definitions to understand progression
5. **`quests/`** - Read quest scripts to see narrative implementation

Search for specific features:
```bash
# Find all cooking-related items
grep -r "cooking\|recipe" items/

# Discover achievement requirements  
grep -r "conditions" achievements/

# Explore quest dialogue
grep -r "conversation\|dialogue" quests/
```

## 📚 Additional Documentation

- **[ARCHITECTURE.md](ARCHITECTURE.md)** - Deep dive into technical architecture
- **[FEATURES.md](FEATURES.md)** - Comprehensive breakdown of all game systems  
- **[DEVELOPMENT.md](DEVELOPMENT.md)** - Developer guide for exploring and understanding the code

## 🎯 Use Cases

### Educational
- **Game Development Courses**: Real-world example of MMO architecture
- **Software Engineering**: Large-scale JavaScript application patterns
- **Game Design**: Player progression and engagement mechanics
- **Computer Science**: Distributed systems and message processing

### Research  
- **Virtual Worlds**: Social interaction and community formation
- **Game Economics**: In-game currency and trading systems
- **Player Behavior**: Progression psychology and achievement design
- **Narrative Design**: Interactive storytelling in games

### Development
- **MMO Frameworks**: Reference implementation for game servers
- **Item Systems**: Complex object behavior and interaction patterns
- **Achievement Systems**: Progression tracking and reward mechanisms  
- **Quest Engines**: Narrative scripting and story management

## 📄 License

All files are provided by Tiny Speck under the [Creative Commons CC0 1.0 Universal License](http://creativecommons.org/publicdomain/zero/1.0/legalcode). This is a broadly permissive "No Rights Reserved" license — you may do what you please with what we've provided.

**Important Notes:**
- All files are provided AS-IS without support
- The Glitch logo and trademark are NOT included in this license
- Only items explicitly included in these files are covered
- No obligation to credit, but links to [glitchthegame.com](http://glitchthegame.com) are appreciated

## 🤝 Contributing

Documentation contributions are welcome! If you figure something out that others could learn from:

1. Write up a clear explanation or how-to guide
2. Submit it as a pull request
3. Share your knowledge with the community

**Areas where contributions would be valuable:**
- Setup guides for running code analysis tools
- Detailed explanations of specific game systems
- Code organization and navigation guides
- Discovery of hidden features or easter eggs

---

*This repository represents one of the most complete and accessible MMO codebases ever released. Whether you're a developer, researcher, or just curious about how Glitch worked, there's something here for you to discover.*
