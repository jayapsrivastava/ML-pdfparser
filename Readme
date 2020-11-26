# VM setup for UML kernel debugging

The setup is aligned with the [official kernel instructions for UML setup](https://www.kernel.org/doc/html/latest/virt/uml/user_mode_linux_howto_v2.html#creating-an-image).  Note that we appear not to need everything that is talked about there, e.g. the command to start user-mode-linux is simpler than what is suggested.

## Vagrant

Vagrant file is provided.

- Use `bento/debian-10` image.  
    - Debian has good support for `rdma-core` which we eventually want to debug
    - Debian has a good facility to make root file systems for UML
- If you don't have Big Sur MacOS - WAIT, don't yet install it, it will break vagrant.
- Don't forget, if you use MacOS Big Sur, you at least need the following to configure networking in Vagrant (but the whole thing doesn't work well at the moment)
  ``` 
  config.vm.provider "vmware_fusion" do |v|
    v.ssh_info_public = true
  end
  ```

## Provisioning this VM

- You will need to build a kernel, and the following might be needed
- apt-get -y install build-essential flex bison xz-utils wget ca-certificates bc libncurses-dev gdb

## Kernel config

- `cd /vagrant`
- This was done with [5.9.8](https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.9.8.tar.xz)
- `tar xf .... ; cd linux-.... `
- A simple kernel configuration file with some UML kernel debugging is enabled is provided `uml-debug.config`
- Build it: `make ARCH=um oldconfig ; make ARCH=um -j10 ; cp linux /bin`
- NOTE: the build in /vagrant gives an error, because hard links are not supported in the vagrant host file system

## Make a root file system

- `mkdir /umlroot`
- `debootrap buster /umlroot http://deb.debian.org/debian`
- set the root password: `chroot /umlroot ; passwd ..... ; exit XXX`
- `cp /vagrant/init.sh /umlroot/init.sh`

## Start very basic UML

- `gdb linux  rootflags=/umlroot rootfstype=hostfs rw mem=128M verbose init=/init.sh`

## What needs to be done?

- [] set up networking so that a few of these machines can talk to each other (we want to be able to run clients and e.g. Lustre or NFS servers in two UML instances)
- [] add ssh to the root file system and to init.sh (or get a better console)
- [] install kernel modules in `/umlroot/lib/modules`
- [] get a better console
- [] include more debug symbols?  Somehow the traces don't look 100% right?
- [] set up vscode to control gdb in the vagrant machine
- [] learn about UML's console, to get stack traces etc
- [] see which network device supports `rdma-core` (then we can debug low-level networking code)
