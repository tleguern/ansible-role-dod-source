# Ansible Role: Day of Defeat: Source

[![builds.sr.ht status](https://builds.sr.ht/~tleguern/ansible-role-dod-source.svg)](https://builds.sr.ht/~tleguern/ansible-role-dod-source?)

An Ansible role that installs and configures a Day of Defeat: Source dedicated server.

The game server is downloaded thought Steam and exposed as a systemd service for easier management.
Using this role it is possible to publish a minimalist server with custom maps.

Automatic testing is provided using molecule's delegated driver and <https://builds.sr.ht>.

## Requirements

An ansible role dedicated to the installation of SteamCMD such as [ansible-steamcmd](https://github.com/tleguern/ansible-steamcmd).
This role should provide the `steam_home` variable, pointing to such a folder as `/home/steam/Steam` or `/home/steam/.steam` depending on your operating system.

## Role Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `steamcmd_user` | User name for steamcmd | `steam` |
| `steamcmd_bin` | Path to the steamcmd executable | `/usr/games/steamcmd` |
| `dod_source_motd` | HTML text for the server's MOTD | See below |
| `dod_source_mapcycle` | List of maps for the rotation | See below |
| `dod_source_server_cfg` | Server configuration | See below |
| `dod_source_port` | Network port | `27015` |
| `dod_source_ip` | IP address to listen on | `0.0.0.0` |
| `dod_source_extra_mapcycles` | Configuration of extra mapcycle | See below |
| `dod_source_extra_maps_directory` | Directory containing extra maps, resource and materials sub folders | `""` |

### `dod_source_motd`

The MOTD is a welcome text displayed by the server during the first connexion of a player.
It is formatted in HTML.

Default value:

```html
  <html>
  <head>
    <title>Day of Defeat: Source</title>
  </head>
  <body scroll="no">
    <pre>Welcome.</pre>
  </body>
  </html>
```

### `dod_source_mapcycle`

The mapcycle file is a simple list of maps loaded on the server.

There is no default value.

Example:

```
dod_flash
dod_anzio
```

### `dod_source_server_cfg`

The server.cfg file is the main configuration file for the server.
It holds the server name, the administrator password as well as various rules.

There is no default value.

Example:

```
hostname "My Day of Defeat Server"
rcon_password mypassword
mp_friendlyfire 1
```

### `dod_source_extra_mapcycles`

A list of hashes containing two keys: `name` and `content`.
The first will be used as a prefix to form a new mapcycle file named `mapcycle_{{ name }}.txt` and containing `{{ content }}`.

Example:

```
dod_source_extra_mapcycles:
  - name: officials
    content: |
      dod_anzio
      dod_argentan
      dod_avalanche
      ...
  - name: extra
    content: |
      dod_anemia
      dod_anvil
      dod_farmers_b1
      ...
  - name: fun
    content: |
      dod_dust2_slg
      ...
```

These files can later be used with rcon, using the `mapcyclefile` CVAR.

## Dependencies

The `acl` package should be installed on the server.

## Example Playbook

```yaml
- hosts: game
  vars:
    dod_source_extra_maps_directory: "files/dod-source"
    dod_source_server_cfg: |
      hostname "My Server"
      sv_password password
      rcon_password rconpassword
    dod_source_mapcycle: |
      dod_library
      dod_anvil
  pre_tasks:
    - package:
        name: acl
        state: present
  roles:
    - role: ansible-steamcmd
    - role: ansible-role-dod-source
```

## License

ISC

## Contributing

Either send [send GitHub pull requests](https://github.com/tleguern/ansible-role-dod-source) or [send patches on SourceHut](https://lists.sr.ht/~tleguern/misc).

## Author Information

Tristan Le Guern <tleguern@bouledef.eu>
