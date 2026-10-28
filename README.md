# Herit Aware: downloads

Download builds of **Herit Aware**, the serious game of the European project of the same name.
Official website: <https://herit-aware.eu>

| Platform | Download |
|---|---|
| Windows 10/11 (64-bit) | [HeritAware-Windows.zip](https://github.com/tackYon22/herit-aware-downloads/releases/latest/download/HeritAware-Windows.zip) |
| macOS 12+ (Apple silicon and Intel) | [HeritAware-Mac.pkg](https://github.com/tackYon22/herit-aware-downloads/releases/latest/download/HeritAware-Mac.pkg) |

All versions: [Releases](https://github.com/tackYon22/herit-aware-downloads/releases)

## Installation

**Windows:** right-click the `.zip` > *Extract All…*, open the `Herit Aware` folder and run `Herit Aware.exe`.
If SmartScreen shows "Windows protected your PC", click *More info* > *Run anyway*.

**macOS:** double-click the `.pkg` and follow the installer. The game is installed in *Applications*.

---

## For maintainers: publishing a new version

The website links to `releases/latest/download/<file name>`. As long as every release
uses **the same file names**, the website never needs to be edited: the links always
serve the most recent release, and the download block on the site reads the version
number and file sizes from the GitHub API.

Fixed file names (never change them):

- `HeritAware-Windows.zip`
- `HeritAware-Mac.pkg`

### 1. Prepare the files

```bash
# macOS: copy the signed + notarized installer under the fixed name
cp "Herit Aware X.Y.Z.pkg" HeritAware-Mac.pkg

# Windows: zip the build folder (the .exe, the _Data folder, the DLLs...)
# with a single "Herit Aware" folder at the root of the zip
cp -R "<Windows build folder>" "Herit Aware"
zip -r -X HeritAware-Windows.zip "Herit Aware" -x "*.DS_Store"
```

Do not ship the `*_BurstDebugInformation_DoNotShip` or `*_BackUpThisFolder_ButDontShipItWithYourGame` folders.

Quick check before uploading:

```bash
pkgutil --check-signature HeritAware-Mac.pkg   # must say "Notarization: trusted"
unzip -l HeritAware-Windows.zip | head         # must show "Herit Aware/Herit Aware.exe"
```

### 2. Create the release

```bash
gh release create vX.Y.Z HeritAware-Mac.pkg HeritAware-Windows.zip \
  --repo tackYon22/herit-aware-downloads \
  --title "Herit Aware vX.Y.Z" \
  --notes "What changed in this version." \
  --latest
```

GitHub limit: 2 GB per file.

### 3. Check

```bash
curl -sIL https://github.com/tackYon22/herit-aware-downloads/releases/latest/download/HeritAware-Mac.pkg | grep -i -E "^location|content-length"
curl -sIL https://github.com/tackYon22/herit-aware-downloads/releases/latest/download/HeritAware-Windows.zip | grep -i -E "^location|content-length"
```

The `location` lines must point to the new tag `vX.Y.Z`.

### Fixing a file in an existing release

```bash
gh release upload vX.Y.Z HeritAware-Windows.zip --clobber --repo tackYon22/herit-aware-downloads
```

### Moving the website

The links do not depend on the web host. When the site moves (e.g. to a static site),
copy the download block as it is: it is plain HTML/CSS with a small optional script.
