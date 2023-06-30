# Setup Linux

## Remote Desktop

```bash
sudo apt install xfce4 xrdp
sudo systemctl enable xrdp
```

### Disable Screensaver

```
Applications / Settings / Screensaver
- [ ] Enable Screensaver
- [ ] Lock Screen with Screensaver
```

### Fix Authentication Request

> Authentication is required to create a color profile

```bash
# https://c-nergy.be/blog/?p=12043
sudo nano /etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla
```

```
[Allow Colord all Users]
Identity=unix-user:*
Action=org.freedesktop.color-manager.create-device;org.freedesktop.color-manager.create-profile;org.freedesktop.color-manager.delete-device;org.freedesktop.color-manager.delete-profile;org.freedesktop.color-manager.modify-device;org.freedesktop.color-manager.modify-profile
ResultAny=no
ResultInactive=no
ResultActive=yes
```

### Install Utilities

```bash
sudo apt install xfce4-screenshooter
```

## Korean Locale and Fonts

```bash
sudo dpkg-reconfigure locales
sudo apt install fonts-nanum ibus ibus-hangul
```

```
Applications / Settings / IBus Preferences
Input Method / Korean - Hangul (Remove English)
```

## Support Hardware Keys

```bash
# https://support.yubico.com/hc/en-us/articles/360013708900
# https://github.com/Yubico/libfido2/blob/main/udev/70-u2f.rules
sudo nano /etc/udev/rules.d/70-u2f.rules
```

## DNS

```bash
# https://ubuntu.com/server/docs/network-configuration
# https://manpages.ubuntu.com/manpages/jammy/man8/systemd-resolved.service.8.html
sudo nano /etc/systemd/resolved.conf
```

```
[Resolve]
# Some examples of DNS servers which may be used for DNS= and FallbackDNS=:
# Cloudflare: 1.1.1.1#cloudflare-dns.com 1.0.0.1#cloudflare-dns.com 2606:4700:4700::1111#cloudflare-dns.com 2606:4700:4700::1001#cloudflare-dns.com
# Google:     8.8.8.8#dns.google 8.8.4.4#dns.google 2001:4860:4860::8888#dns.google 2001:4860:4860::8844#dns.google
# Quad9:      9.9.9.9#dns.quad9.net 149.112.112.112#dns.quad9.net 2620:fe::fe#dns.quad9.net 2620:fe::9#dns.quad9.net
#DNS=94.140.14.14
#FallbackDNS=94.140.15.15
```

```bash
resolvectl dns
# Global: 94.140.14.14
```
