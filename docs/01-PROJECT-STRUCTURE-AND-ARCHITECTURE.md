# 1. Project Structure & Architecture: Foundation for Scalability

## Overview

Your game codebase needs structure from day one. A well-organized codebase saves 100+ hours of debugging, refactoring, and searching for where things live. This section covers folder structure, subsystem design, and architectural patterns.

---

## 1.1 Recommended Folder Structure

```
game03/
├── CMakeLists.txt                 # Build config
├── src/                           # Engine + Game code
│   ├── engine/
│   │   ├── core/
│   │   │   ├── game_loop.cpp
│   │   │   └── time_manager.cpp
│   │   ├── render/
│   │   │   ├── renderer.cpp
│   │   │   ├── camera.cpp
│   │   │   └── animation.cpp
│   │   ├── input/
│   │   │   └── input_manager.cpp
│   │   ├── audio/
│   │   │   └── audio_manager.cpp
│   │   ├── resource/
│   │   │   ├── texture_manager.cpp
│   │   │   ├── sound_manager.cpp
│   │   │   └── font_manager.cpp
│   │   ├── physics/
│   │   │   └── collision.cpp
│   │   ├── entity/
│   │   │   ├── entity.cpp
│   │   │   └── world.cpp
│   │   ├── ui/
│   │   │   └── ui_manager.cpp
│   │   ├── save/
│   │   │   └── save_system.cpp
│   │   └── effects/
│   │       ├── particle_system.cpp
│   │       └── trails.cpp
│   ├── game/
│   │   ├── player/
│   │   │   └── player.cpp
│   │   ├── enemies/
│   │   │   ├── enemy.cpp
│   │   │   └── ai_system.cpp
│   │   ├── world/
│   │   │   ├── world_manager.cpp
│   │   │   ├── map_loader.cpp
│   │   │   └── area.cpp
│   │   ├── items/
│   │   │   ├── item.cpp
│   │   │   └── inventory.cpp
│   │   ├── npcs/
│   │   │   ├── npc.cpp
│   │   │   └── dialog_system.cpp
│   │   └── quests/
│   │       └── quest_system.cpp
│   └── game03.cpp                 # Main entry point
├── include/                       # Header files (mirror structure above)
├── assets/
│   ├── sprites/
│   │   ├── global/
│   │   │   ├── ui/
│   │   │   ├── effects/
│   │   │   └── items/
│   │   ├── areas/
│   │   │   ├── area1/
│   │   │   │   ├── tiles/
│   │   │   │   ├── enemies/
│   │   │   │   ├── npcs/
│   │   │   │   └── objects/
│   │   │   ├── area2/
│   │   │   └── ...
│   │   └── player/
│   │       └── player_animations/
│   ├── maps/
│   │   ├── area1/
│   │   │   ├── room1.json
│   │   │   ├── room2.json
│   │   │   └── dungeon1.json
│   │   ├── area2/
│   │   └── ...
│   ├── audio/
│   │   ├── music/
│   │   │   ├── ambient/
│   │   │   ├── exploration/
│   │   │   ├── combat/
│   │   │   └── boss/
│   │   └── sfx/
│   │       ├── player/
│   │       ├── enemies/
│   │       ├── items/
│   │       └── ui/
│   ├── fonts/
│   └── data/
│       ├── items.json
│       ├── enemies.json
│       ├── npcs.json
│       ├── quests.json
│       ├── abilities.json
│       └── world_data.json
├── tools/
│   └── map_editor_exporters/
└── build/
```

---

## 1.2 Why This Structure?

### **By Feature Type** (e.g., `/engine/render/`, `/engine/audio/`)
- **Pro:** Easy to find and reuse code across projects.
- **Con:** Large projects become complex; hard to find zone-specific data.

### **By Zone/Area** (e.g., `/assets/sprites/area1/`, `/assets/music/dungeon/`)
- **Pro:** Zone-specific assets grouped; quick lookup for a level.
- **Con:** Need to manage duplicates or shared assets carefully.

### **Hybrid (Recommended for Your Game)**
- `/engine/` for reusable systems (renderer, input, audio manager).
- `/game/` for game logic (player, NPCs, quests).
- `/assets/` organized by **type AND zone** for large projects.

---

## 1.3 Modularity & Subsystems

### **Core Subsystems You Need**

Each subsystem is a separate module with clear responsibility:

1. **Rendering System** — Textures, sprites, layers, animations, post-processing.
2. **Audio System** — Music, SFX, ambience, dynamic layering.
3. **Input System** — Keyboard, controller, remapping, accessibility.
4. **Resource System** — Manages textures, sounds, fonts (no leaks!).
5. **Physics/Collision** — Hitbox checks, slopes, pushback.
6. **Entity System** — World, entities, components, systems (ECS-like).
7. **UI/Menu System** — Menus, HUD, dialog, inventory screens.
8. **Save System** — Serialization, checkpoints, auto-save.
9. **Camera System** — Follow, shake, pan, zoom, effects.
10. **Effect System** — Particles, trails, screen effects.
11. **World Manager** — Map loading, area transitions, persistence.
12. **AI System** — Enemy behavior, pathfinding, state machines.

### **Singleton Pattern for Managers**

Each manager (TextureManager, AudioManager, etc.) should exist **once globally**.

```cpp
// texturemanager.h
class TextureManager {
private:
    TextureManager() = default;
    TextureManager(const TextureManager&) = delete;
    TextureManager& operator=(const TextureManager&) = delete;
    
    std::unordered_map<std::string, SDL_Texture*> textures;
    
public:
    static TextureManager& getInstance() {
        static TextureManager instance;
        return instance;
    }
    
    SDL_Texture* load(SDL_Renderer* r, const std::string& id, const std::string& path);
    SDL_Texture* get(const std::string& id);
    void unload(const std::string& id);
    void unloadAll();
};
```

**Usage anywhere in code:**
```cpp
TextureManager& texMgr = TextureManager::getInstance();
SDL_Texture* tex = texMgr.load(renderer, "player_idle", "assets/sprites/player/idle.png");
```

---

## 1.4 Separation of Concerns

### **Engine Code**
- Should be **game-agnostic**: renderer, input, audio, physics, save system.
- Reusable for other projects.
- No hardcoded references to player, enemies, or game-specific logic.

### **Game Code**
- Player logic, NPCs, quests, world rules.
- Uses engine subsystems via clean interfaces.
- Game-specific constants, mechanics, balance.

### **Never Do This**
```cpp
// BAD: Game logic in engine code
class Renderer {
    void render() {
        if (player.isAlive()) { // ← Game logic in engine!
            drawPlayer();
        }
    }
};
```

### **Do This Instead**
```cpp
// GOOD: Engine is agnostic
class Renderer {
    void render(const std::vector<Entity*>& entities) {
        for (auto e : entities) {
            drawEntity(e);
        }
    }
};

// Game code uses renderer
void gameLoop() {
    renderer.render(world.getAllEntities());
}
```

---

## 1.5 Dependency Management

### **Dependency Inversion Principle**

High-level code (game logic) should not depend on low-level code (rendering). Both should depend on **abstractions**.

**Example:**
```cpp
// BAD: Player depends on Renderer directly
class Player {
    Renderer* renderer; // Tightly coupled
    void draw() { renderer->drawSprite(...); }
};

// GOOD: Player depends on abstract interface
class IRenderer {
public:
    virtual void drawSprite(const Sprite&) = 0;
};

class Player {
    IRenderer* renderer; // Loosely coupled
    void draw() { renderer->drawSprite(sprite); }
};
```

---

## 1.6 Configuration & Constants

### **Never Hardcode Game Values**

```cpp
// BAD
float playerSpeed = 100.0f; // Magic number

// GOOD
namespace Config {
    namespace Player {
        constexpr float SPEED = 100.0f;
        constexpr float JUMP_FORCE = 200.0f;
        constexpr int MAX_LEVEL = 100;
        constexpr int MAX_LEVEL_SECRET = 500;
    }
    namespace Camera {
        constexpr float SHAKE_INTENSITY = 5.0f;
        constexpr float DASH_LAG = 0.1f;
    }
    namespace Game {
        constexpr float DAY_LENGTH_SECONDS = 1200.0f; // 20 min real-time
        constexpr int DIFFICULTY_NORMAL = 0;
        constexpr int DIFFICULTY_HARD = 1;
        constexpr int DIFFICULTY_HARDEST = 2;
    }
}
```

Or **load from config file** (JSON/YAML):
```json
{
  "player": {
    "speed": 100.0,
    "jumpForce": 200.0,
    "maxLevel": 100
  },
  "game": {
    "dayLengthSeconds": 1200.0,
    "checkpointRespawnDelay": 2.0
  }
}
```

---

## 1.7 File Naming Conventions

- **Headers:** `.h` (or `.hpp` for C++11+)
- **Source:** `.cpp`
- **Naming:** `snake_case.cpp` or `PascalCase.cpp` (be consistent!)
- **Classes:** `PascalCase`
- **Functions/methods:** `camelCase` or `snake_case`
- **Constants:** `UPPER_SNAKE_CASE`
- **File = Class:** `TextureManager.h/cpp` contains `class TextureManager`

---

## 1.8 Build System (CMake)

Keep `CMakeLists.txt` clean and modular:

```cmake
cmake_minimum_required(VERSION 3.16)
project(game03 VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find dependencies
find_package(SDL3 REQUIRED)
find_package(SDL3_image REQUIRED)

# Engine sources
set(ENGINE_SOURCES
    src/engine/core/game_loop.cpp
    src/engine/render/renderer.cpp
    src/engine/input/input_manager.cpp
    # ... more
)

# Game sources
set(GAME_SOURCES
    src/game/player/player.cpp
    src/game/enemies/enemy.cpp
    # ... more
)

# Create executable
add_executable(game03
    src/game03.cpp
    ${ENGINE_SOURCES}
    ${GAME_SOURCES}
)

# Include directories
target_include_directories(game03 PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/include"
)

# Link libraries
target_link_libraries(game03 PRIVATE
    SDL3::SDL3
    SDL3_image::SDL3_image
)
```

---

## 1.9 Common Pitfalls

### **Pitfall 1: God Class**
**Problem:** One massive `Game` class doing everything.  
**Solution:** Break into subsystems (Renderer, Audio, Input, Physics, etc.).

### **Pitfall 2: Circular Dependencies**
**Problem:** `A` includes `B`, and `B` includes `A`.  
**Solution:** Use forward declarations or dependency injection.

```cpp
// BAD
// player.h
#include "world.h"
class Player { World* world; };

// world.h
#include "player.h"
class World { Player* player; }; // Circular!

// GOOD: Forward declare + pass as parameter
// world.h
class Player; // Forward declare
class World { void addEntity(Player* p); };

// player.h
class World; // Forward declare
class Player { World* world; };
```

### **Pitfall 3: Over-Engineering**
**Problem:** Building "for future needs" before you have them.  
**Solution:** Start simple; refactor when you feel pain.

### **Pitfall 4: Global State Everywhere**
**Problem:** Every class accessing global `Game::instance()` calls.  
**Solution:** Pass references or use dependency injection.

---

## 1.10 Refactoring Strategy

As your game grows, you'll want to refactor. Here's a safe approach:

1. **Write tests** (or manual test cases).
2. **Identify pain point** (slow load, buggy system, hard to extend).
3. **Plan new structure** (sketch on paper).
4. **Refactor incrementally** (small changes, test often).
5. **Document changes** (update architecture notes).

---

## Summary

- Organize by subsystem type in `/engine/`, game logic in `/game/`, assets by type **and** zone.
- Use singletons for global managers (TextureManager, AudioManager, etc.).
- Separate engine (reusable) from game (specific) code.
- No hardcoded magic numbers; use config files or constants.
- Start simple; refactor ruthlessly as you grow.

---

**Next:** Resource Management (how to load, cache, unload assets safely).