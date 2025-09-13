# SHandbox

SHandbox is a collection of shell scripts that make a sandboxed environment based on a base image. I made this as an experiment to learn how containerization works under the hood, so this works in a similar way, but less featured of course.

## Usage

To run a SHandbox, the syntax is as follows:
```sh
shandbox /path/to/image.shandbox.img /bin/sh
```
Replace `/bin/sh` with the path (in the image) to whatever command you want to execute. Do not run shandbox as root.

## Making images

SHandbox images are actually just regular filesystem images. Thus, you can make the rootfs as a regular directory then just copy everything in to the image. We will assume here you have a directory `alpine-root` containing the Alpine minirootfs.

First, you need to know how big to make the image. You can find the size of the rootfs directory with `du -sh alpine-root`. Mine was around 9M, so I'll make an image of 16M. The image itself will be readonly so there is no need to make it much larger than it needs to be.

Make an empty ext4 image file: (Note that it doesn't have to be ext4)
```sh
dd if=/dev/zero of=alpine.shandbox.img bs=1M count=16
mkfs.ext4 alpine.shandbox.img
```

Mount the image to a directory alpine-img:
```sh
mkdir alpine-img
sudo mount -o loop alpine.shandbox.img alpine-img/
```

Copy contents of alpine-root to alpine-img:
```sh
sudo cp -r alpine-root/* alpine-img/
```

Clean up:
```sh
sudo umount alpine-img/
rmdir alpine-img/
sudo rm -rf alpine-root # Optional, of course
```

This can now be used in SHandbox. Example: `shandbox alpine.shandbox.img /bin/sh`
