# Compile CWSL_DIGI experimental-ft2 with JS8Call 2.3

## 1. Update the repo

```powershell
cd C:\src\CWSL_DIGI
git fetch origin experimental-ft2
git checkout experimental-ft2
git reset --hard origin/experimental-ft2
git apply --ignore-space-change --ignore-whitespace experimental-ft2.patch
git apply --ignore-space-change --ignore-whitespace js8-2.3.patch
```

If a patch says already applied, continue.

Confirm JS8 2.3 parser is in the tree:

```powershell
Select-String "DecodedText decodedtext" source\OutputHandler.cpp
```

You want `{ frame, bits, submode }`, not `FN03ng`.

## 2. JS8Call 2.3.1 sources (build-time)

```powershell
if (Test-Path C:\js8call_source) { Remove-Item -Recurse -Force C:\js8call_source }
git clone --branch v2.3.1 --depth 1 https://github.com/js8call/js8call.git C:\js8call_source

# vcxproj compiles ..\..\js8call_source from source\
if (-not (Test-Path C:\src\js8call_source)) {
  cmd /c mklink /J C:\src\js8call_source C:\js8call_source
}
```

## 3. Visual Studio project settings (Release | x64)

**C/C++ → Additional Include Directories** must contain:

- `C:\js8call_source`
- your Qt 5 include dir (project default `C:\Qt\5.14.2\msvc2017_64\include`)
- your Boost include dir (`C:\boost_1_70_0`)

**Linker → Additional Library Directories:**

- Qt lib dir
- Boost `stage\lib`
- `wsjt_fort.lib` folder if you have it

**Linker → Additional Dependencies:**

- `Qt5Core.lib`
- `wsjt_fort.lib`

Keep `decodedtext.cpp`, `jsc.cpp`, `varicode.cpp` **included** in the project for this try.

## 4. Build

Open `source\CWSL_DIGI.vcxproj`

Configuration: **Release**  Platform: **x64**

Build → Rebuild Solution

## 5. If JS8 2.3 sources will not compile under Qt 5

Exclude from build:

- decodedtext.cpp
- jsc.cpp
- varicode.cpp

FT2/FT8/FT4 will still build. JS8 support will not.

## 6. Runtime JS8 (after a successful build)

Install JS8Call 2.3.1 and set in config.ini:

```ini
[js8call]
binpath=C:\Program Files\JS8Call\bin
```
