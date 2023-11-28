# Bootstrap

This guide provides instructions for setting up Raspberry Pis (RPIs) with a bootable Ubuntu image and necessary configurations for MicroK8s and FluxCD.

Inspired by [billimek's homelab infrastructure](https://github.com/billimek/homelab-infrastructure) for SD card configuration, and the [Ubuntu tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi) for creating a bootable image.

## Getting Started

### Bootable Image

1. **Preparing the SD Card**: 
   - Follow the steps outlined in the [Ubuntu tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card) to prepare the SD card with Ubuntu Server 22.04 64-bit image.
   - Ensure the image matches the RPI's architecture.

   ![Raspberry PI Arm64 image](ubuntuRPI.png)

### Adding Bootstrap Information 

2. **Network Configuration**: 
   - If using Wi-Fi, update the `network-config` file with your Wi-Fi details. Ethernet connections are preferred for stability.

## Setup Instructions

### Windows

3. **File Transfer**: 
   - After flashing the image, reinsert the SD card. Copy the following files into the `system-boot` directory:
     - `usercfg.txt`
     - `network-config`
     - `cmdline.txt`
     - `config.txt`
     - Copy the appropriate file from the `nodes` directory (e.g., `puc1` or `test`) and rename it to `user-data`. Replace `$UBUNTU_PASSWORD` in this file with your chosen password for the ubuntu user.

### MAC/Linux

4. **File Transfer**:
   - After flashing the image, remount the drive. Copy the same files as above into `<drive>/system-boot/`.
   
   **Option 1: Manual Setup**
   - Follow the same procedure as for Windows.

   **Option 2: Scripted Setup**
   - Use `bootstrap.sh` script for streamlined setup. Set the Ubuntu user password using the environment variable:
     ```shell
     export UBUNTU_PASSWORD='YourChosenPassword'
     ./bootstrap.sh dev /Volumes/system-boot
     ```
   - Unmount the filesystem on Mac:
     ```shell
     diskutil unmount /Volumes/system-boot/
     ```

### Starting the RPI

5. **Initialization**:
   - Insert the MicroSD card into the Raspberry Pi 4 and connect it via Ethernet cable.
   - The setup will take approximately 20 minutes, depending on internet speed and SD card performance. The process involves downloading data, installing Kubernetes applications, and deploying containers.

### Testing the Setup

6. **Verification**:
   - Test the RPI using an edge app component for instance a simple ping/pong endpoint. Replace `localhost` with the RPI's IP address:
     ```shell
     localhost:30080/api/v1/ping # Should return "pong"
     ```

7. **Troubleshooting**:
   - If the setup fails, wait a few minutes and retry. 
   - For further investigation, SSH into the RPI and check the status using `microk8s.kubectl get all`. If needed, clean the cloud-init logs and reboot:
     ```shell
     sudo cloud-init clean --logs
     ```

### GitHub Repository Updates

8. **Secure Token Handling**:
   - Be cautious when sharing an SD card with a read/write Personal Access Token (PAT). This poses a security risk.
   - Newer RPI installations can update from the GitHub repository. However, a PAT with higher permissions than the current setup is required.

9. **Creating and Updating PAT**:
   - Generate a new PAT on GitHub with the necessary scopes (read and write access). Visit [GitHub Personal Access Tokens](https://github.com/settings/tokens).
   - To update the PAT on the RPI, SSH into the device and execute the following as root:
     ```shell
     microk8s.kubectl delete secret -n flux-system flux-system # Delete old secret
     microk8s.kubectl create secret -n flux-system generic flux-system --from-literal=username=<github_username> --from-literal=password=<new_pat> # Create new one with higher permission
     ```
