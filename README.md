# riscv_emufun (mini-rv32ima)

## What

mini-rv32ima is a single-file-header, [mini-rv32ima.h](https://github.com/cnlohr/riscv_emufun/blob/master/mini-rv32ima/mini-rv32ima.h), in the [STB Style library](https://github.com/nothings/stb) that:
 * Implements a RISC-V **rv32ima/Zifencei+Zicsr** (and partial su), with CLINT and MMIO.
 * Is about **400 lines** of actual code.
 * Has **no dependencies**, not even libc.
 * Is **easily extensible**.  So you can easily add CSRs, instructions, MMIO, etc!
 * Is pretty **performant**. (~450 coremark on my laptop, about 1/2 the speed of QEMU)
 * Is human-readable and in **basic C** code.
 * Is "**incomplete**" in that it didn't implement the tons of the spec that Linux doesn't (and you shouldn't) use.
 * Is tivially **embeddable** in applications.

It has a [demo wrapper](https://github.com/cnlohr/riscv_emufun/blob/master/mini-rv32ima/mini-rv32ima.c) that:
 * Implements a CLI, SYSCON, UART, DTB and Kernel image loading.
 * And it only around **250 lines** of code, itself.
 * Compiles down to a **~18kB executable** and only relies on libc.

Just see the `mini-rv32ima` folder.

It's "fully functional" now in that I can run Linux, apps, etc.  Compile flat binaries and drop them in an image.

## Why

I'm working on a really really simple C Risc-V emultor. So simple it doesn't even have an MMU (Memory Management Unit). I have a few goals, they include:
 * Furthering RV32-NOMMU work to improve Linux support for RV32-NOMMU.  (Imagine if we could run Linux on the $1 ESP32-C3)
 * Learning more about RV32 and writing emulators.
 * Being further inspired by @pimaker's amazing work on [Running Linux in a Pixel Shader](https://blog.pimaker.at/texts/rvc1/) and having the sneaking suspicion performance could be even better!
 * Hoping to port it to some weird places.
 * Understand the *most simplistic* system you can run Linux on and trying to push that boundary.
 * Continue to include my [education of people about assembly language].(https://www.youtube.com/watch?v=Gelf0AyVGy4)

## How

To test this, you will need a Linux box with `git build-essential` and whatever other requirements are in place for [buildroot](https://buildroot.org/).
 * Clone this repo.
 * `make testdlimage`
 * It automatically downloads the image (~1MB) and runs the emulator.
 * Should be up and running in about 2.5s depending on internet speed.

...And you can almost run Linux in Linux in Linux (though not quite yet).

## Questions?
 * Why not rv64?
   * Because then I can't run it as easily in a pixel shader if I ever hope to.
 * Can I add an MMU?
   * Yes.  It actually probably would be too difficult.
 * Should I add an MMU?
   * No.  It is important to further support for nommu systems to empower minimal Risc-V designs!

Everything else: Contact us on my Discord: https://discord.com/invite/CCeyWyZ

## Hopeful goals?
 * Further drive down needed features to run Linux.
   * Remove need for RV32A extension on systems with only one CPU.
   * Support for relocatable ELF executables.
   * Add support for an unreal UART.  One that's **much** simpler than the current 8250 driver.
 * Maybe run this in a pixelshader too!
 * Get opensbi working with this.
 * Be able to "embed" rv32 emulators in random projects.
 * Can I use early console to be a full system console?
 * Can I increase the maximum contiguous memory allocatable?

## Special Thanks
 * For @regymm and their [patches to buildroot](https://github.com/regymm/buildroot) and help!
   * callout: Regymm's [quazisoc project](https://github.com/regymm/quasiSoC/).
 * Buildroot (For being so helpful).
 * @vowstar and their team working on [k210-linux-nommu](https://github.com/vowstar/k210-linux-nommu).
 * This [guide](https://jborza.com/emulation/2020/04/09/riscv-environment.html)
 * [rvcodecjs](https://luplab.gitlab.io/rvcodecjs/) I probably went through over 1,000 codes here.


## More details

If you want to build the kernel yourself:
 * `make everything`
 * About 20 minutes.  (Or 4+ hours if you're on [Windows Subsytem for Linux 2](https://github.com/microsoft/WSL/issues/4197))
 * And you should be dropped into a Linux busybox shell with some little tools that were compiled here.


If you want to use bare metal to build your binaries so you don't need buildroot, you can use the rv64 gcc in 32-bit mode built into Ubuntu 20.04 and up.
```
sudo apt-get install gcc-multilib gcc-riscv64-unknown-elf make
```


## General notes:
 * QEMU out-of-box.
 * Pi's Linux
 * https://github.com/cnlohr/riscv_emufun/commit/2f09cdeb378dc0215c07eb63f5a6fb43dbbf1871#diff-b48ccd795ae9aced07d022bf010bf9376232c4d78210c3113d90a8d349c59b3dL440
 * Making the kernel assembly to dig through.
 * touch kernel_config && make
 * Debugging process.
 * test-driven-development.
   -> Pitfalls.
 * Talk about converting the style to HLSL
 * People are working on relocatable ELFs.
 * MMIO
 * Background on RV32IMA
 * Being able to run it elsewhere.
 * Mention assembly language. (Baremetal)
 * Talk about Makefiles.
 * Mention license and file at the end - encourage others.


## WSL notes.
 * You can't have spaces in your paths.  Try this:
	`PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/mnt/c/Windows/system32:/snap/bin`




## Attic

(These things don't currently work)

### Building Tests

(This does not work, now)
```
cd riscv-tests
export CROSS_COMPILE=riscv64-linux-gnu-
export PLATFORM_RISCV_XLEN=32
CC=riscv64-linux-gnu-gcc ./configure
make XLEN=32 RISCV_PREFIX=riscv64-unknown-elf- RISCV_GCC_OPTS="-g -O1 -march=rv32imaf -mabi=ilp32f -I/usr/include"
```

### Building OpenSBI

(This does not currently work!)
```
cd opensbi
export CROSS_COMPILE=riscv64-unknown-elf-
export PLATFORM_RISCV_XLEN=32
make
```

### Extra links
 * Clear outline of CSRs: https://five-embeddev.com/riscv-isa-manual/latest/priv-csrs.html
