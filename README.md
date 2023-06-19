## Kernel

### What if the kernel is already signed?

You need ```mokutil``` and ```pesign```.
The command below will enroll the kernel key while rebooting:

```sudo mokutil --import-hash $(pesign -P -h -i /boot/vmlinuz-[KERNEL_VERSION]  | cut -f 2 -d ' ')```

### Signing non-presigned Kernels

See: https://github.com/M-P-P-C/Signing-an-Ubuntu-Kernel-for-Secure-Boot

#### Automate signing

You may use script something like this:
```
#!/bin/sh

LATEST_KERNEL=`ls -lt /path/to/boot/vmlinuz-* | tr -s ' ' | cut -d' ' -f9 | awk 'NR==1{print $1}' `
IS_IT_SIGNED=$(echo $LATEST_KERNEL | rev | cut -d '.' -f 1 | rev)

if [ $IS_IT_SIGNED = "backup" ]; then
	echo "Already Signed"
else
	sudo mv $LATEST_KERNEL $LATEST_KERNEL.backup
	sudo sbsign --key /path/to/MOK.priv --cert /path/to/MOK.pem $LATEST_KERNEL.backup --output $LATEST_KERNEL
	sudo update-grub
fi
```

I __don't__ recommend you to run this script if you don't know what's going on. 


## Kernel Modules (DKMS)

Find your dkms keys and enroll them with ```sudo mokutil --import /path/to/dkms/keys/mok.pub```.

In Ubuntu based distros (such as Linux Mint), cli package managers will be likely to help you to enroll the module key.

In Debian, see [debian wiki page](https://wiki.debian.org/SecureBoot?action=quicklink#Making_DKMS_modules_signing_by_DKMS_signing_key_usable_with_the_secure_boot).



-------
WIP
