MirageOS and Xen
================

This is a testing environment for MirageOS using the Xen Hypervisor installed on a Ubuntu 14.04 LTS Virtualbox VM.

I replicated the steps provided by Magnus Skjegstad at [his website](http://www.skjegstad.com/blog/2015/01/19/mirageos-xen-virtualbox/) with the following software:
* Ansible 1.9.3
* Virtualbox 4.3.30 r101610
* Vagrant 1.7.2

Prerequisites:
--------------

As always, provide host entries for DNS resolution. I found vagrant-hostmanager to handle the /etc/hosts nicely. You can install it with:

```
vagrant plugin install vagrant-hostmanager
```


To build the Xen and MirageOS VM:
=================================

```
vagrant up
```

NOTICE:
=======

There are a lot of reboots that happen to put the VM into the correct state. For the most part, it's idempotent. But I haven't come up with a foolproof way to reboot the server and wait for SSH to come back to continue along. So if the reboot steps and wait for SSH to come back steps fail, simply run:

```
vagrant provision
```

The provision should continue right along with the installation/setup. The Xen and MirageOS related libraries get placed in the ansible_ssh_users home directory. In the case of this vagrant provision, libraries and /bin programs end up in /home/vagrant. Switch to the vagrant user to use Xen and MirageOS. If you log in with:

```
vagrant ssh
```

you will be ready to go.


Test out the Installation
=========================

The playbook creates a bridged network interface ("br0").

```
mirage --version
```
Should return the newest installed version for Ubuntu



Download mirage skeleton project:
=================================

```
git clone http://github.com/mirage/mirage-skeleton.git
```

Navigate to the static-webpage directory:

```
cd mirage-skeleton/static-webpage
```

And execute the Xen configuration:

```
env DHCP=true mirage configure --xen
```

Now compile the unikernel:

```
make
```

After it successfully compiles, run it:

```
sudo xl create www.xl -c
```


Future Development
==================

Feedback is welcome and encourage. This is a personal study and feel free to do whatever you want with the code.

