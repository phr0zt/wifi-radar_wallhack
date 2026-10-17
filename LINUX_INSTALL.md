# WiFi Radar - Linux Installation

## Quick Install (One-Liner)

```bash
curl -fsSL https://raw.githubusercontent.com/KevinB2212/wifi-radar/main/install_linux.sh | bash
```

Or with wget:

```bash
wget -qO- https://raw.githubusercontent.com/KevinB2212/wifi-radar/main/install_linux.sh | bash
```

---

## Manual Installation

### 1. Download Files

```bash
# Download the Python script
curl -O https://raw.githubusercontent.com/KevinB2212/wifi-radar/main/wifi_radar_linux.py

# Make it executable
chmod +x wifi_radar_linux.py
```

### 2. Install Dependencies

**Ubuntu/Debian:**
```bash
sudo apt-get update
sudo apt-get install python3 python3-tk network-manager
```

**Fedora/RHEL:**
```bash
sudo dnf install python3 python3-tkinter NetworkManager
```

**Arch/Manjaro:**
```bash
sudo pacman -S python python-tkinter networkmanager
```

**openSUSE:**
```bash
sudo zypper install python3 python3-tk NetworkManager
```

### 3. Run WiFi Radar

**With NetworkManager (recommended):**
```bash
python3 wifi_radar_linux.py
```

**Without NetworkManager (requires sudo):**
```bash
# Install wireless-tools first
sudo apt-get install wireless-tools  # Ubuntu/Debian
# or
sudo dnf install wireless-tools      # Fedora

# Run with sudo
sudo python3 wifi_radar_linux.py
```

---

## Requirements

- **Linux** (tested on Ubuntu 22.04, Fedora 38, Arch)
- **Python 3.7+**
- **Tkinter** (`python3-tk` package)
- **NetworkManager** (`nmcli` command) **OR** wireless-tools (`iwlist` command)

---

## WiFi Scanning Methods

WiFi Radar supports three methods for scanning WiFi networks, tried in order.

### Method 1: `iw scan` - Recommended, run with sudo
- **Pros:** The only method that actually re-scans the band on every pass, so
  signal readings are fresh. Reports real dBm.
- **Cons:** Needs root, and one sweep takes about 4-5 seconds.
- **Test:** `sudo iw dev <interface> scan`

### Method 2: NetworkManager (nmcli) - Fallback, no sudo
- **Pros:** No sudo required.
- **Cons:** **Readings are stale.** `nmcli dev wifi` reads NetworkManager's
  cache, which only refreshes every couple of minutes. Between refreshes every
  AP except the one you are connected to is frozen, and then they all jump at
  once. Motion detection is poor and false alarms are common. `--rescan yes`
  does not help; NetworkManager throttles it.
- **Test:** `nmcli dev wifi`

### Method 3: wireless-tools (iwlist) - Last resort
- **Pros:** Works without NetworkManager.
- **Cons:** Requires root/sudo, slower.
- **Test:** `sudo iwlist scanning`

The script automatically detects which method to use. **Run it with sudo** for
the real-scan path:

```bash
sudo python3 wifi_radar_linux.py
```

Without root it still runs, but falls back to the cached NetworkManager
readings described above.

---

## Troubleshooting

### "Failed to scan WiFi"

**Check NetworkManager:**
```bash
systemctl status NetworkManager
```

If not running:
```bash
sudo systemctl start NetworkManager
sudo systemctl enable NetworkManager
```

**Check WiFi interface:**
```bash
nmcli device status
```

Your WiFi interface should show "connected" or "disconnected" (not "unavailable").

### "Permission denied"

If using `iwlist`, you need sudo:
```bash
sudo python3 wifi_radar_linux.py
```

Or add your user to the `netdev` group (Ubuntu/Debian):
```bash
sudo usermod -aG netdev $USER
# Log out and back in
```

### "Tkinter not found"

Install the tkinter package:
```bash
sudo apt-get install python3-tk      # Ubuntu/Debian
sudo dnf install python3-tkinter     # Fedora
sudo pacman -S python-tkinter        # Arch
```

---

## How It Works

1. **Baseline Phase** (40 seconds / 20 scans)
   - Records normal WiFi signal strengths
   - Averages each access point's signal level

2. **Monitoring Phase**
   - Scans WiFi every 2 seconds
   - Compares current signals to baseline
   - Detects deviations > 5 dBm

3. **Motion Detection**
   - Requires 2+ access points showing deviation
   - Reduces false positives from single AP fluctuations
   - Visual + audio alerts

---

## Performance

- **CPU Usage:** 5-10% during active scanning
- **Memory:** ~40-50 MB
- **Network:** No internet required (local WiFi scanning only)

---

## Limitations

- **Indoor use recommended** (needs multiple WiFi networks)
- **Not perfect** (can miss slow/subtle movements)
- **Environmental factors** (other people moving, doors opening, etc.)
- **Baseline drift** (signal levels change over time)

---

## Tips

- **More APs = Better detection** (aim for 5+ visible networks)
- **Open spaces work better** (signal bounces more)
- **Avoid metal/concrete barriers** (blocks signals)
- **Recalibrate periodically** (restart monitoring to rebuild baseline)

---

## License

MIT License - See [LICENSE](LICENSE)

---

## Support

- **GitHub Issues:** https://github.com/KevinB2212/wifi-radar/issues
- **Tested on:** Ubuntu 22.04, Fedora 38, Arch Linux, Pop!_OS

---

**Built by Kevin Brennan**  
CS Student @ DCU | Product Support Engineer @ Telnyx
