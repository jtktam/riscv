## Remote connecting to openOCD

### Assumptions

- openOCD is running at 192.168.1.229:3333

- demo program demo_gpio is already compiled for riscv

### Steps

- riscv64-unknown-elf-gdb demo_gpio

- commands to use while in gdb:

    - set remotetimeout 240
    
    - target extended-remote 192.168.1.229:3333

    - monitor halt

    - monitor flash protect 0 64 last off
    
    - load
    
    - monitor resume