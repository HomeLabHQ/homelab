# Homelab

Config for media server

```mermaid
    C4Container
    title Container diagram for homelab mediaserver

    Person(user, User, "User that consumes media")
    System_Boundary(homelab, "Media server") {
        Container(heimdall, "Heimdall", "Homepage/dashboard")
        Container(qbit, "QbitTorrent", "Torrent client")
        Container(sonarr, "Sonarr", "Series watcher")
        Container(radarr, "Radarr", "Movies watcher")
        Container(prowlarr, "Prowlarr", "Search indexer")
        Container(jellyfin, "Jellyfin", "Media server")
        Container(jellyseerr, "Jellyseerr", "Media discovery&request service")
        Container(watchtower, "Watchtower", "Monitoring for new images")
    }
    Rel(user, heimdall, "Uses dashboard to navigate")
    Rel(watchtower, heimdall, "Checks for new images")
    Rel(watchtower, qbit, "Checks for new images")
    Rel(watchtower, sonarr, "Checks for new images")
    Rel(watchtower, radarr, "Checks for new images")
    Rel(watchtower, jellyfin, "Checks for new images")
    Rel(watchtower, jellyseerr, "Checks for new images")
    Rel(watchtower, prowlarr, "Checks for new images")
    BiRel(prowlarr, sonarr, "Syncs search feeds")
    BiRel(prowlarr, radarr, "Syncs search feeds")
    Rel(sonarr, qbit, "Delegate download tasks")
    Rel(radarr, qbit, "Delegate download tasks")
    Rel(user, jellyfin, "Consumes media")
    Rel(user, jellyseerr, "Discovers media, and requests download")
    Rel(jellyseerr, sonarr, "Requests tv media download")
    Rel(jellyseerr, radarr, "Requests movies media download")
    UpdateLayoutConfig($c4ShapeInRow="3", $c4BoundaryInRow="1")
```

## Crontab example

```sh
@reboot /home/user/start.sh

```

## Sonarr, Radarr, Jellyfin, Prowlarr

Please note for nginx to work you first need to setup basepath in each service separately, after that you can remove open port from them.

## Jellyseerr

Currently jellyseerr don't support baseurl setting (next js have some issue with that)
so it will have open port ;(
