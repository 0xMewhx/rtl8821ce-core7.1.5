# rtl8821ce-core7.1.5

Fork of [rtl8821ce](https://github.com/tomaspinho/rtl8821ce) driver, patched to build and work on Linux kernel 7.1.5+ (tested on Kali).

Original driver didn't compile on newer kernels — API changes broke the build and packet injection was broken. This fork fixes both.

## What's changed

- `Makefile` — fixed build rules for kernel 7.1+ (kernel API changes)
- `core/rtw_debug.c` — adapted to new kernel debug API
- `core/rtw_mlme_ext.c` — fixed MLME extension compatibility
- `hal/hal_com.c` — hardware abstraction layer fixes for new kernel
- `os_dep/linux/ioctl_cfg80211.c` — cfg80211 ioctl compatibility, packet injection fix
- `monitorfix.patch` — patch for monitor mode issues

## Known issues

- `iw wlan0 set type monitor` fails with `command failed: Operation not supported (-95)` on kernel 7.1.5.
  Use `iwconfig wlan0 mode monitor` instead — works reliably.

## Install

```bash
git clone git@github.com:0xMewhx/rtl8821ce-core7.1.5.git
cd rtl8821ce-core7.1.5
make
sudo insmod 8821ce.ko
```

Or via DKMS: `sudo ./dkms-install.sh`

## Tested

- Kernel 7.1.5+kali-amd64
- Packet injection confirmed working via `aireplay-ng --test` (90-100% success rate)

## License

GPL, same as original driver. See `COPYING`.

## Key fix detail

`cfg80211_rtw_del_key()` in `os_dep/linux/ioctl_cfg80211.c` used the old
`del_key(wiphy, netdev, ...)` callback signature. Kernel 7.1+ cfg80211 API
passes `struct wireless_dev *wdev` instead of `struct net_device *netdev`.
Fixed by changing the parameter to `wdev` and deriving `netdev` from
`wdev->netdev` inside the function body, so the rest of the function logic
didn't need touching.
