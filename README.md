# Unreal Engine 3D Action / Platformer Game Prototype

A 3D action-platformer and shooting prototype developed in **Unreal Engine 5**. This project features character movement, item collection mechanics, enemy AI shooting behavior, level transitions, and persistent game state tracking.

---

## 📌 Project Overview & Key Features

* **Persistent Game State Management:** Utilizes a custom Game Instance (`BP_Dave3DGI`) to track player lives, collectible values (coins, crowns, diamonds), and level key pickup states across map transitions.
* **Health & Damage System:** Standardized interface/event-driven damage pipeline (`Event Get Hurt`, `ProcessDeath`) with centralized life subtraction and clamped limits.
* **Game Over & Respawn Flow:** Handled gracefully with UI focus modes, mouse cursor toggle controls, level reload, and full state reset mechanisms upon restart.
* **Key & Lock Progression:** Level-specific key collection and door unlock logic supporting multi-level navigation across 4 distinct levels.
* **Enemy AI & Combat:** Line-of-sight targeting, socket-based bullet spawning, dynamic timer management, and garbage-collection-safe enemy firing logic.
* **Dynamic Camera System:** Trigger-based camera offsets that adjust the player's perspective during combat encounters and reset seamlessly upon defeating waves.

---

## ⏱️ Duration to Build

* **Total Development Time:** ~3 Weeks (Iterative Prototyping, Logic Blueprinting, & Refactoring)
  * **Core Mechanics & State Management:** 1 Week
  * **Enemy AI & Combat Mechanics:** 1 Week
  * **UI, Level Transitions & Bug Fixing:** 1 Week

---

## 💡 Technical Challenges & Solutions Faced

### 1. Side Camera View Transition (`Follow Camera` Offset Reset)
* **The Problem:** Shifting the `Follow Camera` relative offset during combat encounters caused immediate snapping issues. In early implementations, hooking camera reset logic to the `Completed` pin of a `For Each Loop` caused the camera to snap back to `(0, 0, 0)` in a single frame because the spawning loop completes instantly before gameplay even begins.
* **The Solution:** Decoupled the camera reset from the spawn loop. Implemented a wave-tracking mechanism (`EnemiesRemaining`) on `BP_BotSpawn`. Each spawned enemy receives a direct `Self` reference to its spawner (`Expose on Spawn`). When an enemy dies, it calls `On Enemy Killed`, decrementing the count and triggering the camera reset to `(0, 0, 0)` only when all wave enemies are eliminated.

### 2. Character Movement & Input Control in UI Transitions
* **The Problem:** Transitioning to the Game Over screen caused mouse pointer visibility bugs and input leakage (character attempting to move or jump while interacting with menu buttons). On restart, the mouse cursor remained visible during active gameplay.
* **The Solution:** Implemented explicit input mode switching using `Set Input Mode UI Only` + `Set Show Mouse Cursor (True)` when entering the Game Over state. Reverted back to `Set Input Mode Game Only` + `Set Show Mouse Cursor (False)` inside the restart button event handler before triggering map reloads.

### 3. Pick Item Reset & Cross-Level State Logic
* **The Problem:** Using a single global boolean (`IsKeyPicked`) in the Game Instance caused key state bleed across levels—picking up a key in Level 1 left the flag `True` upon entering Level 2, allowing doors to open automatically without collecting the new key. Furthermore, restart attempts failed to reset player live counts back to `3`.
* **The Solution:** Restructured state clearing by forcing `BP_Door` to consume the key (`SET Is Key Pick = False`) immediately upon unlocking the exit portal prior to calling `Open Level`. Additionally, standardized a centralized `ResetGameData` routine inside `BP_Dave3DGI` to restore lives to `3`, clear score variables, and wipe key states whenever a fresh game run is initiated.

---

## 📚 References & Resources

* **Unreal Engine Documentation:** [Unreal Engine Blueprint API Reference](https://docs.unrealengine.com/5.0/en-US/blueprint-api/)
* **Unreal Engine Core Concepts:**
  * Game Instance & Persistent Data Storage
  * Blueprint Class Interfaces (`BPI_Damageable`)
  * AI Controller & Pawn Sensing / Line of Sight
  * Delegated Events & Timer Handles (`Set Timer by Event`)
* **Community & Tutorials:**
  * Unreal Engine Official Community Forums & Learning Portal
  * Unreal Engine YouTube Tutorials on UI Input Modes & Game Instance Management

---

## 📂 Project Structure

```
Content/
 ├── Blueprints/
 │    ├── Characters/
 │    │    ├── BP_Character
 │    │    └── AI/
 │    │         ├── BP_EnemyAI
 │    │         └── BP_RifleEnemy
 │    ├── Environment/
 │    │    ├── BP_Key
 │    │    ├── BP_Door
 │    │    └── BP_BotSpawn
 │    └── Core/
 │         └── BP_Dave3DGI (Game Instance)
 └── UI/
      ├── WBP_HUD
      └── WBP_GameOver
```

---

## 🚀 How to Run

1. Clone or download this repository.
2. Open the project file (`.uproject`) in **Unreal Engine 5.0** or higher.
3. Open `Content/Maps/Level_1` (or your initial starting level).
4. Press **Play in Editor (PIE)**.
