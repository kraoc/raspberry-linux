**Build Raspberry Pi 4 64bit Kernel with Huge Pages**

1. Prerequisites

    Install required packages
    ```
    sudo apt install git bc bison flex libssl-dev make git bc bison flex libssl-dev make libc6-dev libncurses5-dev crossbuild-essential-arm64 libncurses5-dev raspberrypi-kernel-headers
    ```

2. Get kernel sources

    *If you already done this step and only want to update the kernel, then skip to step 3*

    Make a dedicated folder inside the opt path and get kernl sources
    ```
    cd /opt
    mkdir -p /opt/kernel64
    git clone --depth=1 --branch [branch_tag] https://github.com/raspberrypi/linux
    ```
    
    Currently done by
    ```
    git clone --depth=1 --branch rpi-5.10.y https://github.com/raspberrypi/linux
    ```

    **Generate the default kernel configuration**

    ```
    cd linux
    KERNEL=kernel8
    make bcm2711_defconfig
    ```

3. Refresh kernel sources

    Only when you already compiled a kernel and needed update previous builds
    ```
    git pull
    ```

4. Use specific .config file

    Backup config file:
    ```
    mv .config .config.backup
    ```

    Copy fonctionnal config file **5.10-y** currently:
    ```
    cp rpi_kernel.config.txt .config
    ```

5. Customising the kernel

    ```
    CONFIG_LOCALVERSION="-v8-K64HP"
    make menuconfig
    ```

6. Backup files

    *Allow to revert changes in case of problems*
    ```
    sudo mkdir -p /boot/backups
    sudo cp -R /boot/. /boot/backups/
    ```

7. Building the kernel

    This is time consuming, consider approximatrly 2 hour
    ```
    make -j4 Image modules dtbs
    make -j4 modules_install
    ```

8. Copy files (install kernel)

    ```
    sudo cp arch/arm64/boot/dts/broadcom/*.dtb /boot/
    sudo cp arch/arm64/boot/dts/overlays/*.dtb* /boot/overlays/
    sudo cp arch/arm64/boot/dts/overlays/README /boot/overlays/
    sudo cp arch/arm64/boot/Image /boot/kernel8_k64hp.img
    ```

9. Make kernel use

    Add at bottom of /boot/config.txt file
    ```
    kernel=kernel8_k64hp.img
    ```
    This allow to load this specific kernel.
    In case of crash or not booting, just edit config.txt and put a # in front of the line to go back to default kernel.

10. Add Huge Page Filesystem

    Add at bottom of /etc/fstab file
    ```
    hugetlbfs /dev/hugepages hugetlbfs rw,relatime,pagesize=1G 0 0
    ```

11. Bonus, allow Huge Pages at boot time

    Adjusting cmdline.txt file, add at end of line:
    ```
    hugepagesz=1G hugepages=1
    ```
    
    *Not really needed after testing...*

12. Reboot

    Synchronize all disk access
    ```
    sudo sync
    ```
    
    Optionnal, flush all caches
    ```
    sudo echo 3 > /proc/sys/vm/drop_caches
    ```
    
    Then you can reboot your Pi and enjoy it with 64bit kernel + huge pages !

13. Annexes

    You can grab and use *kernel8_k64hp.img.gz* in this directory.
    
    It's a functionnal 64bit kernel v5.10-y compiled on my RPi with hugepage enabled.
    
