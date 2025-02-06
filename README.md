# README for NBD Tool

This script facilitates managing Network Block Devices (NBD) by mounting and unmounting devices via the `qemu-nbd` utility. The script includes enhanced functionality, such as timeout handling, device checking, and error reporting.

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
    - [Syntax](#syntax)
    - [Actions](#actions)
    - [Options](#options)
    - [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## Requirements

The script requires the following:

- `qemu-nbd` (part of the `qemu-utils` package)
- Bash shell environment
- Root privileges for managing NBD devices

## Installation

### 1. Copy the Script to `/usr/local/bin`

To make the script globally accessible, copy it to `/usr/local/bin`:

```bash
sudo cp nbd-tool /usr/local/bin/nbd-tool
sudo chmod +x /usr/local/bin/nbd-tool
```

This allows you to run the script from anywhere using the command `nbd-tool`.

### 2. Loading NBD module to kernel linux

#### 1. Loading module on live system
Set up NBD module by loading on live system (this option disappear after reboot):

```bash
modprobe nbd nbds-max=64 max_part=8
```

#### 2. Configure `modprobe` for the NBD Module

##### Step 1: Modify the `modprobe` Configuration File

Set up the NBD module with the desired parameters by creating or editing `/etc/modprobe.d/nbd.conf`:

```bash
sudo nano /etc/modprobe.d/nbd.conf
```

Add the following configuration:

```
options nbd nbds-max=64 max_part=8
```

##### Step 2: Load the NBD Module on Boot

Ensure the `nbd` module is loaded during system startup by adding it to `/etc/modules-load.d/nbd.conf`:

```bash
sudo nano /etc/modules-load.d/nbd.conf
```

Add:

```
nbd
```

#### 3. Rebuild Initramfs (Optional)

If your system uses `initramfs`, rebuild it to include these changes:

```bash
sudo dracut -f
```

## Usage

### Syntax

```bash
nbd-tool ACTION [options] <requires> 
```
or
```bash
nbd-tool [options] ACTION <requires>
```

### Actions
#### 1. mount

Mounts qcow2 disk."

##### Options

- -o OPTION   Extra mount options (comma-separated or multiple -o).
- -q OPTION   Extra qemu-nbd options (comma-separated or multiple -q).
- -p PART_ID  ID of partition, if 0 mounted disk without partitions (default: 0).
- -t TIMEOUT  Timeout in seconds for waiting for free nbd devie, if 0 disable timeout (default: 0).

##### Requires

- SOURCE      Path to qcow2 file.
- TARGET      Path to mount point.

#### 2. unmount

Unmounts mountpoint.

##### Requires

- TARGET      Path to mount point.

#### 3. info

Info (fdisk) of qcow2 disk.

##### Options

- -q OPTION   Extra qemu-nbd options (comma-separated or multiple -q).
- -t TIMEOUT  Timeout in seconds for waiting for free nbd devie, if 0 disable timeout (default: 0).

##### Requires

- SOURCE      Path to qcow2 file.

#### 4. disconnect_all

Disconnect all unused discs.

### Options
- -h        Display help message.

### Examples

```bash
nbd-tool -h
```

```bash
nbd-tool mount -o rw,defaults -o noatime -q format=qcow2 -t 60 -p 1 test.img /mnt/nbd
```

```bash
nbd-tool unmount /mnt/nbd
```

```bash
nbd-tool info -t 60 test.img
```

```bash
nbd-tool disconnect_all
```

## Troubleshooting

- **"Error: nbd module is not loadaded to kernel**:
  Ensure the NBD module is loaded and that the specified source device exists.
- **"Error: This script must be run as root.**:
  Ensure you have root privileges when running the script.

## License

This script is distributed under the GPLv3 License. See the LICENSE file for details.
