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
