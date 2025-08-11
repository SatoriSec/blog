---
title: "ASUS G14 Arch Linux Cool Mode"
date: 2025-08-11
summary: "Simple setup to keep ASUS G14 cool by using Integrated GPU and lowering CPU frequency."
tags:
  - arch-linux
  - asus
  - zephyrus-g14
  - laptop
  - power-management
---

I was never a gamer. But I bought a gaming laptop (ASUS ROG Zephyrus G14) because it had good specs for personal stuff.  
I didn’t realize it would run hot even when I was just coding or doing normal stuff.  

<!-- more -->
So I was researching for ways to make it run cooler and the below settings could help to some extent so want to try for a few days.

- Turn off the NVIDIA GPU and use the integrated graphics.  
- Use a quiet power profile.  
- Lower the CPU max frequency.  

This is what I set up on Arch Linux.

---

## Install tools

```bash
sudo pacman -Syu
sudo pacman -S asusctl supergfxctl cpupower
sudo systemctl enable --now supergfxd asusd cpupower
```

---

## Script to apply settings

`/usr/local/bin/g14-coolmode.sh`:

```bash
#!/usr/bin/env bash
set -e

# Integrated GPU
if [ -f /etc/supergfxd.conf ]; then
  sudo sed -i 's/^mode=.*/mode=Integrated/' /etc/supergfxd.conf || true
fi
if systemctl is-active --quiet supergfxd; then
  supergfxctl --mode Integrated || true
fi

# Quiet profile
asusctl profile --set quiet || true

# CPU settings
for f in /sys/devices/system/cpu/cpu*/cpufreq/energy_performance_preference; do
  [ -w "$f" ] && echo power | sudo tee "$f" >/dev/null
done
for f in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
  [ -w "$f" ] && echo powersave | sudo tee "$f" >/dev/null
done
for f in /sys/devices/system/cpu/cpu*/cpufreq/scaling_max_freq; do
  [ -w "$f" ] && echo 2200000 | sudo tee "$f" >/dev/null
done
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/g14-coolmode.sh
```

---

## Systemd service

`/etc/systemd/system/g14-coolmode.service`:

```ini
[Unit]
Description=G14 Cool Mode
After=supergfxd.service asusd.service
Wants=supergfxd.service asusd.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/g14-coolmode.sh
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Enable it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now g14-coolmode.service
```

---

## Check after reboot

```bash
supergfxctl --get        # Integrated
asusctl profile --get    # quiet
cpupower frequency-info  # powersave, max ~2.2GHz
nvidia-smi               # No devices found
```

Will update on how it goes in a few days.
