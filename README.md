# WebRTC.xcframework for macOS (arm64)

libwebrtc built from source for macOS on Apple silicon, packaged as an XCFramework for Swift Package Manager.

| | |
|---|---|
| Milestone | M155 — `branch-heads/8059` @ `5bd86a2f17a35dfc986b87d058f511e45805bd1d` |
| Platform | macOS 15 or later, arm64 only |
| Objective-C prefix | `PB` — `PBRTCPeerConnection`, `PBRTCVideoFrame`, …; enums, C functions and constants keep their `RTC` names |
| Codecs | H.265 on; VP9 off |
| Toolchain | Xcode 27.0 (27A266a) |

## Swift Package Manager

```swift
.binaryTarget(
    name: "WebRTC",
    url: "https://github.com/msaidbilgehan/webrtc-xcframework/releases/download/155.8059.1/WebRTC.xcframework.zip",
    checksum: "4e5a079de585e61384a647d82fdc12ae8bd15353d0cdca96992ec0cb2ddf717b"
)
```

The module is `WebRTC`; the prefix is defined in the framework's `RTCMacros.h`, so `import WebRTC` sees the prefixed names.

## Changes from upstream

1. An external Objective-C encoder can report HEVC codec-specific info (`RTCCodecSpecificInfoH265`); upstream converts only
   the H.264 one.
2. The macOS Metal video view names two types with `RTC_OBJC_TYPE`, so the framework compiles with a type prefix.
3. The macOS framework bundle carries its public headers (upstream ships only the umbrella header) and places
   `PrivacyInfo.xcprivacy` in `Versions/A/Resources`.
4. The `PB` prefix is defined in `RTCMacros.h` instead of a compiler define, so clients that import the framework see it.

## Build configuration

```
target_os="mac" target_cpu="arm64" mac_deployment_target="15.0" is_debug=false is_component_build=false
rtc_include_tests=false rtc_build_examples=false rtc_build_tools=false rtc_enable_objc_symbol_export=true
rtc_use_h265=true rtc_libvpx_build_vp9=false enable_dsyms=true enable_stripping=true
```

Target: `//sdk:mac_framework_objc`, built from an empty output directory.

## Licences

libwebrtc is distributed under the BSD-3-Clause licence with the WebRTC patent grant. The binary statically embeds other
open-source components; their notices are in [NOTICES.md](NOTICES.md), which must accompany any redistribution of the
binary.
