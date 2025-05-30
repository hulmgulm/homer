# Smart cards

Some cards can use a specific a component that provides some extra features by adding a `type` key to the service yaml
configuration and other parameters when needed.

> [!WARNING]  
> Note that `config.yml` is exposed at `/assets/config.yml` via HTTP and any sensitive information, like api keys,
> included in the configuration file is exposed to anyone who can access the homer instance. Only include an api key
> if your homer instance is secured behind some form of authentication or access restriction.

Available services are in `src/components/`. Here is an overview of all smart cards that are available
within Homer:

- [Common options](#common-options)
- [AdGuard Home](#adguard-home)
- [CopyToClipboard](#copy-to-clipboard)
- [Docuseal](#docuseal)
- [Docker Socket Proxy](#docker-socket-proxy)
- [Emby / Jellyfin](#emby--jellyfin)
- [FreshRSS](#freshrss)
- [Gitea / Forgejo](#gitea--forgejo)
- [Glances](#glances)
- [Gotify](#gotify)
- [Healthchecks](#healthchecks)
- [Home Assistant](#home-assistant)
- [Immich](#immich)
- [Jellystat](#jellystat)
- [Lidarr, Prowlarr, Sonarr, Readarr and Radarr](#lidarr-prowlarr-sonarr-readarr-and-radarr)
- [Linkding](#linkding)
- [Matrix](#matrix)
- [Mealie](#mealie)
- [Medusa](#medusa)
- [Nextcloud](#nextcloud)
- [OctoPrint / Moonraker](#octoprintmoonraker)
- [Olivetin](#olivetin)
- [OpenHAB](#openhab)
- [OpenWeatherMap](#openweathermap)
- [PaperlessNG](#paperlessng)
- [PeaNUT](#peanut)
- [PiAlert](#pialert)
- [PiHole](#pihole)
- [Ping](#ping)
- [Plex](#plex)
- [Portainer](#portainer)
- [Prometheus](#prometheus)
- [Proxmox](#proxmox)
- [qBittorrent](#qbittorrent)
- [rTorrent](#rtorrent)
- [SABnzbd](#sabnzbd)
- [Scrutiny](#scrutiny)
- [Speedtest Tracker](#speedtesttracker)
- [Tautulli](#tautulli)
- [Tdarr](#tdarr)
- [Traefik](#traefik)
- [TrueNas Scale](#truenas-scale)
- [Uptime Kuma](#uptime-kuma)
- [Vaultwarden](#vaultwarden)
- [Wallabag](#wallabag)
- [What's Up Docker](#whats-up-docker)

> [!IMPORTANT]  
> Using smart cards, which interact with other services, will require either that:
>
> - All services are exposed on the **same domain** as homer (mydomain.tld/pihole, mydomain.tld/proxmox), avoiding any cross domain request issues (CORS).
> - All services **accept cross site requests** (= send the necessary CORS headers, either set directly in the service configuration if possible, or using a proxy to set the headers)
>
> If you experiencing any issue, please have a look to the [troubleshooting](troubleshooting.md#my-service-card-doesnt-work-nothing-appears-or-offline-status-is-displayed-pi-hole-sonarr-ping-) page.

## Common options

```yaml
- name: "My Service"
  logo: "assets/tools/sample.png"
  url: "http://my-service-link"
  endpoint: "http://my-service-endpoint" # Optional: alternative base URL used to fetch service data is necessary.
  useCredentials: false # Optional: Override global proxy.useCredentials configuration.
  headers: # Optional: Override global proxy.headers configuration.
  type: "<type>"
```

## AdGuard Home

For AdGuard Home you need to set the type to AdGuard, if you have some issues as 403 responses on requests you need to provide authentication in headers for locations needed as below.

```yaml
- name: "Adguard"
  logo: "assets/tools/adguardhome.png"
  url: "https://adguard.exemple.com"
  target: "_blank"
  type: "AdGuardHome"
```

## Copy to Clipboard

This service displays the same information of a generic one, but shows an icon button on the indicator place (right side) you can click to get the content of the `clipboard` field copied to your clipboard.

You can still provide an `url` that would be open when clicked anywhere but on the icon button.

Configuration example:

```yaml
- name: "Copy me!"
  logo: "assets/tools/sample.png"
  subtitle: "Subtitle text goes here"
  url: "#"
  type: "CopyToClipboard"
  clipboard: "this text will be copied to your clipboard"
```

## Docker Socket Proxy

This service display the number of running, stopped and containers that have errors.
It calls the API of DOcker Socket Proxy

```yaml
- name: Docker
  type: "DockerSocketProxy"
  endpoint: "http://192.168.0.151:2375"
```

## Docuseal

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: Docuseal
  type: Docuseal
  logo: assets/tools/sample.png
  url: http://docuseal.example.com
```

## Emby / Jellyfin

You need to set the type to Emby, provide an api key and choose which stats to show if the subtitle is disabled.

```yaml
- name: "Emby"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151/"
  type: "Emby"
  apikey: "<---insert-api-key-here--->"
  libraryType: "music" #Choose which stats to show. Can be one of: music, series or movies.
```

## FreshRSS

The FreshRSS service displays unread and subscriptions counts from your FreshRSS server.

```yaml
- name: "FreshRSS"
  type: "FreshRSS"
  username: "<-- Your username -->"       
  password: "<-- Your password -->"
  updateInterval: 5000 # (Optional) Interval (in ms) for updating the stats
```

## Gitea / Forgejo

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: Forgejo
  type: Gitea
  logo: assets/tools/sample.png
  url: http://git.example.com
```

## Glances

This is a basic widget for showing cpu and ram usage using a glances server

You'll need a glances server up and running, this is a sample compose.yml

```yml
---
services:
  glances:
    image: nicolargo/glances:latest
    container_name: glances
    environment:
      - TZ=Europe/Rome
      - GLANCES_OPT=-w
    ports:
      - 61208:61208
    restart: unless-stopped
```

And this is a sample homer configuration

```yml
- name: System
  icon: "fa-solid fa-heart-pulse"
  url: http://192.168.1.2:61208
  type: Glances
  stats: [cpu, mem] # Metric to display. Possible values are: load, cpu, mem, swap.
  updateInterval: 5000 # (Optional) Interval (in ms) for updating the stats   
```

## Gotify

The Gotify service will show the number of currently oustanding messages
available as well as the overall health of the system.

Note that `apikey` must be a client token, not an app token.

```yaml
- name: "Gotify"
  type: "Gotify"
  apikey: "<api_key>" # Client token to retrieve messages
```

## Healthchecks

This service displays information about the configured status checks from the Healthchecks application.
Two lines are needed in the config.yml :

```yaml
  type: "Healthchecks"
  apikey: "<---insert-api-key-here--->"
```

The url must be the root url of the Healthchecks application.
The Healthchecks API key can be found in Settings > API Access > API key (read-only). The key is needed to access Healthchecks API.

## Home Assistant

You need to set the type to HomeAssistant, provide an api key and enable cors on Home Assistant.

```yaml
- name: "HomeAssistant"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151/"
  type: "HomeAssistant"
  apikey: "<---insert-api-key-here--->"
  items: [] # optional, which items to show (and in which order) in the subtitle. Possible values are "name", "version", "entities"
  separator: " " # optional, how to separate items
```

To create an API token on HomeAssistant, follow the [official documentation here](https://developers.home-assistant.io/docs/auth_api/#long-lived-access-token).  
To enable cors on HomeAssistant, edit your `configuration.yml` and add the IP of Homer to `https: cors_allowed_origins`

## Immich

The Immich service displays stats from your Immich server.
The Immich server must be running at least version 1.118.0 for the correct api endpoint to work.

```yaml
- name: "Immich"
  type: "Immich"
  apikey: "<--- Your api key --->" # administrator user
  updateInterval: 5000 # (Optional) Interval (in ms) for updating the stats
```

## Jellystat

The Jellystat service display the number of concurrent streams on your jellyfin server.
The Jellystat server must be running behind a reverse proxy to add some cors headers:

- Access-Control-Allow-Origin: ${your_domain}
- Access-Control-Allow-Headers: Authorization

```yaml
- name: "Jellystat"
  logo: "assets/tools/jellystat.png"
  url: "http://192.168.1.154:3000"
  type: "Jellystat"
  apikey: "<---insert-api-key-here--->"
```

You can create an API key in the dashboard of you jellystat server: settings/API Keys -> Add Key

## Lidarr, Prowlarr, Sonarr, Readarr and Radarr

This service displays Activity (blue), Missing(purple) Warning (orange) or Error (red) notifications bubbles from the Lidarr, Readarr, Radarr or Sonarr application.
Two lines are needed in the config.yml :

```yaml
  type: "Lidarr", "Prowlarr", "Radarr" or "Sonarr"
  apikey: "<---insert-api-key-here--->"
  checkInterval: 5000 # (Optional) Interval (in ms) for updating the status
```

The url must be the root url of Lidarr, Prowlarr, Readarr, Radarr or Sonarr application.
The Lidarr, Prowlarr, Readarr, Radarr or Sonarr API key can be found in Settings > General. It is needed to access the API.
If you are using an older version of Radarr or Sonarr which don't support the new V3 api endpoints, add the following line to your service config "legacyApi: true", example:

```yaml
- name: "Radarr"
  type: "Radarr"
  url: "http://localhost:7878/"
  apikey: "<---insert-api-key-here--->"
  target: "_blank"
  legacyApi: true
```

## Linkding

This integration makes it possible to query Linkding and list multiple results from Linkding.
Linkding has to be configured with CORS enabled. Linkding does not support that, but a reverse proxy in front can fix that.
For example if you use Traefik, documentation about that is here: <https://doc.traefik.io/traefik/middlewares/http/headers/#cors-headers>
Examples for various servers can be found at <https://enable-cors.org/server.html>.

This integration supports at max 15 results from Linkding. But you can add it multiple times to you dashboard with different queries to retrieve what you need.

```yaml
      - name: "Linkding"
        # Url to Linkding instance
        url: https://ld.ceesbos.nl
        token: "<add your secret token here>"
        type: "Linkding"
        # Maximum number of items returned by Linkding, minimal 1 and max 15
        limit: 10 
        # query to do on Linkding. Use #tagname to search for tags
        query: "#ToDo #Homer" 
```

## Matrix

This service displays a version string instead of a subtitle. The indicator
shows if Matrix Server is online, offline

```yaml
- name: "Matrix - Server"
  type: "Matrix"
  logo: "assets/tools/sample.png"
  url: "http://matrix.example.com"
```

## Mealie

First off make sure to remove an existing `subtitle` as it will take precedence if set.
Setting `type: "Mealie"` will then show the number of recipes Mealie is keeping organized or the planned meal for today if one is planned. You will
have to set an API key in the field `apikey` which can be created in your Mealie installation. The API page can be found: Click on hamburger menu -> Click on your profile -> Click on "Manage your API Tokens"

```yaml
  type: "Mealie"
  apikey: "<---insert-api-key-here--->"
```

## Medusa

This service displays News (grey), Warning (orange) or Error (red) notifications bubbles from the Medusa application.
Two lines are needed in the config.yml :

```yaml
  type: "Medusa"
  apikey: "<---insert-api-key-here--->"
```

The url must be the root url of Medusa application.
The Medusa API key can be found in General configuration > Interface. It is needed to access Medusa API.

## Nextcloud

This service displays a version string instead of a subtitle. The indicator
shows if Nextcloud is online, offline, or in [maintenance
mode](https://docs.nextcloud.com/server/stable/admin_manual/maintenance/upgrade.html#maintenance-mode).
Example configuration:

```yaml
- name: Nextcloud
  type: Nextcloud
  logo: assets/tools/sample.png
  url: http://nextcloud.example.com
```

## OctoPrint/Moonraker

The OctoPrint/Moonraker service only needs an `apikey` & `endpoint` and optionally a `display` or `url` option. `url` can be used when you click on the service it will launch the `url`

Moonraker's API mimmicks a few of OctoPrint's endpoints which makes these services compatible. See <https://moonraker.readthedocs.io/en/latest/web_api/#octoprint-api-emulation> for details.

```yaml
- name: "Octoprint"
  logo: "https://cdn-icons-png.flaticon.com/512/3112/3112529.png"
  apikey: "xxxxxxxxxxxx" # insert your own API key here.
  endpoint: "http://192.168.0.151:8080"
  display: "text" # 'text' or 'bar'. Default to `text`.
  type: "OctoPrint"
```

## Olivetin

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: Olivetin
  type: Olivetin
  logo: assets/tools/sample.png
  url: https://olivetin.example.com
```

## OpenHAB

You need to set the type to OpenHAB, provide an api key and enable cors on OpenHAB.

```yaml
- name: "OpenHAB"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151/"
  type: "OpenHAB"
  apikey: "<---insert-api-key-here--->"
  things: true # true will query the things API and report total and online things count. false will skip the call
  items: true # true will query the items API and report total items count. false will skip the call
```

To create an API token on OpenHAB, follow the [official documentation here](https://www.openhab.org/docs/configuration/apitokens.html).  
To enable cors on OpenHAB, edit your services/runtime.cfg and uncomment or add this line: `org.openhab.cors:enable=true`

## OpenWeatherMap

Using the OpenWeatherMap service you can display weather information about a given location.
The following configuration is available for the OpenWeatherMap service:

```yaml
- name: "Weather"
  location: "Amsterdam" # your location.
  locationId: "2759794" # Optional: Specify OpenWeatherMap city ID for better accuracy
  apikey: "<---insert-api-key-here--->" # insert your own API key here. Request one from https://openweathermap.org/api.
  units: "metric" # units to display temperature. Can be one of: metric, imperial, kelvin. Defaults to kelvin.
  background: "square" # choose which type of background you want behind the image. Can be one of: square, circle, none. Defaults to none.
  type: "OpenWeather"
```

**Remarks:**
If for some reason your city can't be found by entering the name in the `location` property, you could also try to configure the OWM city ID in the `locationId` property. To retrieve your specific City ID, go to the [OWM website](https://openweathermap.org), search for your city and retrieve the ID from the URL (for example, the City ID of Amsterdam is 2759794).

## PaperlessNG

This service displays total number of documents stored. Two lines are required:

```yaml
  type: "PaperlessNG"
  apikey: "<---insert-api-key-here--->"
```

API key can be generated in Settings > Administration > Auth Tokens

## PeaNUT

This service show current status of the UPS device. By default, the subtitle line shows UPS load, unless you provide the `subtitle` property

```yaml
- name: "PeaNUT"
  type: PeaNUT
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151"
  # device: "ups" # The ID of the device
```

## PiAlert

The PiAlert service displays stats from your PiAlert server.

```yaml
- name: "PiAlert"
  type: "PiAlert"
  updateInterval: 5000 # (Optional) Interval (in ms) for updating the stats
```

## PiHole

Using the PiHole service you can display info about your local PiHole instance right on your Homer dashboard.

The following configuration is available for the PiHole service.

```yaml
- name: "Pi-hole"
  logo: "assets/tools/sample.png"
  # subtitle: "Network-wide Ad Blocking" # optional, if no subtitle is defined, PiHole statistics will be shown
  url: "http://192.168.0.151/admin"
  # endpoint: "http://192.168.0.151" # optional, For v6 API, this is the base URL used to fetch Pi-hole data overwriting the url
  apikey: "<---insert-api-key-here--->" # optional, needed if web interface is password protected
  type: "PiHole"
  apiVersion: 5 # optional, defaults to 5. Use 6 if your PiHole instance uses API v6
  checkInterval: 3000 # optional, defaults to 300000. interval in ms to check Pi-hole status
```

**Remarks:**
- If PiHole web interface is password protected, obtain the `apikey` from Settings > API/Web interface > Show API token.
- For PiHole instances using API v6, set `apiVersion: 6` in your configuration. This enables session management and proper authentication handling.

## Ping

This card checks if the target link is available. All you need is to set the `type` to `Ping` and provide a url. By default the HEAD method is used but it can be configured to use GET using the optional `method` property. By default, the subtitle line shows the round trip time (RTT) of the request, unless you provide the `subtitle` property. Optionnaly, use `successCodes` to define which HTTP response status codes should be considered as available status.

```yaml
- name: "Awesome app"
  type: Ping
  logo: "assets/tools/sample.png"
  url: "https://www.wikipedia.org/"
  # method: "head"
  # successCodes: [200, 418] # Optional, default to all 2xx HTTP response status codes
  # timeout: 500 # Timeout in ms before ping is aborted. Default 2000
  # subtitle: "Bookmark example" # By default, request round trip time is displayed when subtitle is not set
  # updateInterval: 5000 # (Optional) Interval (in ms) for updating ping status
```

## Plex

This card shows the current active streams and the number of movies and series that this Plex instance has.

```yaml
- name: Plex
  type: "Plex"
  logo: "assets/tools/sample.png"
  token: "<---insert-plex-token-here--->" # see here how to get the plex token: https://www.plexopedia.com/plex-media-server/general/plex-token/
  url: "http://192.168.0.151:32400/web"
  endpoint: "http://192.168.0.151:32400"
```

## Portainer

This service displays info about the total number of containers managed by your Portainer instance.
In order to use it, you must be using Portainer version 1.11 or later. Generate an access token from the UI and pass
it to the apikey field.
By default, every connected environments will be checked. To select specific ones, add an "environments" entry which can be a simple string or an array containing all the selected environments name.

### New features

Displays the Portainer version from /api/status  
Shows online/offline status depending on API reachability  

See <https://docs.portainer.io/api/access#creating-an-access-token>  

```yaml
- name: "Portainer"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151/"
  type: "Portainer"
  apikey: "<---insert-api-key-here--->"
  # environments:
  #   - "raspberry"
  #   - "local"
```

## Prometheus

For Prometheus you need to set the type to Prometheus and provide a url.

```yaml
- name: "Prometheus"
  type: Prometheus
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151/"
  # subtitle: "Monitor data server"
```

## Proxmox

This service displays status information of a Proxmox node (VMs running and disk, memory and cpu used). It uses the proxmox API and [API Tokens](https://pve.proxmox.com/pve-docs/pveum-plain.html) for authorization so you need to generate one to set in the yaml config. You can set it up in Proxmox under Permissions > API Tokens. You also need to know the realm the user of the API Token is assigned to (by default pam).

The API Token (or the user assigned to that token if not separated permissions is checked) are this:

| Path                | Permission | Comments                                                          |
|---------------------|------------|-------------------------------------------------------------------|
| /nodes/\<your-node> | Sys.Audit  |                                                                   |
| /vms/\<id-vm>       | VM.Audit   | You need to have this permission on any VM you want to be counted |

It is highly recommended that you create and API Token with only these permissions on a read-only mode.

If you get errors, they will be shown on browser's dev console. Main issues tend to be CORS related as Proxmox does not include CORS headers and you have to deploy it behind a reverse proxy and make the proxy add this headers.

Configuration example:

```yaml
- name: "Proxmox - Node"
  logo: "https://www.google.com/url?sa=i&url=https%3A%2F%2Fgithub.com%2FandOTP%2FandOTP%2Fissues%2F337&psig=AOvVaw2YKVuEUIBeTUikr7kAjm8D&ust=1665323538747000&source=images&cd=vfe&ved=0CAkQjRxqFwoTCPCTruLj0PoCFQAAAAAdAAAAABAN"
  type: "Proxmox"
  url: "https://your.proxmox.server"
  node: "your-node-name"
  warning_value: 50
  danger_value: 80
  api_token: "PVEAPIToken=root@pam!your-api-token-name=your-api-token-key"
  # values below this line are optional (default value are false/empty):
  hide_decimals: true # removes decimals from stats values.
  hide: [] # hides information. Possible values are "vms", "vms_total", "lxcs", "lxcs_total", "disk", "mem" and "cpu".
  small_font_on_small_screens: true # uses small font on small screens (like mobile)
  small_font_on_desktop: true # uses small font on desktops (just in case you're showing much info)
```

## qBittorrent

This service displays the global upload and download rates, as well as the number of torrents
listed. The service communicates with the qBittorrent API interface which needs
to be accessible from the browser. Please consult
[the instructions](https://github.com/qbittorrent/qBittorrent/pull/12579)
for setting up qBittorrent and make sure the correct CORS-settings are applied. Examples for various
servers can be found at [enable-cors.org](https://enable-cors.org/server.html).

```yaml
- name: "qBittorrent"
  logo: "assets/tools/sample.png"
  url: "http://192.168.1.2:8080" # Your rTorrent web UI, f.e. ruTorrent or Flood.
  type: "qBittorrent"
  rateInterval: 2000 # Interval for updating the download and upload rates.
  torrentInterval: 5000 # Interval for updating the torrent count.
  target: "_blank" # optional html a tag target attribute
```

## rTorrent

This service displays the global upload and download rates, as well as the number of torrents
listed in rTorrent. The service communicates with the rTorrent XML-RPC interface which needs
to be accessible from the browser. Please consult
[the instructions](https://github.com/rakshasa/rtorrent-doc/blob/master/RPC-Setup-XMLRPC.md)
for setting up rTorrent and make sure the correct CORS-settings are applied. Examples for various
servers can be found at <https://enable-cors.org/server.html>.

```yaml
- name: "rTorrent"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151" # Your rTorrent web UI, f.e. ruTorrent or Flood.
  xmlrpc: "http://192.168.0.151:8081" # Reverse proxy for rTorrent's XML-RPC.
  type: "Rtorrent"
  rateInterval: 5000 # Interval for updating the download and upload rates.
  torrentInterval: 60000 # Interval for updating the torrent count.
  username: "username" # Username for logging into rTorrent (if applicable).
  password: "password" # Password for logging into rTorrent (if applicable).
```

## SABnzbd

The SABnzbd service can allow you to show the number of currently active
downloads on your SABnzbd instance. An API key is required, and can be obtained from
the "Config" > "General" section of the SABnzbd config in the SABnzbd web UI.

```yaml
- name: "SABnzbd"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151:8080"
  type: "SABnzbd"
  apikey: "MY-SUPER-SECRET-API-KEY"
  downloadInterval: 5000 # (Optional) Interval (in ms) for updating the download count
```

## Scrutiny

This service displays info about the total number of disk passed and failed S.M.A.R.T and scrutiny checks

```yaml
- name: "Scrutiny"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151:8080"
  type: "Scrutiny"
  updateInterval: 5000 # (Optional) Interval (in ms) for updating the status
```

## SpeedtestTracker

This service will show the download and upload speeds in Mbit/s and the ping in ms.
To configure the service, you need to define the url of SpeedtestTracker running and an entry with type `SpeedtestTracker`.

Configuration example:

```yaml
- name: "Speedtest Tracker"
  type: "SpeedtestTracker"
  url: "http://192.168.0.1:8080"
  target: "_blank"
```

## Tautulli

The Tautulli service can allow you to show the number of currently active
streams on you Plex instance. An API key is required, and can be obtained from
the "Web Interface" section of settings on the Tautulli web UI.

```yaml
- name: "Tautulli"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151:8181"
  type: "Tautulli"
  apikey: "<---insert-api-key-here--->"
  checkInterval: 5000 # (Optional) Interval (in ms) for updating the status
```

Because the service type and link don't necessarily have to match, you could
even make the service type Tautulli on your Plex card and provide a separate
endpoint pointing to Tautulli!

```yaml
- name: "Plex"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151:32400/web" # Plex
  endpoint: "http://192.168.0.151:8181" # Tautulli
  type: "Tautulli"
  apikey: "<---insert-api-key-here--->"
```

## Tdarr

The Tdarr service can allow you to show the number of currently queued items
for transcoding on your Tdarr instance as well as the number of errored items.

```yaml
- name: "Tdarr"
  logo: "assets/tools/sample.png"
  url: "http://192.168.0.151:8265"
  type: "Tdarr"
  checkInterval: 5000 # (Optional) Interval (in ms) for updating the queue & error counts
```

## Traefik

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: Traefik
  type: Traefik
  logo: assets/tools/sample.png
  url: http://traefik.example.com
```

## Truenas Scale

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: "Truenas"
  type: "TruenasScale"
  logo: "assets/tools/sample.png"
  url: "http://truenas.example.com"
  api_token: "your_api_token"
```

## Uptime Kuma

Using the Uptime Kuma service you can display info about your instance uptime right on your Homer dashboard.

The following configuration is available for the UptimeKuma service. Needs v1.13.1 or later because of the change in APIs due to [multiple status pages support](https://github.com/louislam/uptime-kuma/releases/tag/1.13.1).

```yaml
- name: "Uptime Kuma"
  logo: "assets/tools/sample.png"
  # subtitle: "A fancy self-hosted monitoring tool" # optional, if no subtitle is defined, Uptime Kuma incidents, if any, will be shown
  url: "http://192.168.0.151:3001"
  slug: "myCustomDashboard" # Defaults to "default" if not provided.
  type: "UptimeKuma"
```

## Vaultwarden

This service displays a version string instead of a subtitle. The indicator
shows if Vaultwarden is online, offline

```yaml
- name: "Vaultwarden - Server"
  type: "Vaultwarden"
  logo: "assets/tools/sample.png"
  url: "http://vaultwarden.example.com"
```

## Wallabag

This service displays a version string instead of a subtitle. Example configuration:

```yaml
- name: Wallabag
  type: Wallabag
  logo: assets/tools/sample.png
  url: https://wallabag.example.com
```

## What's up Docker

What's up Docker allow to display info about the number of container running and the number for which an update is available on your Homer dashboard.

The following configuration is available for the WUD service.

```yaml
- name: "What's Up Docker"
  logo: "assets/tools/sample.png"
  subtitle: "Docker image update notifier"
  url: "http://192.168.1.12:3001"
  type: "WUD"
```
