# op8pro-uboot-ci

CI that builds HakiOS's U-Boot for the OnePlus 8 Pro 5G (IN2020, `instantnoodlep`, SM8250 "kona") and
keeps the result as an artifact.

It replicates, step for step, what the HakiOS base repository does with `uefi/u-boot/apply.sh` and
`uefi/u-boot/build.sh`:

1. clone U-Boot at the release in `uefi/u-boot/BASE` (`v2026.07`; from GitHub's official mirror),
2. apply `uefi/u-boot/hakios.patch` (the `hakios` command and the Qualcomm USB device protocol),
3. copy the common files (`uefi/u-boot/files/`) and the device files of every device on top
   (`devices/*/u-boot/files/`: dts, defconfig, environment),
4. `make CROSS_COMPILE=aarch64-linux-gnu- hakios_<device>_defconfig && make -j`.

Jobs:

- **oneplus8pro** — the device files live in [ilmilm/hakios-oneplus8pro](https://github.com/ilmilm/hakios-oneplus8pro)
  at `u-boot/files/` (`configs/hakios_oneplus8pro_defconfig`,
  `dts/upstream/src/arm64/qcom/sm8250-oneplus-instantnoodlep.dts`,
  `board/qualcomm/hakios-oneplus8pro.env`). Produces `u-boot.bin`.
- **qemu** — `devices/qemu/u-boot/files/` of the base repository, as a sanity check that the patched
  tree itself still compiles (and where the chain is actually exercised, see the HakiOS docs'
  `make qemu-u-boot`).

Nothing is flashed anywhere and nothing here runs against a phone; this is a compile check only.
The phone flow (ABL `fastboot boot` → U-Boot from RAM → the HakiOS loader) is described in the
HakiOS documentation under `docs/u-boot.md`.
