There are two versions of this lab in the repo; choose the version that your hardware is able to support best. A minimum of 16GB of RAM is recommended for the Full version of the lab.

The Full version uses two vQFX images that consist of two VMs, the virtual Routing Engine (vqfx-re) and virtual Packet Forwarding Engine (vqfx-pfe). The Lite version omits the PFE image, reducing the RAM footprint, but does not support certain features such as DHCP or IPv6 Route Advertisements.

The VyOS images are identical between labs. The Centos host images receive IP and IPv6 addresses dynamically in the Full version, but are configured statically in the Lite (due to aforementioned lack of support from vqfx-re).

All VM Settings and provisioning data are defined in `Vagrantfile`. This includes NIC-to-bridge mappings, per-role Ansible playbooks/host data (for vQFX and VyOS), and post-boot shell commands to run on CentOS hosts.

For additional documentation on Vagrantfile commands, see https://www.vagrantup.com/docs/vagrantfile


_Full Lab Topology:_

![Full Lab Topology](images/hackathon81_lab_4_devices.png?raw=true "vQFX Full Lab Topology")

_Lite Lab Topology:_

![Lite Lab Topology](images/h81_lite_lab_2vqfx_vyos_centos.png?raw=true "vQFX Lite Lab Topology")

## Instructions for Lab install ##

### Centos 8:
### Install latest Vagrant release:

    sudo dnf -y install https://releases.hashicorp.com/vagrant/2.2.14/vagrant_2.2.14_x86_64.rpm


### **Install VirtualBox:**

_1. Enable repo_

    sudo dnf -y install wget
    sudo wget https://download.virtualbox.org/virtualbox/rpm/el/virtualbox.repo
    sudo mv virtualbox.repo /etc/yum.repos.d/
    sudo wget -q https://www.virtualbox.org/download/oracle_vbox.asc
    sudo rpm --import oracle_vbox.asc
    sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

_2. Install required libs/modules_

    sudo dnf -y install binutils kernel-devel kernel-headers libgomp make patch gcc glibc-headers glibc-devel dkms
    sudo dnf install -y VirtualBox-6.1

### **Install Ansible:**
_1. Create and activate python3 virtual environment_

    python3 -m venv {{ name_of_virtual_env }}
    source {{ name_of_virtual_env }}/bin/activate

_2. Install Ansible and plugins_  
    
    sudo dnf install -y ansible
    ansible-galaxy install Juniper.junos
    pip3 install netaddr junos-eznc jxmlease

## Instructions for MacOS lab install ##
### **Download and install Virtualbox 6.1:**
https://www.virtualbox.org/wiki/Downloads

### **Download and install Vagrant:**
https://www.vagrantup.com/downloads

### **Install Ansible:**
_1. Create and activate python3 virtual environment_

    python3 -m venv {{ name_of_virtual_env }}
    source {{ name_of_virtual_env }}/bin/activate

_2. Install Ansible and required modules_

    pip3 install ansible netaddr junos-eznc jxmlease
    ansible-galaxy install Juniper.junos 
    ansible-galaxy collection install vyos.vyos

## Install and activate lab environment: ##
**Clone the lab repo**

    sudo dnf install -y git 
    mkdir lab
    cd lab
    git clone https://github.com/nanog/hackathon81_lab

**Launch VMs defined in Vagrantfile**

    cd full|lite-vqfx-vyos
    export VAGRANT_DEFAULT_PROVIDER=virtualbox  
    vagrant up

_If centos1/2 provisioning displays errors about invalid routes, recreate those VMs_

    vagrant destroy -f centos1 centos2 && vagrant up centos1 centos2


### **Common Vagrant commands**

_Instantiate and provision all unprovisioned images defined in Vagrantfile_

    vagrant up

_Instantiate and provision a specific VM_

    vagrant up {{ vm_name }}

_ssh to VM_

    vagrant ssh {{ vm_name }}    

_Delete all images_ (often needed to re-provision after making changes to the Vagrantfile)

    vagrant destroy [-f]

_Delete a specific VM_

    vagrant destroy [-f] {{ vm_name }}

_Get state of all VMs defined in Vagrantfile_

    vagrant status

_Run provisioning operations on active VM_

    vagrant provision [-f] {{ vm_name }}