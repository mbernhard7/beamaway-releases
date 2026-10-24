# Beamaway releases

Downloads for [Beamaway](https://beamaway.milestomorrow.com), and the source for the
libraries it ships. The files are attached to this repository's
[Releases](https://github.com/mbernhard7/beamaway-releases/releases).

## VLCKit without GPL code

Beamaway plays a tuner's raw broadcast stream with [VLCKit](https://code.videolan.org/videolan/VLCKit),
licensed under the GNU Lesser General Public License, version 2.1 or later. The
`vlckit-*` releases hold the build Beamaway uses (macOS, iOS and tvOS, devices and
Simulators) and everything needed to rebuild or modify it:

| File | What it is |
|---|---|
| `VLCKit-4.0-beamaway-lgpl-<commit>.zip` | The xcframework Beamaway links |
| `vlckit-<commit>-source.tar.gz` | VLCKit at its release commit, including its patches to VLC |
| `vlc-<commit>-source.tar.gz` | VLC at the base commit VLCKit pins |
| `vlc-contrib-sources.tar` | Every third-party library source archive the build fetched |
| `build-vlckit.sh` | The build script |
| `VLCKit.json` | Pinned commits, checksums and patch list |
| `SHA256SUMS` | Checksums of the files above |

It differs from VideoLAN's own VLCKit 4.0 build in two ways: zvbi is turned off (its
VPS/PDC code is GPL-2 only), and VLC modules that declare a GPL license are left out
(the file and syslog loggers, the dummy interface, SAP discovery, the cycle and RTP
stream outputs, and the rotate filter).

To rebuild, on a Mac with Xcode and GNU Make 4.4 (`brew install make`):

```sh
mkdir work && tar -xf vlc-contrib-sources.tar -C work      # optional: skips downloads
BEAMAWAY_VLC_ARCHIVE=$PWD/vlc-<commit>-source.tar.gz \
BEAMAWAY_VLC_TARBALLS=$PWD/work/tarballs \
  x/tools/build-vlckit.sh "$PWD/work" mac     # then ios, tvos
x/tools/build-vlckit.sh "$PWD/work" package   # one xcframework and zip
```

`build-vlckit.sh` expects to sit in a `tools` folder with `VLCKit.json` in a sibling
`dependencies` folder (`x/tools/build-vlckit.sh`, `x/dependencies/VLCKit.json`), as in
Beamaway's source. To relink Beamaway with a modified VLCKit, replace `VLCKit.framework` in the
app's `Frameworks` folder (it is a dynamic library) and re-sign the app.
