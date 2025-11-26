# Home Assistant Snapcast-server Add-on

This add-on provides a Snapcast-server with support for Spotify connect and DLNA Media Renderer via [gmrender-resurrect](https://github.com/hzeller/gmrender-resurrect). \
If the add-on doesn't show up in Spotify, try providing the Spotify username/password to connect the add-on to your account.

## How to use

1. Install the addon.
2. Connect snapcast clients to the snapcast server.
3. Start streaming using one of the configured streams or stream from Spotify directly to the addon.

## Configuration notes

- **Default sources**: Spotify (librespot) and a DLNA Renderer are preconfigured and enabled. Each can be toggled independently from the configuration page if you prefer to expose only your custom inputs.
- **Additional streams**: Use the `streams` option to append extra Snapcast sources. See the [Snapcast configuration documentation](https://github.com/badaix/snapcast/blob/master/doc/configuration.md) for formatting examples.
- **Librespot specific options**: Advanced users can append any additional librespot parameters by filling in `Librespot specific options (modifies the default Spotify stream)` on the configuration tab. Provide the desired `key=value` pairs separated by `&` (for example `bitrate=320&autoplay=true`). The complete list of supported flags is documented in the [librespot Options wiki](https://github.com/librespot-org/librespot/wiki/Options).