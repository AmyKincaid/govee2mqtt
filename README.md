# Govee to MQTT bridge for Home Assistant

This repo provides a `govee` executable whose primary purpose is to act
as a bridge between [Govee](https://govee.com) devices and Home Assistant,
via the [Home Assistant MQTT Integration](https://www.home-assistant.io/integrations/mqtt/).

> **Unofficial fork** of [wez/govee2mqtt](https://github.com/wez/govee2mqtt)
> (all credit for the project goes to Wez Furlong). This fork only adds two
> fixes that are not yet merged upstream, so the add-on keeps working today.

## What this fork fixes

|Problem|Fix|Upstream PR|
|-------|---|-----------|
|**2FA login:** Govee now requires an email verification code for many accounts, so the login to the undocumented API fails (status 454) and no IoT features work.|On 454 the code is requested by email; enter it via the new `govee_2fa_code` option, `GOVEE_2FA_CODE` or `--govee-2fa-code`. Also updates the app version/user agent sent to Govee.|[#656](https://github.com/wez/govee2mqtt/pull/656) by @nungster|
|**Tap-to-Run:** one-click scenes fail with `authorization token is invalid` and new ones don't appear after purging caches ([#737](https://github.com/wez/govee2mqtt/issues/737)).|Tap-to-Run shortcuts are fetched with the main account token instead of the community-api token.|[#739](https://github.com/wez/govee2mqtt/pull/739) by @UPEngineer|

Everything else is identical to upstream. Once both PRs are released
upstream, switch back to the official add-on. Add-on and container images
of this fork are published as `ghcr.io/amykincaid/govee2mqtt`.

### Installing this fork as a Home Assistant add-on

1. In Home Assistant: Settings > Add-ons > Add-on Store > three-dot menu > Repositories.
2. Add `https://github.com/AmyKincaid/govee2mqtt` and install
   "Govee to MQTT Bridge (2FA + Tap-to-Run fix)". See [docs/ADDON.md](docs/ADDON.md).
3. Stop the official add-on first, otherwise devices are published twice.

### 2FA login

If your Govee account requires 2FA, login fails on first start with
status 454 and Govee emails a verification code to your account.
Enter that code in the add-on option `govee_2fa_code` (Docker:
`GOVEE_2FA_CODE`, CLI: `--govee-2fa-code`) and restart. Once running you
can clear the option; the code is single-use.
## Features

* Robust LAN-first design. Not all of Govee's devices support LAN control,
  but for those that do, you'll have the lowest latency and ability to
  control them even when your primary internet connection is offline.
* Support for per-device modes and scenes.
* Support for the undocumented AWS IoT interface to your devices, provides
  low latency status updates.
* Support for the new [Platform
  API](https://developer.govee.com/reference/get-you-devices) in case the AWS
  IoT or LAN control is unavailable.

|Feature|Requires|Notes|
|-------|--------|-------------|
|DIY Scenes|API Key|Find in the list of Effects for the light in Home Assistant|
|Music Modes|API Key|Find in the list of Effects for the light in Home Assistant|
|Tap-to-Run / One Click Scene|IoT|Find in the overall list of Scenes in Home Assistant, as well as under the `Govee to MQTT` device|
|Live Device Status Updates|LAN and/or IoT|Devices typically report most changes within a couple of seconds.|
|Segment Color|API Key|Find the `Segment 00X` light entities associated with your main light device in Home Assistant|

* `API Key` means that you have [applied for a key from Govee](https://developer.govee.com/reference/apply-you-govee-api-key)
  and have configured it for use in govee2mqtt
* `IoT` means that you have configured your Govee account email and password for
  use in govee2mqtt, which will then attempt to use the
  *undocumented and likely unsupported* AWS MQTT-based IoT service
* `LAN` means that you have enabled the [Govee LAN API](https://app-h5.govee.com/user-manual/wlan-guide)
  on supported devices and that the LAN API protocol is functional on your network

## Usage

* [Installing the HASS Add-On](docs/ADDON.md) - for HAOS and Supervised HASS users
* [Running it in Docker](docs/DOCKER.md)
* [Configuration](docs/CONFIG.md)

## Have a question?

* [Is my device supported?](docs/SKUS.md)
* [Check out the FAQ](docs/FAQ.md)

## Want to show your support or gratitude?

The project itself is written and maintained by Wez Furlong. The links below
are **his** sponsor links (the original author), not mine; this fork only adds
the two small fixes above.

It takes significant effort to build, maintain and support users of software
like this. If you can spare something to say thanks, it is appreciated!

* [Sponsor Wez on Github](https://github.com/sponsors/wez)
* [Sponsor Wez on Patreon](https://patreon.com/WezFurlong)
* [Sponsor Wez on Ko-Fi](https://ko-fi.com/wezfurlong)
* [Sponsor Wez via liberapay](https://liberapay.com/wez)

## Credits

This work is based on Wez Furlong's earlier work with [Govee LAN
Control](https://github.com/wez/govee-lan-hass/).

The AWS IoT support was made possible by the work of @bwp91 in
[homebridge-govee](https://github.com/bwp91/homebridge-govee/).

