# Slideshow Kiosk Ansible Role

An Ansible role to set up a Debian-based picture frame/slideshow kiosk using a minimal Openbox window manager, shell-level xinit autologin, feh image viewer for slideshow, and optional Chromium kiosk mode. Supports Samba synchronization for remote picture updates.

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
- `kiosk_slideshow_filelist`: Absolute path for the `feh` filelist file (default: `"/home/{{ kiosk_user }}/feh-screensaver.filelist"`). Generated automatically by the role.
- `kiosk_slideshow_image_dir`: Directory within the kiosk user's home containing slideshow images (default: `"Pictures"`).
- `kiosk_slideshow_orientation`: Filter images by orientation before building the filelist. Options: `"landscape"`, `"portrait"`, `"all"` (default: `"all"`).
- `kiosk_chromium_enabled`: Enable Chromium kiosk mode (default: `false`)

### Samba Sync Variables

- `kiosk_samba_enabled`: Enable Samba synchronization (default: `false`)
- `kiosk_samba_share`: Samba share path (default: `""`)
- `kiosk_samba_mount_point`: Local mount point for Samba share (default: `/mnt/kiosk-smb`)
- `kiosk_samba_sync_schedule`: Cron schedule for sync (default: `"*/6 * * * *"`)
- `kiosk_samba_rsync_options`: Rsync options for sync (default: `"-av --delete-after"`)

## Dependencies

None.

## Installation and Setup

### How It Works

This role sets up an extremely minimal graphical environment:

1. **No Display Manager**: Instead of using LightDM or similar, the kiosk user's shell is configured to automatically start X via xinit when logging in.
2. **Shell-Level Autologin**: The `.bash_login` file contains logic to detect if X is not running and automatically execute `startx`.
3. **X Initialization**: The `.xinitrc` file is executed by `startx` and handles:
   - Disabling screen blanking via `xset s off` and `xset -dpms`
   - Starting Openbox window manager
   - Launching unclutter to hide the mouse
   - Starting the feh screensaver script
   - Launching Chromium (if enabled)
4. **Openbox Configuration**: A minimal `rc.xml` file provides basic window management. No panel, desktop icons, or unnecessary decorations.

### TTY Autologin (Optional)

For true "power on → kiosk runs" behavior on the console, you may want to configure the console TTY to auto-login the kiosk user. This requires additional system configuration (e.g., agetty or systemd-logind) and is outside the scope of this role. The role configures shell-level autologin, which works after a user is logged in to the console.

### Verification

After running the role:

1. SSH to the target system as the kiosk user
2. Run `startx` to manually test the X session
3. Verify:
   - Openbox starts (blank desktop, no panel)
   - Mouse cursor is hidden
   - Chromium appears (if enabled) in kiosk mode
   - Feh screensaver runs after idle timeout
   - Screen does not blank or sleep

For full autologin testing, console access is required to reboot the system.

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

- Minimal Openbox window manager (lightweight, panelless)
- Shell-level xinit autologin (no display manager overhead)
- Idle-based slideshow using feh
- Optional Chromium kiosk mode
- Samba/CIFS mount and sync for remote pictures
- Unclutter to hide mouse cursor
- Power management via xset (no screen blanking/DPMS)

## Issues and fixes:

## pi zero 1 needs extra settings
# /etc/X11/Xwrapper.config
#   allowed_users=anybody
#   needs_root_rights=yes
#
# usermod -aG tty,video,input,render kiosk
#
# /etc/X11/xorg.conf.d/99-fbdev.conf
#   Section "Device"
#       Identifier "Framebuffer"
#       Driver     "fbdev"
#       Option     "fbdev" "/dev/fb0"
#   EndSection 

## Author

dirjax

## LICENSE

enjoy
