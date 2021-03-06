# 🗲 Ride the Lightning!🗲

Virt-Lightning can quickly deployment a bunch of new VM. It
can also prepare the Ansible inventory file!

This is really handy to quickly validate a new playbook or a role on a large number of environments.

## Example ⚡

```shell
echo "- distro: centos-7" > virt-lightning.yaml
vl up
vl ansible_inventory
ansible all -m ping -i inventory
```

In this example, we:

1. use the list of distribution to generate a virt-lightning.yaml file.
2. we than create a environment based on this file
3. once the environment is ready, we generate an Ansible inventory file
4. and we use it to call Ansible's ping module on all the host.

[![demo](https://asciinema.org/a/auiusqdLi83LmnqjibmX7ihtg.svg)](https://asciinema.org/a/auiusqdLi83LmnqjibmX7ihtg?autoplay=1)

This example show up how to use the host selector to quickly connect to a host.

[![ssh host selector](https://asciinema.org/a/228996.svg)](https://asciinema.org/a/228996?autoplay=1)

## Pre-requirements



<details><summary>Debian</summary>
<p>

First you need to install libvirt and guestfs:
```shell
sudo apt install -f libguestfs-tools libvirt-daemon libvirt-daemon-system python3 python3-libvirt python3-pip python3-urwid
sudo systemctl start --now libvirtd
```

The second step is to grant to your user the ability to use libvirt:
```shell
sudo usermod -a -G kvm,libvirt,libvirt-qemu $USER
```
</p>
</details>


<details><summary>Fedora-29</summary>
<p>

First you need to install libvirt and guestfs:
```shell
sudo apt install -f libguestfs-tools libselinux-python libvirt python3 python3-libvirt python3-pip python3-urwid
sudo systemctl start --now libvirtd
```

The second step is to grant to your user the ability to use libvirt:
```shell
sudo usermod -a -G qemu,libvirt $USER
```
</p>
</details>


<details><summary>Ubuntu-16.04</summary>
<p>

First you need to install libvirt and guestfs:
```shell
sudo apt install -f libguestfs-tools libvirt-bin libvirt-daemon python3 python3-libvirt python3-pip python3-urwid
sudo systemctl start --now libvirtd
```

The second step is to grant to your user the ability to use libvirt:
```shell
sudo usermod -a -G kvm,libvirtd $USER
```
</p>
</details>


<details><summary>Ubuntu-18.04</summary>
<p>

First you need to install libvirt and guestfs:
```shell
sudo apt install -f libguestfs-tools libvirt-bin libvirt-daemon python3 python3-libvirt python3-pip python3-urwid
sudo systemctl start --now libvirtd
```

The second step is to grant to your user the ability to use libvirt:
```shell
sudo usermod -a -G kvm,libvirt $USER
```
</p>
</details>


<details><summary>Ubuntu-18.10</summary>
<p>

First you need to install libvirt and guestfs:
```shell
sudo apt install -f libguestfs-tools libvirt-daemon libvirt-daemon-system python3 python3-libvirt python3-pip python3-urwid
sudo systemctl start --now libvirtd
```

The second step is to grant to your user the ability to use libvirt:
```shell
sudo usermod -a -G kvm,libvirt $USER
```
</p>
</details>



## Installation

```shell
pip3 install --user --no-deps git+https://github.com/virt-lightning/virt-lightning
```

The `--no-deps` argument is only required on Ubuntu (See: https://github.com/pypa/pip/issues/4222).

`virt-lightning` will be installed in ~/.local/bin/. Add it in your `$PATH` if
it's not already the case. For instance if you use:

```shell
echo "export PATH=$PATH:~/.local/bin/" >> ~/.bashrc
source ~/.bashrc
```

## Fetch some images

Before you start your first VM, you need to fetch the images. To do so,
you just need these scripts:
https://github.com/virt-lightning/virt-lightning/tree/master/images

```shell
git clone https://github.com/virt-lightning/virt-lightning
cd virt-lightning/images
./image centos-7 build
./image debian-9 build
(etc)
```

## Actions

`vl` is an alias for `virt-lightning`, you can us both. In the rest of the document
we use the shortest version.

### vl up

`virt-lightning` will read the `virt-lightning.yaml` file from the current directory and prepare the associated VM.

### vl down

Destroy the VM.

	Flash before my eyes
	Now it's time to die
	Burning in my brain
	I can feel the flame

### vl status

List the VM, their IP and if they are reachable.

### vl ansible_inventory

Export an inventory in the Ansible format.

### vl distro

List the distro images that can be used. Its output is compatible with `vl up`.
You can initialize a new configuration with: `vl distro > virt-lightning.yaml`.

### Performance profiling

```shell
time vl up
vl ansible_inventory > inventory
ansible all -m shell -a "systemd-analyze blame|head -n 5" -i inventory
```

### Configuration from file

You can create your own configuration file like this and save to config.ini

```
[main]
network_name = virt-lightning
root_password = root
storage_pool = virt-lightning
```

After creation configuration you can use `vl` command with `--config` argument and provide location to your config file.

```shell
vl --config config.ini
```