# tools_C_harlock

A collection of C utility modules written between 2012 and 2016: dynamic
arrays, a counted string type, key/value config loading, buffered file I/O,
a character-grid canvas and an RGB image canvas, plus two small games built
on top of them (Conway's game of life and a tic-tac-toe with a basic AI).

This is an archive snapshot of student-era work, published for reference.
It does not build as-is on a current toolchain -- see Status below.

## Modules

| Module | LOC | Purpose |
|---|---|---|
| `tools` | 2224 | Shared base: memory helpers, search/sort, `regstr` C-string helpers, math |
| `hcharpix` | 876 | Character-grid canvas with a cursor, for terminal rendering |
| `intarray` | 831 | Growable `int` array |
| `himage` | 826 | RGB image buffer (`imgcolor` pixels), drawing and file output |
| `char_key_tools` | 807 | Character tables and keyboard input handling |
| `array` | 791 | Growable `void*` array, doubles as a heap |
| `lifegame` | 662 | Conway's game of life, rendered through `hcharpix` |
| `jstr` | 615 | Counted string type, substring search, comparison |
| `huintarray` | 545 | Growable `unsigned int` array |
| `mfile` | 525 | Buffered file reader/writer over raw `open`/`read` |
| `stringarray` | 513 | Growable array of `jstr` |
| `module_morpion` | 232 | Tic-tac-toe grid, scoring and AI move selection |
| `KVdata` | 113 | `key = value` config file loader |

Everything is handle-based: each module exposes an opaque pointer typedef
(`array`, `jstr`, `hcharpix`, ...) with `_create` / `_destroy` pairs.
`tools_config.h` holds the width typedefs (`nint`, `unint`) used to keep the
code working on both 32- and 64-bit targets.

## Status

Do not expect `make` to work. Verified against gcc 16.2.1:

- Every module fails on `typedef unsigned int bool` (`tools.h:17`,
  `intarray.h:23`). `bool` became a reserved keyword in C23, which modern gcc
  defaults to. Building with `-std=gnu11` or older is mandatory.
- With `-std=gnu11`, 4 of 13 modules compile clean: `intarray`, `jstr`,
  `hcharpix`, `himage`. The other 9 have genuine pre-existing errors
  (implicit declarations, conflicting prototypes, a stray backslash in
  `char_key_tools.c:203`).
- Three headers the sources include are absent from this repository:
  `jfile.h` and `btree.h` (required by the `jstr` and `intarray` makefile
  rules) and `hunintarray.h` (included by `huintarray.c`, where the filename
  is also misspelled).
- The makefile defines `OBJS` and per-object rules but has no link target,
  and its `clean` rule expands an undefined `$(ALL)`.

So the repository is an incomplete snapshot: it was cut from a larger working
tree (the `tools0_50` collection referenced by the sibling `game_of_life`
project) and the missing pieces were never committed.

To compile the four that work:

```sh
gcc -std=gnu11 -c -W -Wall intarray.c jstr.c hcharpix.c himage.c
```

## Attribution

Authorship here is mixed and worth stating plainly:

- `tools.c` / `tools.h` carry the notice *"TOOLS, a part of the Cfuns
  modules. (C) Jacques-Olivier Lapeyre 2012-2016"*. That module is his work,
  not mine.
- `jstr.h` and `stringarray.h` are marked *"algorithme facon Jacque Olivier
  Lapeyre"* -- my implementations of algorithms taught by him. The functions
  carrying his method keep a `_jol` suffix.
- `hcharpix`, `himage`, `lifegame`, `char_key_tools` and `module_morpion` are
  mine.

## License

None. No license file was ever added, and the repository contains
third-party copyrighted code (see Attribution), so its status is unresolved
and it should be treated as all-rights-reserved rather than reusable.
