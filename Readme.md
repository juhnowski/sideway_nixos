# Introduction
There is [Sideway](https://github.com/RDMA-Rust/sideway) - a wrapper for using RDMA programming APIs.

When you start exploring it, you most likely won't have two physical RDMA adapters to run the [examples](https://github.com/RDMA-Rust/sideway/tree/main/examples) .

This repo proposes software RDMA emulation that will allow debugging Rust code. We will use **QEMU/KVM** with **Soft-RoCE (RXE)** support.

Once you find [Sideway's](https://github.com/RDMA-Rust/sideway) code useful and build a project around it, you'll need to set up a CI/CD pipeline to run integration tests. Our script will also be helpful in this case.

My experience using this solution to develop a global cache for storage was successful and brought positive results, so I decided to share it with [Luke Yue](https://github.com/dragonJACson) to make life easier for contributors and users of his product [Sideway](https://github.com/RDMA-Rust/sideway)

Using [NixOS](https://nixos.org/) on the host is not mandatory, but I chose this operating system because of its advantages:

- **Declarative configuration:** The entire system is described in one or more text configuration files. You specify the desired system state (list of packages, service settings, users), and NixOS automatically configures the system to that state.

- **Atomic updates and rollbacks:** Updates are atomic—if something goes wrong during installation, the system will remain in its previous working state. If an update fails, you can easily revert to any previous system version (generation) directly from the boot menu.

- **Reproducibility:** The same configuration file will create an identical environment on different computers. This makes NixOS ideal for deploying servers and synchronizing settings between home and work PCs.

- **Dependency isolation:** Packages in Nix are stored in isolated directories in /nix/store, eliminating library conflicts ("dependency hell"). Programs only see those dependencies that were explicitly specified during compilation.

The solution was tested on two versions of NixOS:

- **Host #1:** Home Computer
```bash
nix-shell -p fastfetch --run fastfetch
          ▗▄▄▄       ▗▄▄▄▄    ▄▄▄▖             ilya@nixos
          ▜███▙       ▜███▙  ▟███▛             ----------
           ▜███▙       ▜███▙▟███▛              OS: NixOS 26.05 (Yarara) x86_64
            ▜███▙       ▜██████▛               Host: Precision 3640 Tower
     ▟█████████████████▙ ▜████▛     ▟▙         Kernel: Linux 6.18.16
    ▟███████████████████▙ ▜███▙    ▟██▙        Uptime: 48 mins
           ▄▄▄▄▖           ▜███▙  ▟███▛        Packages: 669 (nix-system), 49 (nix-user)
          ▟███▛             ▜██▛ ▟███▛         Shell: bash 5.3.9
         ▟███▛               ▜▛ ▟███▛          Terminal: /dev/pts/4
▟███████████▛                  ▟██████████▙    CPU: Intel(R) Core(TM) i7-10700 (16) @ 4.80 GHz
▜██████████▛                  ▟███████████▛    GPU 1: NVIDIA GeForce RTX 3070 [Discrete]
      ▟███▛ ▟▙               ▟███▛             GPU 2: Intel UHD Graphics 630 @ 1.20 GHz [Integrated]
     ▟███▛ ▟██▙             ▟███▛              Memory: 2.38 GiB / 31.06 GiB (8%)
    ▟███▛  ▜███▙           ▝▀▀▀▀               Swap: Disabled
    ▜██▛    ▜███▙ ▜██████████████████▛         Disk (/): 128.71 GiB / 1.72 TiB (7%) - ext4
     ▜▛     ▟████▙ ▜████████████████▛          Local IP (eno2): 192.168.3.27/24
           ▟██████▙         ▜███▙              Locale: ru_RU.UTF-8
          ▟███▛▜███▙         ▜███▙
         ▟███▛  ▜███▙         ▜███▙
         ▝▀▀▀    ▀▀▀▀▘         ▀▀▀▘
```
- **Host #2:** Huawei server
```bash
nix-shell -p fastfetch --run fastfetch
          ▗▄▄▄       ▗▄▄▄▄    ▄▄▄▖             ilya@nixos
          ▜███▙       ▜███▙  ▟███▛             ----------
           ▜███▙       ▜███▙▟███▛              OS: NixOS 25.11 (Xantusia) x86_64
            ▜███▙       ▜██████▛               Host: FPD-8-SP-H2K288G6-CTO (Whitley)
     ▟█████████████████▙ ▜████▛     ▟▙         Kernel: Linux 6.12.74
    ▟███████████████████▙ ▜███▙    ▟██▙        Uptime: 1 day, 7 hours, 44 mins
           ▄▄▄▄▖           ▜███▙  ▟███▛        Packages: 658 (nix-system), 34 (nix-user)
          ▟███▛             ▜██▛ ▟███▛         Shell: bash 5.3.3
         ▟███▛               ▜▛ ▟███▛          Display (Unknown-1): 1024x768
▟███████████▛                  ▟██████████▙    Terminal: /dev/pts/0
▜██████████▛                  ▟███████████▛    CPU: 2 x Intel(R) Xeon(R) Platinum 8358P (128) @ 3.40 GHz
      ▟███▛ ▟▙               ▟███▛             GPU: Huawei iBMC Intelligent Management system chip w/VGA support
     ▟███▛ ▟██▙             ▟███▛              Memory: 10.44 GiB / 1007.42 GiB (1%)
    ▟███▛  ▜███▙           ▝▀▀▀▀               Swap: Disabled
    ▜██▛    ▜███▙ ▜██████████████████▛         Disk (/): 26.97 GiB / 1.72 TiB (2%) - ext4
     ▜▛     ▟████▙ ▜████████████████▛          Disk (/boot-fallback): 4.00 KiB / 1021.98 MiB (0%) - vfat
           ▟██████▙       ▜███▙                Local IP (eno1): 192.168.2.229/24
          ▟███▛▜███▙       ▜███▙               Locale: ru_RU.UTF-8
         ▟███▛  ▜███▙       ▜███▙
         ▝▀▀▀    ▀▀▀▀▘       ▀▀▀▘
```
NIC:
```bash
[ilya@nixos:~]$ nix-shell -p pciutils --run "lspci | grep -E 'Ethernet|Network'"

this path will be fetched (0.39 MiB download, 1.92 MiB unpacked):
  /nix/store/hnif0bxpp0p4w3h7gdfmaglmgk0dp6x8-pciutils-3.14.0
copying path '/nix/store/hnif0bxpp0p4w3h7gdfmaglmgk0dp6x8-pciutils-3.14.0' from 'https://cache.nixos.org'...
02:00.0 Signal processing controller: Huawei Technologies Co., Ltd. iBMA Virtual Network Adapter (rev 01)
17:00.0 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
17:00.1 Ethernet controller: Intel Corporation I350 Gigabit Network Connection (rev 01)
b1:00.0 Ethernet controller: Mellanox Technologies MT27710 Family [ConnectX-4 Lx]
b1:00.1 Ethernet controller: Mellanox Technologies MT27710 Family [ConnectX-4 Lx]
ca:00.0 Ethernet controller: Mellanox Technologies MT27800 Family [ConnectX-5]
ca:00.1 Ethernet controller: Mellanox Technologies MT27800 Family [ConnectX-5]
```
# Changing the configuration on the host
The following settings need to be merged with your own configuration settings in the `/etc/nixos/configuration.nix` file.

## 1. Preparing the network and kernel modules
Add this to the main server config to enable virtualization and load the RDMA emulation drivers:
```Nix
  boot.kernelModules = [
     "nvmet" 
     "nvmet-tcp"
     "rdma_rxe"
     "ib_uverbs"
     "rdma_ucm"
  ];
virtualisation.libvirtd.enable = true;
```
## 2. Preparing build environment
To successfully build the code, we add dependencies:
```Nix
    environment.systemPackages = with pkgs; [
            rustc
            cargo
            rust-analyzer 
            clippy        
            rustfmt       
            pkg-config    
            openssl       
            gcc
            gnumake
            cmake        
            rdma-core
            libclang 
            ninja    
            python3  
            libnl.dev 
            libclang.dev 
    ];

    environment.variables = {
        LIBCLANG_PATH = "${pkgs.libclang.lib}/lib";
    };

    programs.nix-ld.enable = true;
    programs.nix-ld.libraries = with pkgs; [
       stdenv.cc.cc
       openssl
       curl
       zlib
       libz
       icu
       libgcc
       glibc
   ];
```
*Note*
If you encounter errors while updating the config, you can take a look at my working config in `/example` folder. It's a bit cluttered, but it works. It might help you and shed some light on the issue.

## 2. Preparing the Guests hosts
Flake is used to configure and run virtual machines. All configuration is located in the `flake.nix` file.

The file contains configuration for each of the two nodes, which you can change.
```Nix
            virtualisation.vmVariant.virtualisation.qemu.options = [ 
              # --- Settings for virtiofs ---
              "-m 4096"
              "-object memory-backend-memfd,id=mem,size=4G,share=on"
              "-numa node,memdev=mem"
              # --- Socket vhost-user-fs ---
              "-chardev socket,id=char0,path=/tmp/vfs-node1.sock"
              "-device vhost-user-fs-pci,queue-size=1024,chardev=char0,tag=host_share"
              # Network
              "-netdev socket,id=n1,listen=:1234 -device virtio-net-pci,netdev=n1,mac=52:54:00:12:34:01" 
            ];
```

# Run
## Therminal 1
For Node 1:
```bash
cd ~/sideway
sudo nix run nixpkgs#virtiofsd -- \
  --socket-path=/tmp/vfs-node1.sock \
  --shared-dir=$(pwd) \
  --announce-submounts \
  --sandbox none
```

## Therminal 2
For Node 2:
```bash
cd ~/sideway
sudo nix run nixpkgs#virtiofsd -- \
  --socket-path=/tmp/vfs-node2.sock \
  --shared-dir=$(pwd) \
  --announce-submounts \
  --sandbox none
  ```


## Therminal 3
Make sure the program is running in Terminal 1.
```bash
cd ~/sideway
nix build .#nixosConfigurations.node1.config.system.build.vm
sudo QEMU_OPTS="-nographic" nix run .#nixosConfigurations.node1.config.system.build.vm
```


## Therminal 4
Make sure the program is running in Terminal 2.
```bash
cd ~/sideway
nix build .#nixosConfigurations.node2.config.system.build.vm
sudo QEMU_OPTS="-nographic" nix run .#nixosConfigurations.node2.config.system.build.vm
```

## Therminal 3,4: We mount it manually on two virtual machines.
```bash
mkdir -p /src
mount -t virtiofs host_share /src
cd /src
cargo build --release
cargo build --examples
```

## Therminal 3,4: Clear a failed build (if necessary)
```bash
rm -rf target
rm Cargo.lock
```

## Therminal 3,4: Checking the status of RDMA links
```bash
rdma link
```
## Therminal 3,4: Checking for the presence of character devices
```bash
ls -l /dev/infiniband/
```

## Therminal 3,4: If the interface is not raised
```bash
ip link set eth1 up
```

## Therminal 3 or 4: show_gids
```bash
cargo run --example show_gids
 Dev  | Port | Index |                   GID                   |   IPv4   |  Ver   | Netdev 
------+------+-------+-----------------------------------------+----------+--------+--------
 rxe0 |  1   |   0   | fe80:0000:0000:0000:5054:00ff:fe12:3401 |          | RoCEv2 |  eth1  
 rxe0 |  1   |   1   | 0000:0000:0000:0000:0000:ffff:0a00:0001 | 10.0.0.1 | RoCEv2 |  eth1 
```

# Run examples 
## Run example rc_pingpong
### Therminal 3 
```bash
[root@node1:/src]# cargo run --example rc_pingpong -- -d rxe0 -i 1
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.14s
     Running `target/debug/examples/rc_pingpong -d rxe0 -i 1`
 local address: QPN 0x0012, PSN 0xb701eb, GID fe80:0000:0000:0000:5054:00ff:fe12:3401
remote address: QPN 0x0012, PSN 0x8873de, GID fe80:0000:0000:0000:5054:00ff:fe12:3402
2048000 bytes in 0.12 seconds = 16.80 MiB/s
1000 iters in 0.12 seconds = 116.23µs/iter
```

### Therminal 4 
```bash
[root@node2:/src]# cargo run --example rc_pingpong -- -d rxe0 -i 1 10.0.0.1
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.14s
     Running `target/debug/examples/rc_pingpong -d rxe0 -i 1 10.0.0.1`
 local address: QPN 0x0012, PSN 0x8873de, GID fe80:0000:0000:0000:5054:00ff:fe12:3402
remote address: QPN 0x0012, PSN 0xb701eb, GID fe80:0000:0000:0000:5054:00ff:fe12:3401
2048000 bytes in 0.12 seconds = 16.80 MiB/s
1000 iters in 0.12 seconds = 116.29µs/iter
```

## Run example cmtime
### Therminal 3 
```bash
cargo run --example cmtime -- -b 0.0.0.0 -p 18515 -c 10
```

### Therminal 4 
#### Connections = 10
```bash
[root@node2:/src]# cargo run --example cmtime -- -s 10.0.0.1 -c 10
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.14s
     Running `target/debug/examples/cmtime -s 10.0.0.1 -c 10`
 Step            | Total (ms) | Max (us) | Min (us) 
-----------------+------------+----------+----------
 CreateId        |       5.33 |  5303.44 |     0.89 
 ResolveAddr     |       0.12 |    70.59 |    22.74 
 ResolveRoute    |       0.08 |    29.10 |     6.65 
 CreateQueuePair |       0.19 |    58.79 |     9.60 
 Connect         |       1.16 |   988.98 |   701.28 
 Disconnect      |      40.46 | 40448.38 |   160.27 
 ```
#### Connections = 10000
```bash
[root@node2:/src]# cargo run --example cmtime -- -s 10.0.0.1 -c 10000
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.14s
     Running `target/debug/examples/cmtime -s 10.0.0.1 -c 10000`
 Step            | Total (ms) |  Max (us) | Min (us) 
-----------------+------------+-----------+----------
 CreateId        |      34.34 |   5281.40 |     0.82 
 ResolveAddr     |      44.10 |   1625.26 |     5.06 
 ResolveRoute    |      31.78 |   2746.60 |     6.99 
 CreateQueuePair |     244.56 |    118.66 |     8.48 
 Connect         |     986.88 | 900415.51 |   847.53 
 Disconnect      |     134.05 |  96991.11 |  1551.19 
 ```

## Run example ibv_devinfo
```bash
cargo run --example
```
