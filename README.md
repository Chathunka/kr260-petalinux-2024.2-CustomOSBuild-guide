# Custom OS Build for Kria KR260 FPGA – PetaLinux 2024.2

## 📖 Overview

This guide walks you through the process of building a **custom OS for the Kria KR260 FPGA Robotics Kit** using **PetaLinux** and **BSP (Board Support Package)** files. It includes downloading the necessary files, setting up the environment, applying workarounds, and generating a PetaLinux system image.

---

## 1. 🔗 Official Guide from AMD

Refer to the [AMD Official Guide for Kria PetaLinux](https://www.amd.com/en/support/embedded/kria/kr260) for custom OS build instructions.

---

## 2. ⬇️ Download PetaLinux Installer and BSP Files

Download the **PetaLinux installer** and **BSP files** for the Kria KR260 Robotics Kit from the AMD website.

> ⚠️ **Important:**  
> Ensure that the **PetaLinux Tools** and **BSP files** are of the same version to avoid compatibility issues.  
> _In this guide, the version used is `2024.2`._

---

## 3. ⚙️ Workarounds for a Successful Build

### a. 🖥️ Installation Requirements for the Host Machine

Ensure that your host machine meets **all requirements** as specified in the official guide.

---

### b. 🔧 Pre-Installation Steps for PetaLinux Tools

Before installing PetaLinux Tools, execute the following steps:

#### i. Enable 32-bit Architecture

```bash
sudo dpkg --add-architecture i386
```

#### ii. Switch from `bash` to `dash`

```bash
sudo dpkg-reconfigure dash
```

#### iii. Set Up a TFTP Server

```bash
sudo apt install tftpd-hpa tftp
sudo nano /etc/default/tftpd-hpa
```

Edit the config:

```ini
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/var/lib/tftpboot"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_OPTIONS="--secure"
```

Then run:

```bash
sudo mkdir -p /var/lib/tftpboot
sudo chown -R tftp:tftp /var/lib/tftpboot
sudo chmod -R 777 /var/lib/tftpboot
sudo systemctl restart tftpd-hpa
sudo systemctl enable tftpd-hpa
```

> 🔐 **Note:** `chmod -R 777` is not recommended for production environments due to security risks. Use more restrictive permissions as needed.

#### iv. Add User to `dialout` Group

```bash
sudo adduser $USER dialout
```

---

## 4. 🧪 Install PetaLinux

Use the [official instructions](https://www.amd.com/en/support/embedded/kria/kr260) to install the PetaLinux tools.

> If installation fails due to missing libraries, install the required packages using your OS package manager.  
> Check the **console error log** during installation for specific dependency errors.

---

## 5. 🔧 Set Up PetaLinux Working Environment

Follow AMD documentation to configure your **PetaLinux workspace**.

---

## 6. 📁 Create a Project Using BSP

Use the downloaded BSP files to initialize your project environment:

```bash
petalinux-create -t project --name kr260_project --template zynqMP --bsp KRIA_BSP.bsp
```

---

## 7. 🛠 Customize the Project

Customize components of the PetaLinux project such as:

- Root File System  
- Kernel Configuration  
- Firmware Versions

---

## 8. 🏗️ Build the PetaLinux System Image

Build the project using:

```bash
petalinux-build
```

> ⚠️ **Important Considerations:**
>
> - Ensure a **stable internet connection**
> - Have **sufficient disk space** in the build directory
> - The build may take **over an hour**, depending on system specs
> - If the build fails due to **timeouts**, retry the build

---

## 9. 🧰 Build Sysroot (SDK) for the Project

After a successful build, generate the SDK:

```bash
petalinux-build --sdk
```

This produces the **sysroot for Vitis development**.

---

## 10. 🔄 Generate the Boot Image

Use the AMD documentation to generate a **bootable image** for deployment:

```bash
petalinux-package --boot --fsbl <path_to_fsbl.elf> --u-boot --kernel
```

---

## 11. 🚀 Booting PetaLinux on Target Hardware

1. Transfer the generated image files (e.g., `BOOT.BIN`, `image.ub`) to an **SD card**
2. Insert the SD card into the **Kria KR260 board**
3. Power on the board to boot into the custom PetaLinux OS

---

## ✅ Conclusion

By following these steps, you can successfully build and deploy a **custom OS using PetaLinux** for the **Kria KR260 FPGA Robotics Kit**. This setup enables further customization and application development using **Vitis acceleration tools**.
