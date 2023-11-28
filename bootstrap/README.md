# Bootstrap

Bootstrap info for the PIs

Inspired by: https://github.com/billimek/homelab-infrastructure for getting the proper configuration on the SD cards and
https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi for the "bootable image"

## Getting started

### Bootable image

Follow the instructions on: https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card to prepare the SD card and install the Ubuntu Server 22.04 64Bit image.

Make sure that you are using the right image with the right architecture
![Raspberry PI Arm64 image](ubuntuRPI.png)


### Adding bootsrap info 

If you use a WIFI internet connection, you can update the `network-config' file with Wi-Fi connection information. However, an ethernet connection is highly recommended. 

## Windows Setup
After flashing the image, reject the drive and insert it again. Copy the following files into `system-boot` drive:

* `usercfg.txt`
* `network-config`
* `cmdline.txt`
* `config.txt`

*You also need to copy the proper file from nodes directory like puc1 or test and rename it to `user-data`; Remember to replace $UBUNTU_PASSWORD in the file with the password for ubuntu user

## MAC/Linux Setup

After flashing the image, re-mount the drive and copy the following files into `<drive>/system-boot/`:

* `usercfg.txt`
* `network-config`
* `cmdline.txt`
* `config.txt`
* `user-data`
#### Option 1 manual
You also need to copy the proper file from nodes directory like puc1 or test and rename it to `user-data`, Remember to replace $UBUNTU_PASSWORD in the file with the password for ubuntu user
* `user-data`

#### Option 2 scripted setup

This can be streamlined with the following script: `bootstrap.sh` that is depending on an variable to set the password for the ubuntu user:

```shell
export UBUNTU_PASSWORD='SampleLocalPassword'
./bootstrap.sh dev /Volumes/system-boot

After that you need to unmount the filesystem. On mac you do:

```shell
diskutil unmount /Volumes/system-boot/
```

#### Start the RPI 

Add the MicroSD card to the raspberry Pi4 and connect with an ethernet cable. After around 20 min, your system will be ready. During that period, it downloads around 1 GiB of data. The first 10 minutes are upgrading and bootstraping the base operating system, 5 minutes installing the Kubernetes applications, and around 5min for installing the containers. The bootstrapping time could differ based on internet speed and even the speed of the MicroSD card. The recommendation is to run this "job" with a good internet connection and test it before sending it to the patient's home. 

### Test
Eighter use the patient mobile app for finding RPI and test it or use the pin API

replace localhost with the IP of RPI
``` shell
localhost:30080/api/v1/lifechamps/ping -> it returns "pong"
```

### if failed
--Wait for a few more minutes and test again
--Login to the RPI
Check microk8s.kubectl get all
Run 
``` shell
sudo cloud-init clean --logs
```
and reboot


## Change so the RPI can update the gitrepos with new version
Be careful with sharing a sd-card with read/write PAT token with anyone during the project time. This is a secutiry risk.

The RPIs following installed from test have the possibility to update the gitrepos when new images are aviable, but need to have a PAT token with higher permission than the one in this repo. 

To add a new PAT token, you first need to create one at: https://gitlab.com/-/profile/personal_access_tokens
It needs to have the following scopes: 

- read_repository
- write_repository

Login in to the RPI with ssh, and run the following command as root:
```shell
microk8s.kubectl delete secret -n flux-system flux-system #Delete old secret
microk8s.kubectl create secret -n flux-system generic flux-system --from-literal=username=git --from-literal=password=glpat-mysecret-pat-from-above # Create new one with higher permission
```



