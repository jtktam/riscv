## Steps for Raspberry Pi 3 as openOCD interface

### Assumptions

** none of the ubuntu images will work **

I only got it working with https://www.raspberrypi.org/downloads/raspbian/

### Install prerequisites and set PATH

- sudo apt-get update; sudo apt-get dist-upgrade -y

- sudo apt-get install tmux htop build-essential curl git

- sudo apt-get install autoconf libtool libusb-dev

- pico \~/.profile
    - (add following line to end of file)
    - PATH="$PATH:/opt/riscv/bin"

- sudo reboot

### Build openOCD

- cd \~

- mkdir projects

- cd \~/projects

- git clone --recursive https://github.com/riscv/riscv-openocd.git

- cd riscv-openocd

- ./bootstrap

- ./configure --prefix=/opt/riscv --enable-bcm2835gpio

- make -j$(nproc)

- sudo make install

- cd \~/projects

- mkdir lofive_openocd

- cd lofive_openocd

- create the config files below (rpi1.cfg, rpi2.cfg, rpi3.cfg, lofive.cfg, lofive_rpi3.cfg)

- run openOCD like
    - sudo /opt/riscv/openocd -f lofive_rpi3.cfg

## openOCD sample config files

### rpi1.cfg

```
# rpi1.cfg: OpenOCD interface on RPi zero and v1

# Use RPi GPIO pins
interface bcm2835gpio

# Base address of I/O port
bcm2835gpio_peripheral_base 0x20000000

# Clock scaling
bcm2835gpio_speed_coeffs 113714 28

# POWER              5V GND
# Header pin numbers 2  6

# SWD                swclk swdio
# Header pin numbers 22    18
bcm2835gpio_swd_nums 25    24

# JTAG                tck tms tdi tdo
# Header pin numbers  22  18  16  15 
bcm2835gpio_jtag_nums 25  24  23  22

# RESET               trst
# Header pin numbers  11
bcm2835gpio_trst_num  17
reset_config trst_only
```

### rpi2.cfg

```
# rpi2.cfg: OpenOCD interface on RPi v2+

# Use RPi GPIO pins
interface bcm2835gpio

# Base address of I/O port
bcm2835gpio_peripheral_base 0x3F000000

# Clock scaling
bcm2835gpio_speed_coeffs 146203 36

# POWER              5V GND
# Header pin numbers 2  6

# SWD                swclk swdio
# Header pin numbers 22    18
bcm2835gpio_swd_nums 25    24

# JTAG                tck tms tdi tdo
# Header pin numbers  22  18  16  15 
bcm2835gpio_jtag_nums 25  24  23  22

# RESET               trst
# Header pin numbers  11
bcm2835gpio_trst_num  17
reset_config trst_only
```

### rpi3.cfg

```
# rpi3.cfg: OpenOCD interface on RPi v3+

# Use RPi GPIO pins
interface bcm2835gpio

# Base address of I/O port
bcm2835gpio_peripheral_base 0x3F000000

# Clock scaling
bcm2835gpio_speed_coeffs 194938 48

# POWER              5V GND
# Header pin numbers 2  6

# SWD                swclk swdio
# Header pin numbers 22    18
bcm2835gpio_swd_nums 25    24

# JTAG                tck tms tdi tdo
# Header pin numbers  22  18  16  15 
bcm2835gpio_jtag_nums 25  24  23  22

# RESET               trst
# Header pin numbers  11
bcm2835gpio_trst_num  17
reset_config trst_only
```

### lofive.cfg

```
set _CHIPNAME riscv
jtag newtap $_CHIPNAME cpu -irlen 5 -expected-id 0x10e31913

set _TARGETNAME $_CHIPNAME.cpu
target create $_TARGETNAME riscv -chain-position $_TARGETNAME
$_TARGETNAME configure -work-area-phys 0x80000000 -work-area-size 10000 -work-area-backup 1

flash bank onboard_spi_flash fespi 0x20000000 0 0 0 $_TARGETNAME
init
if {[ info exists pulse_srst]} {

  sleep 1500
}
halt
```

### lofive_rpi3.cfg 

```
bindto 0.0.0.0

source rpi3.cfg

transport select jtag

adapter_khz 1000

source lofive.cfg
```
