# HTPC Docker Standup

*PSA*: Project has moved to [GitLab](https://gitlab.com/phikai/htpc-docker-standup). This repo will remain on Github as a mirror.

---
This is a simple docker-compose configuration to standup a new HTPC. It's based on running on an Ubuntu server, but could easily be adapted for other opertaing systems with Docker support.

It includes the following Services

- [Plex Media Server](https://www.plex.tv/) - for managing media and serving files to Plex Clients
- [Deluge](https://deluge-torrent.org/) + [Private Internet Access](https://www.privateinternetaccess.com/pages/buy-vpn/toz) or [TorGuard](https://torguard.net/aff.php?aff=4350) - for downloading torrents... "safely"
- [Sonarr](https://sonarr.tv/) - for TV Series Management
- [Radarr](https://radarr.video/) - for Movie Management
- [Lidarr](https://lidarr.audio/) - for Music Management
- [Readarr](https://readarr.com/) - for eBook Management
- [Calibre](https://calibre-ebook.com/) - for eBook Management
- [Jackett](https://github.com/Jackett/Jackett) - for Torrent Tracker feeds
- [Tautulli](http://tautulli.com/) - for Plex library statistics and usage
- [Ombi](https://ombi.io/) - for requesting additional library content
- [Portainer](https://portainer.io/) - for managing all of your Docker containers
- [Watchtower](https://github.com/v2tec/watchtower) - for automatically updating running containers
- [NetData](https://my-netdata.io/) - for system resource monitoring
- [Muximux](https://github.com/mescon/Muximux) - for simple web based management
- [Duplicati](https://www.duplicati.com/) - for backing up data to your favorite provider
- [InfluxDB](https://www.influxdata.com/) - for time series based database storage
- [Chronograf](https://www.influxdata.com/time-series-platform/chronograf/) - for making pretty dashboards out of the database data
- [SpeedTest](https://github.com/sivel/speedtest-cli/) - for performing a speedtest and posting data to the database
- [Varken](https://github.com/Boerderij/Varken) - for aggregating data from the Plex ecosystem into InfluxDB using Grafana for a frontend
- [Grafana](https://grafana.com/) - for making dashboards out of the Varken Data
- [Traefik](https://traefik.io/) - Reverse Proxy and SSL Support

This project was heavily inspired by the [MediaBox](https://github.com/tom472/mediabox) project... Many Thanks!


## Recomended Folder Structure

| NOTE |
| ----------- |
| `data` is used for root in the below example but you can choose your own as there is a ENV variable exposed called "`${ROOT_FOLDER_PATH}`". |

#### data/shared
```
├── downloads
├── media
├── scratch
├── scratchNAS
├── torrents-manual
└── watch
```
#### data/shared/downloads
```
├── complete
│   ├── blackhole
│   │   ├── books
│   │   ├── movies
│   │   ├── movies4k
│   │   ├── music
│   │   ├── tv
│   │   └── tv4k
│   ├── nzb
│   │   ├── books
│   │   ├── generic
│   │   ├── movies
│   │   ├── movies4k
│   │   ├── music
│   │   ├── tv
│   │   └── tv4k
│   └── torrents
│       ├── movies
│       ├── tv
│       └── tv4k
└── incomplete
    ├── nzb
    └── torrents
```
#### data/shared/media
```
├── AudioBooks
├── Books
├── Comics
├── DVR
│   ├── Movies
│   └── TV
├── HomeVideos
├── Movies
│   ├── ANIME
│   ├── DOCS
│   ├── HD
│   ├── KIDS
│   ├── NEW
│   ├── SD
│   ├── SHORT
│   ├── STAND-UP
│   └── UNKNOWN
├── Movies4K
├── Music
├── MusicVideos
├── Pictures
├── Trailers
├── Tutorials
├── TV
│   ├── ANIME
│   ├── CONTINUING
│   ├── DOCS
│   ├── ENDED
│   ├── KIDS
│   ├── NEW
│   └── STAND-UP
├── TV4K
├── Videos
└── VideosNAS
```

## Known Issues
- [ ] Sometimes the Deluge + VPN Container disconnects and can't re-establish a forwarded port connection. 


## Install Instructions

### Prerequisites
- [Ubuntu 16.04 LTS](https://www.ubuntu.com/)
- VPN Account from [PIA](https://www.privateinternetaccess.com/pages/buy-vpn/toz) or [TorGuard](https://torguard.net/aff.php?aff=4350)
- [Git](https://git-scm.com/)
- [Docker](https://www.docker.com/)
- [Python 2.7](https://www.python.org/)
- [Docker-Compose](https://docs.docker.com/compose/)

### Server Configuration
1. [Install Ubuntu Server](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-server) and get it updated - `sudo apt update` then `sudo apt upgrade`
2. If you have existing media on a separate NTFS formatted drive:
 - Mount NTFS Drive w/ Appropriate Permissions, [Link 1](https://askubuntu.com/questions/113733/how-do-i-correctly-mount-a-ntfs-partition-in-etc-fstab#comment684545_113746)
 - `sudo blkid` - Find the block ID of your media drive
 - `sudo mkdir /media/Media` - make the mount point
 - Add to `/etc/fstab`: `UUID=BC6617A366175D88 /media/Media ntfs-3g uid=1000,gid=1000,dmask=022,fmask=133 0 0`
3. [Setup Samba Share](https://help.ubuntu.com/community/How%20to%20Create%20a%20Network%20Share%20Via%20Samba%20Via%20CLI%20%28Command-line%20interface/Linux%20Terminal%29%20-%20Uncomplicated%2C%20Simple%20and%20Brief%20Way%21) (to access media outside of HTPC)
4. [Install Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository)
 - You'll need to add your user running docker to the `docker` group. See [Post-installation steps for Linux](https://docs.docker.com/install/linux/linux-postinstall/)
5. [Install Docker Compose](https://docs.docker.com/compose/install/#install-compose)
6. Clone Repo `git clone https://gitlab.com/phikai/htpc-docker-standup.git`
7. Copy Sample Environement File `cp sample.env .env`
8. Edit `.env` to match your environment
9. Run Docker Environment `docker-compose up -d`
10. ???
11. Profit.

### Environment File

#### -- Version Tags --
- `DEFAULT_TAG` - Defaults to the `latest` tag.
- `PLEX_TAG` - Which version of Plex Media Server, available options: `latest`, `plexpass` and `public`. You should use `latest` unless you have special requirements or are a Plex Pass subscriber.
- `SONARR_TAG` - Which version of Sonarr, available options: `latest` (currently v2), `5.14` (latest but run on Mono 5.14), `develop` (currently v2) and `preview` (currently v3). You should use `preview` as the v3 builds are now relativel stable and are transitioning now from alpha to beta release.
- `RADARR_TAG` - Which version of Radarr, available options: `latest`, `5.14` (latest but run on Mono 5.14), `nightly` and `preview` (v3 builds). You should use `preview` as the v3 builds are now relativel stable and are transitioning now from alpha to beta release.
- `LIDARR_TAG` - Which version of Lidarr, available options: `latest` (Stable Lidarr releases) and `preview` (Nightly Lidarr Releases).
- `CALIBRE_TAG` - Which version of Calibre (Web), available options: `latest` (Stable Calibre (Web) releases).
- `OMBI_TAG` - Which version of Ombi, available options: `latest` (Stable Ombi releases), `development` (Releases from the develop branch), and `v4-preview`.
- `TRAEFIX_TAG` - Currently set to `v1.7.16` due to breaking change in 2.x

---
- `READARR_TAG` - Which version of Readarr, available options: `latest` (UNAVAILABLE), `stable` (UNAVAILABLE), `untable` (AVAILABLE). it is recommended to use specific build tags. Latest being `unstable-0.1.0.136`.

| WARNING |
| ----------- |
| `READARR` updates require a fresh database until further notice. There's only a unstable tag for the moment. |
---


#### -- Containter / Path Name Version --
- `SONARR_VERSION=` - Sets both the container + volume path name suffix e.g. v3 (sonarr-v3)
- `RADARR_VERSION=` - Sets both the container + volume path name suffix e.g. v3 (radarr-v3)
- `OMBI_VERSION=` - Sets both the container + volume path name suffix e.g. v4 (ombi-v4)


#### -- Global Settings --
- `ROOT_FOLDER_PATH` - Sets the ROOT path or all volume paths. Currently set to `data`.
- `LOCALUSER=` - This is the local user of your linux account and account running docker
- `HOSTNAME=` - Hostame of the server, can be found by executing `hostname` from command line
- `IP_ADDRESS=` - Local IP Address of the server, should be static
- `PUID=` - UID of the local user, can be found by executing `id` from the command line
- `PGID=` - GID of the local user, can be found by executing `id` from the command line
- `PMSTOKEN=` - The claim token for the server to obtain a real server token. If not provided, server is will not be automatically logged in. If server is already logged in, this parameter is ignored. You can obtain a claim token to login your server to your plex account by visiting [https://www.plex.tv/claim](https://www.plex.tv/claim)
- `VPNUNAME=` - Your VPN username from [PIA](https://www.privateinternetaccess.com/pages/buy-vpn/toz) or [TorGuard](https://torguard.net/aff.php?aff=4350)
- `VPNPASS=` - Your VPN password from [PIA](https://www.privateinternetaccess.com/pages/buy-vpn/toz) or [TorGuard](https://torguard.net/aff.php?aff=4350)
- `VPNPROVIDER=` - Your VPN provider, name must match a folder specified in `ovpn`. This defaults to `pia` if you copied `sample.env`.
- `VPN_REMOTE=` - The remote server you want to connect to (must support port forwarding)
- `CIDR_ADDRESS=` - IP/netmask entries which allow access to the server without requiring authorization. We recommend you set this only if you do not sign in your server. For example `192.168.1.0/24,172.16.0.0/16` will allow access to the entire `192.168.1.x` range and the `172.16.x.x`
- `TZ=` - Set the timezone inside the container. For example: `Europe/London`. The complete list can be found here: [https://en.wikipedia.org/wiki/List_of_tz_database_time_zones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)
- `EMAIL=` - Email address to be used for Let's Encrypt SSL certificate validation - `someone@somewhere.com`
- `DOMAIN=` - Public domain to use for accessing services via a public domain - `server.domain.com`
- `WATCHTOWER_EMAIL=` - Email address you'd like Watchtower to notify for any notifications - `someone@somewhere.com`
- `SMTP_FROM=` - From address that your SMTP server uses to send email - `someone@somewhere.com`
- `SMTP_SERVER=` - Servername of your SMTP server - `smtp.domain.com`
- `SMTP_PORT=` - Port that your SMTP server uses to connect - `587`
- `SMTP_USER=` - Username that your SMTP server uses to authenticate
- `SMTP_PASS=` - Password for your SMTP user to authenticate
- `SPEEDTEST_INTERVAL=` - Number of seconds between tests to the [Speedtest.net](http://www.speedtest.net/) services
- `TRAEFIK_AUTH=` - Basic Auth for the Traefik Admin [htpasswd Generator](http://www.htaccesstools.com/htpasswd-generator/)
- `STACK_NAME=` - This is used to specify the appropriate network Traefik should use. See #13 for details.

### Email/SMTP Service
- [Mailgun](https://documentation.mailgun.com/en/latest/quickstart.html) has an excellent QuickStart Guide
- Check out the sending via [SMTP](https://documentation.mailgun.com/en/latest/quickstart-sending.html#send-via-api)
- Make sure to also [verify your domain](https://documentation.mailgun.com/en/latest/quickstart-sending.html#verify-your-domain)

---

## Tips and Tricks

- [PIA List of Servers](https://helpdesk.privateinternetaccess.com/hc/en-us/articles/219460187-How-do-I-enable-port-forwarding-on-my-VPN-) that support port forwarding
- [Deluge + PIA FAQ](https://lime-technology.com/forums/topic/44108-support-binhex-general/?tab=comments#comment-433613)
- [Removing Old/Completed Torrents from Deluge](https://www.cuttingcords.com/home/2015/2/4/auto-deleting-finished-torrents-from-deluge)
- [Create Series Folder in Sonarr](https://forums.sonarr.tv/t/adding-new-series-path-issues/2751/2)
- [Proper Encoding of the Portainer Password](https://github.com/portainer/portainer/issues/1506)
- Open Shell in a Container - [Link 1](http://phase2.github.io/devtools/common-tasks/ssh-into-a-container/), [Link 2](https://stackoverflow.com/a/30173220)

[Potential Script to setup, renew and copy SSL for Plex](https://www.npcglib.org/~stathis/blog/2017/05/13/plex-media-server-over-https-with-letsencrypt-certificates/)

---

If this project has helped you in anyway, and you'd like to say thanks...

[![Donate](https://img.shields.io/badge/Donate-SquareCash-brightgreen.svg)](https://cash.me/$phikai)
[![Donate with Bitcoin](https://en.cryptobadges.io/badge/micro/15JCkpHhjjVmWYaTBc2fJn4tcKHEd194gY)](https://en.cryptobadges.io/donate/15JCkpHhjjVmWYaTBc2fJn4tcKHEd194gY)

You can also [gift a Plex Pass](https://www.plex.tv/plex-pass/gift/) subscription as a great way to show your appreciation.

_AFFILIATE DISCLOSURE: You can also support this project by purchasing a VPN Subscription via one of the links in this README._

---

# Disclaimer

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
