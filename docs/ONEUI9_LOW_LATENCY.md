# One UI 9 and wireless low-latency notes

DX Manager was exercised against a Galaxy Z Fold 8 Ultra running Android 17
and One UI 9 over wireless ADB.

## Verified path

- ADB endpoint: ordinary Android wireless debugging TCP transport
- scrcpy: bundled 4.1 build
- Independent virtual display: 1280 x 720 at 320 DPI
- DeX-style overlay display: 1600 x 900 at 150 DPI
- Start-app launch: Android Settings on both display types
- Cleanup: overlay display removed after the session

The display creation and scrcpy launch completed without a black-screen or
secondary-display failure. Protected-buffer warnings can still appear for a
virtual display because Android restricts protected content on mirrored
displays.

## Low-latency controls

The **Low-latency video** option adds:

- `--video-buffer=0`, which avoids an intentional frame queue;
- `--render-driver=direct3d` on Windows, allowing SDL to choose the hardware
  Direct3D renderer; and
- above-normal Windows process priority for the scrcpy client.

The Windows client also enables the process priority boost when the operating
system allows it. Wireless ADB reuses an already authorized endpoint instead of
issuing another `adb connect` for every launch, which avoids an unnecessary
transport reset when the phone is already connected.

These changes preserve the selected FPS. They do not change the phone's
encoder workload or the Wi-Fi quality, so a busy access point can still add
delay.

The **HID mouse (-M)** option now maps to scrcpy's explicit `--mouse=uhid`
mode on current releases. Clear it to use `--mouse=sdk` by leaving HID off;
this often gives a more responsive pointer on wireless ADB while preserving
the 60 FPS video stream. HID remains useful for apps that require physical
mouse semantics.

## Screen behavior

New profiles leave **Turn phone screen off (-S)** disabled. Existing profiles
are migrated to that safe default once, so dragging a DeX window does not
unexpectedly blank the phone. The option remains available if screen-off is
wanted for a particular session.
