# cuesplit

Split CD image rips (a cue sheet plus one FLAC or APE file) into one tagged file per
track: FLAC, Apple Lossless or AAC.

- **Any cue sheet:** reads cue sheets in Japanese (Shift-JIS, EUC-JP), Chinese (GBK,
  Big5), Korean, Cyrillic and Western encodings as well as Unicode, and detects which
  one a sheet uses. No more garbled titles.
- **Keeps your metadata:** tags come from the cue sheet and the image file, including
  foobar2000-style per-track tags, with the cover art.
- **Checks its work:** source files are checked against their MD5s, and lossless output
  is verified against the original audio. AAC files play gaplessly.
- **Fast:** several tracks are encoded at once.

Runs on macOS 14 or later, on Apple silicon and Intel Macs.

## Install

With [Homebrew](https://brew.sh):

```sh
brew install galaco/tap/cuesplit
```

This also installs the man page (`man cuesplit`) and completions for zsh, bash and fish.

Or download `cuesplit-<version>-macos.zip` from [Releases](../../releases), unzip it,
and put `cuesplit` somewhere on your `PATH`. It is signed and notarized by Apple.

## Use

```sh
cuesplit album.cue                     # FLAC tracks in "Artist - Album" next to the cue sheet
cuesplit album.cue -o ~/Music --alac   # Apple Lossless, in ~/Music/Artist - Album
cuesplit */*.cue --aac -b 256          # several albums as 256 kbps AAC
cuesplit album.cue --tracks 3,5        # only some tracks
cuesplit album.cue --dry-run           # list the files it would save
cuesplit info album.cue                # show the tracks and tags without splitting
```

The input can also be a FLAC or APE file with an embedded cue sheet.

| Option | |
|---|---|
| `--flac`, `--alac`, `--aac` | Output format. FLAC is the default; Apple Lossless and AAC are `.m4a` files that play in Music. |
| `-b`, `--bitrate <kbps>` | AAC bit rate: 128, 160, 192, 224, 256 (the default, Apple Music's) or 320. |
| `-o`, `--output <folder>` | Where to save. Defaults to each cue sheet's folder. |
| `--no-album-folder` | Save straight into the output folder instead of an "Artist - Album" folder. |
| `--tracks <list>` | Only these tracks, e.g. `3` or `1,4-6`. |
| `--overwrite` | Replace existing files. Without it, cuesplit refuses to overwrite anything. |
| `--encoding <name>` | The cue sheet's text encoding, if the guess is wrong (`cuesplit encodings` lists them). |
| `--gaps`, `--hidden-track` | Where pregaps and audio before track 1 go. |
| `--jobs <n>` | Tracks to encode at once. |

`cuesplit help split` lists every option.

### In scripts

The paths of saved files go to standard output, one per line; progress and warnings go
to standard error. `-q` hides progress.

`--json` prints the results instead:

```json
{
  "albums" : [
    {
      "error" : null,
      "files" : [
        "/Music/FELT - Icicle fall/01 - Set.flac",
        "/Music/FELT - Icicle fall/02 - Hail Storm.flac"
      ],
      "input" : "album.cue",
      "output" : "/Music/FELT - Icicle fall/",
      "warnings" : []
    }
  ]
}
```

Every field is always present: `error` is null for an album that succeeded, and
`output` is null if cuesplit couldn't read the album.

`cuesplit info album.cue --json` describes the album: the encoding it detected, the
source files, and every track with its start, length, file name and tags.

Exit status: 0 on success, 1 if any album failed (the others are still split), 64 for
invalid arguments.

## Feedback

Report bugs and ideas in [Issues](../../issues). For a cue sheet that cuesplit reads
wrongly, please attach the `.cue` file.

## License

MIT; see [LICENSE](LICENSE). cuesplit includes libFLAC and the Monkey's Audio SDK
(both BSD-3-Clause) and Swift Argument Parser (Apache 2.0); their notices are in
`THIRD_PARTY_NOTICES.txt` in each release.
