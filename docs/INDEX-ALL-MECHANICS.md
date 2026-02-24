# Complete Game Mechanics Index & Implementation Guide

This document lists **every mechanic** from your game vision and references how to implement each.

---

## **CORE GAMEPLAY MECHANICS**

### 1. **Real-Time Action Combat**
**What it is:** Player fights enemies in real-time (not turn-based).  
**Why:** Feels responsive, skill-based, arcade-like.  
**Implementation approach:**
- Player state machine: Idle, Walk, Attack, Hurt, Dead.
- On attack: check collision with enemies in range.
- On hit: apply damage, knockback, screen shake, particle effect.
- Enemy state machine mirrors player.
- Use frames or time windows for attack hitboxes (not always on).

**Data-driven:**
```json
{
  "attack": {
    "duration": 0.5,
    "hitStartFrame": 3,
    "hitEndFrame": 8,
    "damage": 10,
    "knockback": 50.0,
    "cameraShakeIntensity": 5.0
  }
}
```

**Pitfall:** Every frame checking collision = slow. Use "attack windows" (frames 3-8 only).

---

### 2. **Difficulty Settings**
**What it is:** Normal, Hard, Hardest modes.  
**Data approach:**
```cpp
enum Difficulty { NORMAL = 0, HARD = 1, HARDEST = 2 };

struct DifficultyModifier {
    float enemyHealthMultiplier;
    float enemyDamageMultiplier;
    float playerHealthMultiplier;
    bool autoRestoreHealth; // Only on Normal
};

// In enemy spawn
float enemyHealth = baseHealth * difficultyModifier[currentDifficulty].enemyHealthMultiplier;
```

---

### 3. **Level System (1-100, Secret 500)**
**Implementation:**
```cpp
struct PlayerStats {
    int level = 1;
    int experiencePoints = 0;
    int maxLevel = 100;
    bool secretUnlocked = false;
    
    void gainXP(int amount) {
        experiencePoints += amount;
        while (experiencePoints >= xpToNextLevel) {
            levelUp();
        }
    }
    
    void levelUp() {
        if (level < maxLevel) {
            level++;
            // Distribute stat points, heal, etc.
        }
    }
};

// Secret unlock: find special item
void findSecretItem() {
    player.secretUnlocked = true;
    player.maxLevel = 500;
    difficulty = HARDEST;
}
```

---

### 4. **Ability Unlocks**
**What it is:** Player starts with walk