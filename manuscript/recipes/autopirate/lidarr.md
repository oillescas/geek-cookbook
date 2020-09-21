hero: AutoPirate - A fully-featured recipe to automate finding, downloading, and organising your media 📺 🎥 🎵 📖

!!! warning
    This is not a complete recipe - it's a component of the [autopirate](/recipes/autopirate/) "_uber-recipe_", but has been split into its own page to reduce complexity.

# Lidarr

[Lidarr](https://lidarr.audio/) is an automated music downloader for NZB and Torrent. It performs the same function as [Headphones](/recipes/autopirate/headphones), but is written using the same(ish) codebase as [Radarr](/recipes/autopirate/radarr/) and [Sonarr](/recipes/autopirate/sonarr). It's blazingly fast, and includes beautiful album/artist art. Lidarr supports [SABnzbd](/recipes/autopirate/sabnzbd/), [NZBGet](/recipes/autopirate/nzbget/), Transmission, µTorrent, Deluge and Blackhole (_just like Sonarr / Radarr_)

![Lidarr Screenshot](../../images/lidarr.png)

## Inclusion into AutoPirate

To include Lidarr in your [AutoPirate](/recipes/autopirate/) stack, include the following in your autopirate.yml stack definition file:

````
lidarr:
  image: linuxserver/lidarr:latest
  env_file : /var/data/config/autopirate/lidarr.env
  volumes:
   - /var/data/autopirate/lidarr:/config
   - /var/data/media:/media
  networks:
  - internal

lidarr_proxy:
  image: a5huynh/oauth2_proxy
  env_file : /var/data/config/autopirate/lidarr.env
  networks:
    - internal
    - traefik_public
  deploy:
    labels:
      - traefik.frontend.rule=Host:lidarr.example.com
      - traefik.docker.network=traefik_public
      - traefik.port=4180
  volumes:
    - /var/data/config/autopirate/authenticated-emails.txt:/authenticated-emails.txt
  command: |
    -cookie-secure=false
    -upstream=http://lidarr:8181
    -redirect-url=https://lidarr.example.com
    -http-address=http://0.0.0.0:4180
    -email-domain=example.com
    -provider=github
    -authenticated-emails-file=/authenticated-emails.txt
````

!!! tip
    I share (_with my [sponsors](https://github.com/sponsors/funkypenguin)_) a private "_premix_" git repository, which includes necessary docker-compose and env files for all published recipes. This means that sponsors can launch any recipe with just a ```git pull``` and a ```docker stack deploy``` 👍

## Assemble more tools..

Continue through the list of tools below, adding whichever tools your want to use, and finishing with the **[end](/recipes/autopirate/end/)** section:

* [SABnzbd](/recipes/autopirate/sabnzbd.md)
* [NZBGet](/recipes/autopirate/nzbget.md)
* [RTorrent](/recipes/autopirate/rtorrent/)
* [Sonarr](/recipes/autopirate/sonarr/)
* [Radarr](/recipes/autopirate/radarr/)
* [Mylar](https://github.com/evilhero/mylar)
* [Lazy Librarian](/recipes/autopirate/lazylibrarian/)
* [Headphones](/recipes/autopirate/headphones/)
* Lidarr (this page)
* [NZBHydra](/recipes/autopirate/nzbhydra/)
* [NZBHydra](/recipes/autopirate/nzbhydra/)
* [NZBHydra2](/recipes/autopirate/nzbhydra2/)
* [Ombi](/recipes/autopirate/ombi/)
* [Jackett](/recipes/autopirate/jackett/)
* [Heimdall](/recipes/autopirate/heimdall/)
* [End](/recipes/autopirate/end/) (launch the stack)


## Chef's Notes 📓

1. In many cases, tools will integrate with each other. I.e., Radarr needs to talk to SABnzbd and NZBHydra, Ombi needs to talk to Radarr, etc. Since each tool runs within the stack under its own name, just refer to each tool by name (i.e. "radarr"), and docker swarm will resolve the name to the appropriate container. You can identify the tool-specific port by looking at the docker-compose service definition.
2. The addition of the Lidarr recipe was contributed by our very own @gpulido in Discord (http://chat.funkypenguin.co.nz) - Thanks Gabriel!