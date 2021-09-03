**Build Raspberry Pi 4 64bit kernel

1. Install pre requisites

    ```
    sudo apt install git bc bison flex libssl-dev make git bc bison flex libssl-dev make libc6-dev libncurses5-dev crossbuild-essential-arm64 libncurses5-dev raspberrypi-kernel-headers
    ```

2. Grab kernel sources

    ```
    cd /opt
    mkdir -p /opt/kernel64
    git clone --depth=1 --branch rpi-5.10.y https://github.com/raspberrypi/linux
    ```

3. Refresh kernel sources (for update previous builds)

    ```
    git pull
    ```

4. Apply the Default Configuration

    ```
    cd linux
    KERNEL=kernel8
    make bcm2711_defconfig
    ```

5. Use specific .config file

    ```
    Backup config file:
        mv .config .config.backup

    Copy fonctionnal config file:
        cp rpi_kernel.config.txt .config
    ```

6. Customising the kernel

    ```
    CONFIG_LOCALVERSION="-v8-K64HP"
    make menuconfig
    ```

7. Backup files

    ```
    sudo mkdir -p /boot/backups
    sudo cp -R /boot/. /boot/backups/
    ```

8. Building the kernel

    ```
    make -j4 Image modules dtbs
    make modules_install
    ```

9. Copy files (install kernel)

    ```
    sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/
    sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/overlays/
    sudo cp arch/arm64/boot/dts/overlays/README /boot/overlays/
    sudo cp arch/arm64/boot/Image /boot/kernel8_k64hp.img
    ```

10. Make kernel use

    ```
    Add at bottom of config.txt file:
        kernel=kernel8_k64hp.img
    ```

11. Bonus, allow Huge Pages at boot time

    ```
    Adjusting cmdline.txt file, add at end of line:
        hugepagesz=1G hugepages=1
    ```
