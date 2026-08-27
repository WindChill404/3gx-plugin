# Kirby: Planet Robobot Archipelago plugin (3DS)

A memory bridge for playing the Kirby: Planet Robobot Archipelago randomizer on
real 3DS hardware.

This is **LittleCube's ALBW plugin with one line changed.** All credit for the
plugin itself belongs to [LittleCube](https://github.com/LittleCube-hax/albw-ap-plugin).

---

## Why this fork exists

The plugin holds no game logic. It listens on UDP port 45987 and answers read
and write requests for the running game's memory, and that is the whole of it:

```c
u8 read8(u32 addr)              { return MEM(u8, addr); }
void write8(u32 addr, u8 value) { MEM(u8, addr) = value; }
```

Nothing in the source refers to A Link Between Worlds. What ties it to that game
is a single line of metadata:

```
Targets: 00040000000EC300
```

A 3GX plugin declares which titles it is for, and it will not load correctly
under a title it does not name. Pointing that line at Planet Robobot is the
entire change.

Trying to sidestep it by installing the ALBW build as `default.3gx` does not
work: that is Luma's load-under-every-title slot, so the plugin gets loaded into
a game it never declared and faults during startup. A crash dump from exactly
that situation showed a data abort inside the plugin's own memory region, before
the plugin's `main()` ever ran.

## What changed

Only `CTRPluginFramework.plgInfo`:

```diff
-Targets: 00040000000EC300
+Targets: 00040000001BB800, 0004000000183600
```

Both IDs are listed because two have been seen in the wild for this game.
**Confirm your own before trusting either** (see below). Listing an ID you don't
have is harmless; missing the one you do have means the plugin never loads.

No source file is modified.

## Finding your title ID

- **3DS:** open FBI, browse to the title, and read the ID.
- **Azahar:** right-click the game, Properties, read the Program ID. It is also
  printed near the top of `azahar_log.txt`.

If yours is neither of the two above, add it to the `Targets` line and rebuild.

## Building

You do not need a local toolchain. Push the repo to GitHub and the included
workflow builds it: **Actions -> Build plugin -> run -> download `plugin-3gx`.**

To build locally you need devkitARM, libctru and CTRPluginFramework installed,
then:

```sh
export DEVKITARM=$DEVKITPRO/devkitARM
export CTRPFLIB=$DEVKITPRO/libctrpf
make
```

## Installing

1. Copy the built `.3gx` to `/luma/plugins/<YOUR_TITLE_ID>/plugin.3gx`.
   Use the title-specific folder and the name `plugin.3gx`, **not**
   `default.3gx`.
2. Boot holding **Select** for the Luma configuration screen:
   - `Enable loading external FIRMs and modules` must be **off**.
   - `Enable game patching` must be **on**.
   - Save and exit.
3. Press **L + D-Pad Down + Select** for the Rosalina menu and set
   `Plugin loader` to `[Enabled]`.
4. Launch the game. A blue flash at the end of the boot animation means the
   plugin loaded.
5. The screen shows the console's IP. Run `/3ds <that address>` in the
   Archipelago client.

## If it still crashes

The plugin already detects one known Luma incompatibility and reports it rather
than crashing:

```
This version of Luma3DS does not work with this plugin.
```

If you get that message, the plugin is fine and your Luma version is the issue.
If you get a hard crash with no message instead, it died before `main()`, which
points at the load itself. In that case:

- Check your title ID is really in the `Targets` line.
- Try lowering `MemorySize` in the plgInfo. It asks for 5 MiB of private memory,
  and Planet Robobot is a heavier game than ALBW; if that request cannot be met,
  the plugin faults at startup.
- Luma writes a dump to `sd:/luma/dumps/`. That file identifies the fault
  precisely and is worth attaching to any bug report.

## Credits

- **[LittleCube](https://github.com/LittleCube-hax)** wrote this plugin. This
  fork only retargets it.
- **[Nanquitas](https://github.com/Nanquitas/CTRPluginFramework)** for
  CTRPluginFramework.
