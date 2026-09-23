# The Magic School Bus Explores the Human Body — Static Recompilation

Static recompilation of **The Magic School Bus Explores the Human Body**
(Microsoft / Scholastic, 1994) from its shipping Win16 binaries to native C.

Built on the [pcrecomp](https://github.com/sp00nznet/pcrecomp) toolchain, and
directly on [bob](https://github.com/sp00nznet/bob) — same publisher, same
year, same NE/Win16 shape.

## Project Status: **P0 complete, P1 not started**

Reconnaissance only. Nothing has been lifted, and nothing builds or runs yet.
What is here is the P0 write-up and `analysis/catalog.txt`.

---

## What P0 found

The whole title is **18 binaries and about 60 KB of code**, and that is not a
mistake in the count.

| Binary | Size | Module | Role |
|--------|-----:|--------|------|
| `MSBHUMAN/0/BD2.EXE` | 14,960 | `FEEDER` | |
| `MSBHUMAN/0/BD7.EXE` | 23,040 | `MSBSNOOP` | |
| `MSBHUMAN/0/BD9.EXE` | 7,008 | `GOBAND` | |
| `BANDDLL.DLL` | 5,216 | `BANDDLL` | the "band" engine |
| `MIDIDLL.DLL` | 4,368 | `MIDIDLL` | |
| `SCHED.DLL` | 8,704 | `sched` | |
| `SETUP.EXE` | 167,744 | `SETUP` | installer, not interesting |

All NE (16-bit Windows), Microsoft linker 5.10/5.50. Alongside them sit ~180
numbered directories of `.PAG`, `.MSF`, `.PFL`, `.DIB` and `.WAV` — one per
scene.

**This is a data-driven title with an almost non-existent code surface.** The
ratio is roughly 60 KB of machine code to 190 MB of content. Compare Microsoft
Bob, the nearest neighbour already in the collection: same publisher, same
year, same NE format, but the code there is the product. Here the code is an
interpreter and the product is in the `.MSF` files.

That inverts the normal work split. Lifting 60 KB of 16-bit NE is a short job
for a toolchain that has already lifted El-Fish's 121 segments and 2,236
functions. Working out what `.MSF` and `.PFL` mean is the project.

`BAND.INI` names the engine:

```ini
; Kids Setup  for  'Magic School Bus Explores The Human Body'
[init]
main = !d:\msbhuman\
[KidsCat]
path=shared\stuff\kidscat.exe
```

`KIDSCAT/` is a separate Visual Basic 3 catalogue app (it ships `VBRUN300.DLL`)
and is advertising, not game. Skip it — and see `worldempire/` for why a
VBRUN300 import means a different toolchain entirely.

`DRIVERS/` holds four S3/SVGA DOS drivers. Not ours.

## Where it goes next (P1)

1. `tools/ne/ne_parse.py` → `ne_decode.py` → `ne_xref.py` over the three
   `BD*.EXE` and `BANDDLL.DLL`. Six segments total; this should be a short run.
   Lifting goes through `tools/lift/ne_lift.py` and links against pcrecomp's
   `runtime/win16/`, which is upstream now.
2. Work out what `GOBAND`, `FEEDER` and `MSBSNOOP` each are. The module names
   suggest a loader, a content feeder and a scene player, which would mean the
   engine is smaller still than 60 KB and the rest is per-scene glue.
3. `.MSF` is the format to crack first. Nothing plays until it is readable.
   `tools/formats/` already holds the Encarta 97 decoders (FIF, M20/MVB, SPAM,
   DAT) and is the right place for whatever this turns out to be.

## Layout

```
msbus/
  original/          HUMANBODY.zip
  original/ex/       extracted disc tree
  analysis/
  docs/
```

## Credits

The Magic School Bus Explores the Human Body © 1994 Microsoft Corporation /
Scholastic Inc. This project neither contains nor distributes any part of it.

The code and documentation here are MIT; [LICENSE](LICENSE) spells out that
the grant stops at our own work and does not reach the game or anything
lifted from it.
