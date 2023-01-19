Installation of JavaScript Engines
==================================

2023-01-19
Jaeryeong Kim

iMac
macOS Monterey

Engines
----------------------------------
### d8

```shell
# Prerequisites
cd $ HOME

# install depot_tools and update

mkdir -p bin
mkdir -p bin/v8

cd bin/v8
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git

export PATH="/Users/jaeryeong/bin/v8/depot_tools:$PATH"

# Build V8

# gclient
gclient sync

cd v8
gclient sync

# For xcode-select error
# xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
# sudo xcode-select -s /Applications/Xcode.app/Contents/Developer 

gn gen out/d8 --args='v8_use_external_startup_data=false'
ninja -C out/d8 d8

```