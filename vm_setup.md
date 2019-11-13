## Introduction

### Assumptions

Basic VM setup knowledge and Ubuntu install experience

Create a Ubuntu Desktop VM with the at least 2 CPU Cores, 4GB of RAM and 32GB of disk space.  I recommend 4 CPU Cores, 8GB of RAM and 64GB of disk

## Steps to create an image with the GNU RISC V toolchain

### Install prerequisites and set PATH

- sudo apt-get update; sudo apt-get dist-upgrade -y

- sudo apt-get install tmux htop build-essential curl git

- sudo apt-get install autoconf automake autotools-dev libmpc-dev libmpfr-dev libgmp-dev gawk bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev

- pico \~/.profile
    - (add following line to end of file)
    - PATH="$PATH:/opt/riscv/bin"

- sudo reboot

### Clone the GNU toolchain repo

- cd \~

- mkdir projects

- cd projects

- git clone --recursive https://github.com/riscv/riscv-gnu-toolchain

### Build newlib compiler

- cd \~/projects/riscv-gnu-toolchain/

- ./configure --prefix=/opt/riscv

- sudo make -j$(nproc)

### Build multilib compiler

- cd \~/projects/riscv-gnu-toolchain/

- ./configure --prefix=/opt/riscv --enable-multilib

- sudo make linux -j$(nproc)

