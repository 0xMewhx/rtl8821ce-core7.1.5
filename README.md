# rtl8821ce-core7.1.5

Fork of [tomaspinho/rtl8821ce](https://github.com/tomaspinho/rtl8821ce), patched for Linux kernel **7.1.5+** (tested on Kali).

Upstream no longer compiles on recent kernels — and its packet injection never worked at all (the author stated monitor mode would *never* be supported, then archived the repo). This fork fixes **both**: it builds on 7.1.5+ and injection actually works.

## Fixes

- **Build on kernel 7.1.5+** — updated cfg80211 callbacks for the new `struct wireless_dev`-based API
- **Packet injection in monitor mode** — `cfg80211_rtw_change_iface()` now calls `rtw_indicate_connect()` when switching to monitor. Without it the TX path never comes up and injection sits at 0%

Changed files: `Makefile`, `os_dep/linux/ioctl_cfg80211.c`

## Known issues

`iw dev wlan0 set type monitor` prints `command failed: Operation not supported (-95)`.
**Harmless** — the mode still applies. Just bring the interface up and verify with `aireplay-ng --test`:

```
21:58:28  Trying broadcast probe requests...
21:58:29  Injection is working!
21:58:41  Ping (min/avg/max): 5.578ms/15.970ms/32.048ms Power: -54.33
21:58:41  30/30: 100%
```

## Install

```bash
git clone https://github.com/0xMewhx/rtl8821ce-core7.1.5.git
cd rtl8821ce-core7.1.5
make
sudo make install
sudo modprobe -r 8821ce && sudo modprobe 8821ce
```

Or via DKMS: `sudo ./dkms-install.sh`

## Monitor mode + injection

```bash
sudo iwconfig wlan0 mode monitor    # or: sudo iw dev wlan0 set type monitor (prints -95, ignore)
sudo ip link set wlan0 up
sudo aireplay-ng --test wlan0
```

## Tested

- Kernel `7.1.5+kali-amd64` (Kali)
- `aireplay-ng --test`: 27/30 (90%) – 30/30 (100%) — injection confirmed working

## License

GPL-2.0, same as upstream. See `COPYING`.
