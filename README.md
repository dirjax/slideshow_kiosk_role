# Slideshow Kiosk Ansible Role

An Ansible role to set up a Debian-based picture frame/slideshow kiosk using XFCE4 desktop environment, LightDM autologin, feh image viewer for slideshow, and optional Chromium kiosk mode. Supports Samba synchronization for remote picture updates.

## Requirements

- Ansible >= 2.9
- Debian-based system
- python3-apt

## Role Variables

### Default Variables

- `kiosk_user`: The user account for the kiosk (default: `kiosk`)
- `kiosk_chromium_url`: URL to display in Chromium kiosk mode (default: `https://xkcd.com/`)
- `kiosk_idle_ms`: Idle time in milliseconds before starting slideshow (default: `60000`)
- `kiosk_slideshow_delay`: Delay between slides in seconds (default: `120`)
- `kiosk_slideshow_filelist`: Filelist filename or path for `feh` filelist mode. If relative, the path is resolved under the kiosk home directory (default: `"feh-screensaver.filelist"`). The filelist is generated automatically next to `feh-screensaver.sh`.
- `kiosk_slideshow_image_dir`: Directory containing slideshow images (default: `"/home/{{ kiosk_user }}/Pictures"`).
- `kiosk_chromium_enabled`: Enable Chromium kiosk mode (default: `false`)

### Samba Sync Variables

- `kiosk_samba_enabled`: Enable Samba synchronization (default: `false`)
- `kiosk_samba_share`: Samba share path (default: `""`)
- `kiosk_samba_mount_point`: Local mount point for Samba share (default: `/mnt/kiosk-smb`)
- `kiosk_samba_sync_schedule`: Cron schedule for sync (default: `"*/30 * * * *"`)
- `kiosk_samba_rsync_options`: Rsync options for sync (default: `"-av --delete-after"`)

## Dependencies

None.

## Example Playbook

```yaml
- hosts: kiosks
  roles:
    - role: slideshow_kiosk_role
      kiosk_user: mykiosk
      kiosk_chromium_enabled: true
      kiosk_chromium_url: "https://example.com"
      kiosk_samba_enabled: true
      kiosk_samba_share: "//server/share"
```

## Features

- Automatic LightDM login to XFCE4 desktop
- Idle-based slideshow using feh
- Optional Chromium kiosk mode
- Samba/CIFS mount and sync for remote pictures
- Unclutter to hide mouse cursor
- Power management configuration

## Author

dirjax

## LICENSE

enjoy 