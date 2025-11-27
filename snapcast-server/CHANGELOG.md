## 0.4.8 (2025-11-27)

- feat: enable builds for all supported architectures (amd64, armhf, armv7, aarch64, i386)

## 0.4.7 (2025-11-26)

- fix: descriptions in config not displayed

## 0.4.6 (2025-11-26)

- feat: add configuration toggles to disable the built-in Spotify and DLNA sources
- feat: add optional librespot parameter passthrough for the default Spotify source
- feat: add detailed UI descriptions
- docs: document new configuration workflow

## 0.4.5 (2025-11-25)

- Forked from DjFabFab's snapcast-server 0.4.4
- feat: add initial Snapcast client volume control in UI
- update: bump librespot to 0.8.0 (fixes broken Spotify stream)

## 0.4.4

- update: bump ghcr.io/hassio-addons/base to v18.1.0
- update: bump librespot to 0.7.0 (fixes #10)
- chore: cleanup Dockerfile

## 0.4.3

- fix: respect samplerate in DLNA Media Render
- fix(snapserver): typo in config

## 0.4.2

- fix dbus long startup by @unex in #4

## 0.4.1

- Fix snapweb. Drop support for 32bit platforms

## 0.4.0

- Dependcy Updates. Please migrate to libresport / Spotify token auth.

## 0.3.0

- Add support for DLNA Media Renderer: Connect Homeassitant with snapcast through DLNA

## 0.2.1

- Wait before starting snapcast-client until snapcast-server is started. 
- Use host `127.0.0.1` for snapcast-client 

## 0.2.0

- Add Snapcast client for local playback through Home Assistant.

## 0.1.0

- Add support for Snapweb: a web-interface for Snapcast.

## 0.0.1

- Initial version based on the Home Assistant Add-on example. Supports Snapcast server and Spotify connect.
