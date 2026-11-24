# The Doom folder

`play.html` is loaded in an iframe on `doom.html`. It needs these files next to it:

| File | Where it comes from |
|---|---|
| `websockets-doom.js` | built from doom-wasm (see below) |
| `websockets-doom.wasm` | built from doom-wasm |
| `default.cfg` | `src/default.cfg` in the doom-wasm repo |
| `freedoom2.wad` | the Freedoom download from https://freedoom.github.io/ |
| `COPYING` | Freedoom's license file, from the same download |
| `map.wad` | your own map, renamed to `map.wad` |

All of these are in place. The engine files were built on 2026-11-24 with Emscripten 7.0.1, and the WAD is Freedoom 0.13.0.

## Rebuilding doom-wasm

You only need this if you want to rebuild the engine. The upstream `./scripts/build.sh` no longer works with current Emscripten, so it needs four fixes:

```sh
brew install emscripten automake sdl2 sdl2_mixer sdl2_net
git clone https://github.com/cloudflare/doom-wasm.git
cd doom-wasm
```

1. In `configure.ac`, replace the `EMFLAGS=` line (line 118). The old settings were removed from Emscripten, and Freedoom's 28 MB WAD needs more memory:
   ```
   EMFLAGS="-s INVOKE_RUN=1 -s USE_SDL=2 -s USE_SDL_MIXER=2 -s USE_SDL_NET=2 -s ASSERTIONS=0 -s WASM=1 -s ALLOW_MEMORY_GROWTH=1 -s FORCE_FILESYSTEM=1 -s EXPORTED_RUNTIME_METHODS=[[FS,ccall,callMain]] -s EXIT_RUNTIME=1 -s PROXY_POSIX_SOCKETS=0 -s INITIAL_MEMORY=128MB -s STACK_SIZE=5MB -s ERROR_ON_UNDEFINED_SYMBOLS=0 -s ASYNCIFY -O3"
   ```
2. On line 29 of `configure.ac`, remove `-g` from `CFLAGS="-O$OPT_LEVEL -g ..."`. Without it, the `.wasm` is 2 MB instead of 7.6 MB.
3. In `src/doomtype.h`, make `boolean` always the enum in C. Without this, files that include `<stdbool.h>` first (the SDL headers do) get a 1-byte `bool`, while the rest get a 4-byte enum. Globals like `netgame` and `playeringame` then overwrite each other, and the game crashes with "Player 4 start missing". Change the condition `#if defined(__cplusplus) || defined(__bool_true_false_are_defined)` to `#if defined(__cplusplus)`, and add these two lines just before `typedef enum`:
   ```c
   #undef true
   #undef false
   ```
4. Build as C11, because the code doesn't compile as C23:
   ```sh
   ./scripts/clean.sh
   emconfigure autoreconf -fiv
   CFLAGS="-O3 -std=gnu11" ac_cv_exeext=".html" emconfigure ./configure --host=none-none-none
   emmake make -j8
   cp src/websockets-doom.js src/websockets-doom.wasm /path/to/retro-site/doom/
   ```

## Map requirements

- The map must be in the **MAP01** slot.
- It must use the vanilla **"Doom 2 (Doom format)"** format, not Boom, MBF21 or UDMF. Chocolate Doom only runs vanilla maps.
- To start on a different map slot, change `"-warp", "1"` in `play.html`.
- To swap in a new version of the map, replace `map.wad`.
- A Player 1 start is enough for single player.

## Controls

`default.cfg` comes from doom-wasm and uses WASD, not Doom's original arrow keys:

- **W/S** move, **A/D** strafe
- **Mouse** or **O/P** turn
- **Left click** or **Space** fire
- **E** use
- **Shift** run
- **Tab** map
- **F** fullscreen

The table on `doom.html` matches this. If you change the keys in `default.cfg`, update that table too.

## Licenses

- The engine is GPL v2. Because we host a compiled build, the credits on `doom.html` link to the source.
- Freedoom is BSD-style. Keep `COPYING` next to the WAD.
