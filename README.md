# deluge-piaportplugin

A lightweight plugin for [Deluge](https://deluge-torrent.org) designed to pair with [Gluetun](https://github.com/passteque/gluetun).

Containers using dynamic VPN port forwarding need a way to keep Deluge's listening port in sync. This plugin watches Gluetun's forwarded port file and updates Deluge automatically whenever the port changes.

This is a fork of [jawilson/deluge-piaportplugin](https://github.com/jawilson/deluge-piaportplugin/). This forked version polls `/pia/forwarded_port` directly every 60 seconds without relying on Deluge's external port checker.

## Usage

1. Grab the latest `.egg` build from [Releases](https://github.com/nickneos/deluge-piaportplugin/releases).
2. Save `.egg` file in deluge `plugins` folder.
3. Install and enable **PIAPortPlugin** in Deluge via **Preferences -> Plugins -> Install**.
4. Open **Preferences -> Network** and uncheck **Use Random Port**.
5. Configure Gluetun to export its forwarded port file into a volume mounted at `/pia` inside Deluge (making the file accessible at `/pia/forwarded_port`).

   Example Docker Compose setup for Gluetun v3:

   ```yaml
   services:
     gluetun:
       image: qmcgaw/gluetun:v3
       environment:
         - VPN_PORT_FORWARDING=on
         - PORT_FORWARD_ONLY=on
         - VPN_PORT_FORWARDING_STATUS_FILE=/gluetun/forwarded_port
       volumes:
         - ./gluetun:/gluetun

     deluge:
       image: lscr.io/linuxserver/deluge:latest
       network_mode: service:gluetun
       volumes:
         - ./gluetun:/pia:ro
   ```

5. Choose a VPN region that supports port forwarding—refer to [PIA's support docs](https://privateinternetaccess.com) or [binhex's endpoint list](https://gist.github.com/binhex/0d8bc1974bad90f8a61d2d4219ef862c).

## Configuration

Default settings can be adjusted in Deluge under **Preferences -> PIAPortPlugin** (or in `piaportplugin.conf`):

- `port_file`: Path to Gluetun's forwarded port file (default: `/pia/forwarded_port`)
- `poll_interval`: How often to check for port changes in seconds (default: `60`)

