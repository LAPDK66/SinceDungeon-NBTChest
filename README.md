# SinceDungeon-NBTChest

An addon for the [SinceDungeon](https://github.com/SinceRPG/SinceDungeon/) plugin that adds two powerful custom dungeon actions:

- **`NBT_LOOT_CHEST`** — loot chests that preserve **full item NBT** (enchantments, attributes, custom model data, persistent data) and support random preset pools.
- **`PARALLEL`** — run any number of dungeon sub-actions **simultaneously**, completing only when **all** of them finish.

Both actions register themselves through SinceDungeon's public API, so they appear in the dungeon editor alongside the built-in actions.

---

## Features

-  **Full-NBT loot chests** — items are serialized with Paper's `ItemStack#serializeAsBytes()`, so nothing is lost.
-  **Random preset pools** — list multiple presets and one is chosen at random.
-  **Per-player or shared mode** — each player can draw their own random loot, or everyone shares one chest.
-  **Optional key requirement** — gate chests behind a required dungeon key ID.
- ️ **Time limits & penalties** — supported per action.
-  **PARALLEL action** — combine `SPAWN_WAVE`, `NBT_LOOT_CHEST`, and any other registered action into a single parallel objective.
-  **In-game editors** — visual GUIs for both loot presets and parallel presets, no YAML editing required.

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Server      | Paper `1.21+` |
| Java        | `21` |
| SinceDungeon| Installed and loaded **before** this addon |

---

## Installation

1. Download the latest `SinceDungeon-NBTChest-x.x.x.jar`.
2. Make sure **SinceDungeon** is already installed in your `plugins/` folder.
3. Drop this addon's JAR into `plugins/`.
4. Restart the server.

On startup you should see:

```text
[SinceDungeon-NBTChest] SinceDungeon-NBTChest enabled. Actions: NBT_LOOT_CHEST, PARALLEL
```

---

## Commands

| Command | Description |
|---------|-------------|
| `/sdnbt create <name>` | Create a new loot preset (opens the editor GUI) |
| `/sdnbt edit <name>`   | Edit an existing loot preset |
| `/sdnbt list`          | List all loot presets |
| `/sdnbt delete <name>` | Delete a loot preset |
| `/sdnbt info <name>`   | Show details about a preset |
| `/sdnbt reload`        | Reload `presets.yml` |
| `/lapparallel`         | Open the Parallel preset editor |
| `/lapparallel reload`  | Reload `parallel_presets.yml` |

### Permission

| Node | Default | Grants |
|------|---------|--------|
| `sincedungeon.nbtchest.admin` | `op` | Access to all `/sdnbt` and `/lapparallel` commands |

---

## Creating a Loot Preset

1. Run `/sdnbt create epic_chest`.
2. A 54-slot editor opens — place any items you want (with full NBT) freely.
3. **Close the inventory** to save. Empty editors are not saved.

Presets are stored in:

```plain text
plugins/SinceDungeon-NBTChest/presets.yml
```


---

## Using `NBT_LOOT_CHEST` in a Dungeon

Add it to your dungeon YAML like any other action.

**Single preset (backwards-compatible):**

```yaml
type: "NBT_LOOT_CHEST"
preset: "epic_chest"
location: "0,64,0"
```


**Random preset pool:**

```yaml
type: "NBT_LOOT_CHEST"
presets: "epic_chest,rare_chest,common_chest"
location: "0,64,0"
per_player: true        # each player draws independently
required_key: "NONE"    # or a key ID
```


### `NBT_LOOT_CHEST` parameters

| Key | Default | Description |
|-----|---------|-------------|
| `location` | `0,64,0` | Chest location as `x,y,z` (dungeon-relative) |
| `presets` | `[my_preset]` | One preset name per line; one is picked at random |
| `preset` | — | Single preset (alternative to `presets`) |
| `per_player` | `true` | `true`: each player draws independently · `false`: shared chest |
| `required_key` | `NONE` | Key ID required to open, or `NONE` |
| `time_limit` | `-1` | Seconds before time penalty (`-1` = no limit) |
| `time_penalty` | `1` | Points deducted per second overtime |

> **Note:** Players must take **all** items before the chest counts as completed.

---

## Using the `PARALLEL` Action

The `PARALLEL` action runs every sub-action of a **parallel preset** at the same time and completes when all of them finish.

### 1. Build a parallel preset

```plain text
/lapparallel
```


- Create a preset, then add sub-actions (e.g. `SPAWN_WAVE`, `NBT_LOOT_CHEST`).
- Each sub-action's fields can be edited in the GUI.
- `PARALLEL` itself cannot be nested (recursion is blocked).

Presets are stored in:

```plain text
plugins/SinceDungeon-NBTChest/parallel_presets.yml
```


### 2. Reference it in a dungeon

```yaml
challenge:
  type: "PARALLEL"
  preset: "wave_and_loot"
  start_message: "<red>Survive the wave AND loot the chest!"
```


### `PARALLEL` parameters

| Key | Default | Description |
|-----|---------|-------------|
| `preset` | `""` | Name of the parallel preset to run |
| `time_limit` | `-1` | Seconds before time penalty (`-1` = no limit) |
| `time_penalty` | `1` | Points deducted per second overtime |

---


## License

MIT-license.

## Author

- **LAPDK66**