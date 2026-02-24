# Implementation Roadmap & Checklist

## Phase 1: Engine Foundation (Weeks 1-3)
- [ ] Project structure created (folders, CMake)
- [ ] Basic game loop (update, render, input)
- [ ] TextureManager singleton
- [ ] AudioManager singleton
- [ ] Basic renderer (draw sprites, layers)
- [ ] Input manager (keyboard, controller, remapping)
- [ ] Camera follow (simple, then with lerp)

## Phase 2: Core Systems (Weeks 4-7)
- [ ] Entity/Component system (basic ECS)
- [ ] World/Area manager (load maps from TileSetter JSON)
- [ ] Player character (movement, animation states)
- [ ] Save/load system (GameState, JSON serialization)
- [ ] Checkpoint system (respawn on death)
- [ ] Collision detection (AABB)
- [ ] Camera effects (shake, lag, pan)

## Phase 3: Game Feel & Polish (Weeks 8-10)
- [ ] Particle system (dust, effects)
- [ ] Trail system (snow/mud footprints)
- [ ] Screen effects (fade, dissolve)
- [ ] UI manager (menus, HUD, pause screen)
- [ ] Animation system (frame timing, state transitions)
- [ ] Audio layers (music crossfading, ambience)

## Phase 4: Gameplay Mechanics (Weeks 11-16)
- [ ] Inventory system (weight-based, equipment slots)
- [ ] Item system (weapons, armor, consumables)
- [ ] Crafting system (recipe-based)
- [ ] Enemy AI (state machine, patrol, chase, attack)
- [ ] Boss system (multi-phase, unique attacks)
- [ ] Loot drops (currency, items, materials)
- [ ] Level/XP system (progression)

## Phase 5: World & Persistence (Weeks 17-20)
- [ ] World persistence (doors, enemies, items)
- [ ] NPC system (dialog, portraits)
- [ ] Quest system (objectives, rewards)
- [ ] Day/night cycle (time progression)
- [ ] Weather system (visual + gameplay effects)
- [ ] World respawning (lore-based, timed)

## Phase 6: Polish & Optimization (Weeks 21-24)
- [ ] Performance profiling (FPS, memory)
- [ ] Lower-end device optimization
- [ ] Accessibility (colorblind, text scaling, controls)
- [ ] Save system robustness (validation, recovery)
- [ ] Debug tools (FPS counter, collision viewer)
- [ ] Content population (sprites, music, dialogs)

---

## Key Files to Create

### Engine Files
- `engine/core/game_loop.h/cpp`
- `engine/resource/texture_manager.h/cpp`
- `engine/resource/audio_manager.h/cpp`
- `engine/input/input_manager.h/cpp`
- `engine/render/renderer.h/cpp`
- `engine/render/camera.h/cpp`
- `engine/entity/entity.h/cpp`
- `engine/entity/world.h/cpp`
- `engine/physics/collision.h/cpp`
- `engine/save/save_system.h/cpp`
- `engine/ui/ui_manager.h/cpp`
- `engine/effects/particle_system.h/cpp`

### Game Files
- `game/player/player.h/cpp`
- `game/enemies/enemy.h/cpp`
- `game/enemies/ai_system.h/cpp`
- `game/items/inventory.h/cpp`
- `game/items/item.h/cpp`
- `game/npcs/npc.h/cpp`
- `game/world/area.h/cpp`
- `game/world/map_loader.h/cpp`

---

## Testing Checklist

### Save System
- [ ] Save and load restores all player data
- [ ] Multiple save slots don't interfere
- [ ] Checkpoint respawn works
- [ ] Corrupted save doesn't crash game
- [ ] World persistence (enemies stay dead, items stay gone)

### Camera
- [ ] Follows player smoothly
- [ ] Doesn't pan outside map bounds
- [ ] Shake feels responsive (not nauseating)
- [ ] Dash lag is cinematic
- [ ] Pan and normal follow transition smoothly

### Performance
- [ ] 60 FPS on target hardware
- [ ] No memory leaks (check every frame)
- [ ] Loading screens reasonable (<1 sec)
- [ ] No stuttering when saving/loading

### Gameplay
- [ ] Player movement feels responsive
- [ ] Inventory doesn't overflow
- [ ] Enemies pathfind sensibly
- [ ] Quests complete properly
- [ ] UI is responsive to input

---

## Performance Targets

- **Target FPS:** 60 (capped)
- **Frame time:** ~16.67ms per frame
- **Memory budget:** <256MB (low-end); <512MB (target)
- **Load time:** <2 seconds per area
- **Save/load:** <1 second

---

## Data Format Examples

### Item Data (items.json)
```json
{
  "items": [
    {
      "id": "sword_iron",
      "name": "Iron Sword",
      "type": "weapon",
      "rarity": "common",
      "weight": 5.0,
      "damage": 15,
      "cost": 100
    }
  ]
}
```

### Enemy Data (enemies.json)
```json
{
  "enemies": [
    {
      "id": "goblin_basic",
      "name": "Goblin",
      "health": 20,
      "speed": 50.0,
      "drops": [
        {"item": "gold_coin", "chance": 0.5, "amount": 5}
      ]
    }
  ]
}
```

### Map Format (exported from TileSetter as JSON)
```json
{
  "width": 20,
  "height": 15,
  "tileSize": 32,
  "layers": [
    {
      "name": "ground",
      "tiles": [0, 1, 1, 2, ...]
    }
  ],
  "objects": [
    {
      "id": "enemy_1",
      "type": "goblin_basic",
      "x": 100,
      "y": 150
    }
  ]
}
```

---

## Documentation Template for Each System

When you implement a system, document:

1. **Purpose:** What does this system do?
2. **Dependencies:** What other systems does it need?
3. **Public Interface:** What methods/functions are public?
4. **Data Flow:** How does data move through the system?
5. **Initialization:** How/when does it start?
6. **Cleanup:** How/when does it shut down?
7. **Example Usage:** Code snippet showing typical use.

---

This roadmap is your guide. As you complete each phase, check it off and update this document with discoveries, pitfalls, and refactors!