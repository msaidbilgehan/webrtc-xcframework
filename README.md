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
    url: "https://github.com/msaidbilgehan/webrtc-xcframework/releases/download/155.8059.3/WebRTC.xcframework.zip",
    checksum: "6eece3236a44bdec3fe7f7eb9c1beb38d0bad2bbd8d109a12629f9e94fe353c4"
)
```

| Release | Changes from upstream |
|---|---|
| `155.8059.3` | 1–6 (5 also bounds probe results and the second after ALR) |
| `155.8059.2` | 1–6 |
| `155.8059.1` | 1–4 |

Tags are `<milestone>.<branch>.<release>`: the last part grows when the patch set changes on the same branch.

The module is `WebRTC`; the prefix is defined in the framework's `RTCMacros.h`, so `import WebRTC` sees the prefixed names.

## Changes from upstream

1. An external Objective-C encoder can report HEVC codec-specific info (`RTCCodecSpecificInfoH265`); upstream converts only
   the H.264 one.
2. The macOS Metal video view names two types with `RTC_OBJC_TYPE`, so the framework compiles with a type prefix.
3. The macOS framework bundle carries its public headers (upstream ships only the umbrella header) and places
   `PrivacyInfo.xcprivacy` in `Versions/A/Resources`.
4. The `PB` prefix is defined in `RTCMacros.h` instead of a compiler define, so clients that import the framework see it.
5. While the sender is application limited — in ALR or within `grace` after it — an over-use episode lowers the
   delay-based estimate at most once, to no less than `floor` times the estimate at its first back-off, a probe result
   lowers it to no less than `floor` times the estimate, and the application-limited throughput is not fed to the link
   capacity estimate. Upstream backs off to 0.85 times the acknowledged throughput and lets a low probe result replace the
   estimate down to that bound — on a still screen a fraction of the link, and over Wi-Fi a probe at twice the estimate is
   often received in bunches. Behind the field trial `WebRTC-Bwe-AlrBoundedBackoff/Enabled,floor:0.5,grace:1s/`; off by
   default.
6. `PBRTCPeerConnectionFactory` gains `initWithEncoderFactory:decoderFactory:fieldTrials:`, so field trials are given per
   factory instead of through the deprecated process-wide `RTCInitFieldTrialDictionary`.

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
