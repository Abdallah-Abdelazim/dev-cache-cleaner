# gradle-cache-cleaner

Shell scripts that reclaim disk space by removing cached Maven, Gradle and Xcode/SwiftPM dependencies and build artifacts.

- [`clean-gradle-cache`](#clean-gradle-cache) — Maven and Gradle caches
- [`clean-xcode-cache`](#clean-xcode-cache) — Xcode, SwiftPM and related caches

## clean-gradle-cache

### What it does

Scans two caches and deletes old versions of each artifact, keeping only the latest:

- **`~/.m2/repository`** — Maven local repository
- **`~/.gradle/caches/modules-2/files-2.1`** — Gradle module cache

SNAPSHOT versions are left untouched by default.

### Usage

```sh
# Delete old versions, keep latest (SNAPSHOTs untouched)
./clean-gradle-cache

# Delete everything — all versions, latest, and SNAPSHOTs
./clean-gradle-cache --all

# Preview without deleting (works with or without --all)
./clean-gradle-cache --dry-run
./clean-gradle-cache --all --dry-run
```

#### Example output

```
Scanning /Users/you/.m2/repository ...
  deleting  /Users/you/.m2/repository/com/squareup/okhttp3/okhttp/4.9.3  (1.2M)
  deleting  /Users/you/.m2/repository/com/squareup/okhttp3/okhttp/4.10.0  (1.3M)

Scanning /Users/you/.gradle/caches/modules-2/files-2.1 ...
  deleting  /Users/you/.gradle/caches/modules-2/files-2.1/org.jetbrains.kotlin/kotlin-stdlib/1.8.22  (3.1M)

Done — removed 3 old version directories.
```

### Requirements

- **bash** 4+
- **`sort -V`** — available natively on Linux and macOS 12+

## clean-xcode-cache

Deletes Xcode and SwiftPM caches. Quit Xcode first; the script warns if it is running.

### What it does

Default run removes:

- **`~/Library/Developer/Xcode/DerivedData`** — build products, indexes, module cache
- **`~/Library/Caches/org.swift.swiftpm`** and **`~/Library/org.swift.swiftpm`** — SwiftPM package caches
- **`~/Library/Caches/com.apple.dt.Xcode`** — Xcode general cache

`--all` additionally removes:

- **`~/Library/Caches/CocoaPods`** and **`~/Library/Caches/Carthage`**
- **`~/Library/Developer/Xcode/iOS DeviceSupport`** — device symbols, re-downloaded on next connect
- **`~/Library/Developer/Xcode/Archives`** — also deletes the dSYMs of shipped builds, which cannot be regenerated
- Unavailable simulators, via `xcrun simctl delete unavailable`

### Usage

```sh
# DerivedData, SwiftPM and Xcode caches
./clean-xcode-cache

# Also CocoaPods/Carthage caches, device support, archives, dead simulators
./clean-xcode-cache --all

# Preview without deleting (works with or without --all)
./clean-xcode-cache --dry-run
./clean-xcode-cache --all --dry-run
```

### Requirements

- **bash** 3.2+ (macOS default)
- **Xcode command line tools** (`xcrun`) for the simulator step under `--all`

## Installation

Clone the repo and make the scripts executable:

```sh
git clone git@github.com:Abdallah-Abdelazim/gradle-cache-cleaner.git
cd gradle-cache-cleaner
chmod +x clean-gradle-cache clean-xcode-cache
```

Then run one directly:

```sh
./clean-gradle-cache --dry-run
./clean-xcode-cache --dry-run
```

Or place them on your `PATH` for convenience:

```sh
cp clean-gradle-cache clean-xcode-cache /usr/local/bin/
```
