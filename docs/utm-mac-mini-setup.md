UTM Virtual Machine Setup on a Base-Model M4 Mac Mini

This guide documents the virtual-machine environment used to build and test the initial CloudCommand Kubernetes prototype.

The environment consists of three Ubuntu Server virtual machines running in UTM on a base-model M4 Mac mini:

* One Kubernetes control-plane node
* Two Kubernetes worker nodes

The objective is to create a small, reproducible infrastructure environment for Kubernetes development, cluster automation, and CloudCommand integration testing.

⸻

Environment Overview

Component	Role
Base-model M4 Mac mini	Physical development host
UTM	Local virtualization platform
Ubuntu Server ARM64	Guest operating system
k8s-cp1	Kubernetes control-plane node
k8s-worker1	Kubernetes worker node
k8s-worker2	Kubernetes worker node

This environment is intended for development, testing, and proof-of-concept workloads rather than production use.

⸻

Host Hardware Verification

Before creating the virtual machines, verify the hardware resources available on the M4 Mac mini.

Display Physical and Logical CPU Cores

sysctl hw.physicalcpu hw.logicalcpu

Display a Sanitized Hardware Summary

The following command displays the relevant host information without exposing the serial number, hardware UUID, or provisioning identifiers:

system_profiler SPHardwareDataType | grep -E \
"Model Name|Model Identifier|Chip|Total Number of Cores|Memory"

Example output:

Model Name: Mac mini
Model Identifier: Mac16,10
Chip: Apple M4
Total Number of Cores: 10
Memory: 16 GB

Do not publish the complete output of system_profiler SPHardwareDataType without filtering it. The complete report may include unique hardware identifiers.

⸻

Install UTM

Download UTM from the official UTM website:

* Download UTM from the official website

The website version is free and provides the functionality required for this environment.

The Mac App Store release is not required for this guide.

After downloading UTM:

1. Open the downloaded disk image.
2. Drag UTM into the macOS Applications folder.
3. Start UTM.
4. Approve any macOS security prompts.
5. Confirm that UTM opens successfully.

The M4 Mac mini uses Apple silicon. Use an ARM64 guest operating system and select virtualization rather than emulation.

Virtualization allows the ARM64 guest to run directly on the Mac’s ARM-based processor and provides substantially better performance than CPU emulation.

⸻

Download Ubuntu Server for ARM

This environment uses Ubuntu Server ARM64.

Ubuntu Server was selected because:

* It works reliably in UTM on the M4 Mac mini.
* It provides an official ARM64 installation image.
* It is widely supported by Kubernetes documentation and tooling.
* It has predictable package management.
* Long-term support releases receive extended security and maintenance updates.
* Using one known operating system makes the build easier to reproduce.

Download Ubuntu Server for ARM from the official Ubuntu website:

* Download Ubuntu Server for ARM

Use the following image type:

Ubuntu Server LTS
64-bit ARM / ARM64

For this environment, use:

Ubuntu Server 24.04 LTS ARM64

Download the standard ARM64 server installation ISO.

The filename should resemble:

ubuntu-24.04.x-live-server-arm64.iso

The exact maintenance version represented by x may change as Ubuntu publishes updates.

Do not download the AMD64 or x86-64 image. The M4 Mac mini uses the ARM64 architecture for native virtualization.

Store the ISO somewhere that will remain available until all three virtual machines have been installed.

A suitable location is:

~/Downloads/ubuntu-24.04-live-server-arm64.iso

The exact filename may differ depending on the current Ubuntu maintenance release.

⸻

Virtual Machine Resource Plan

The resource allocation must leave enough CPU and memory available for macOS, UTM, terminal sessions, and CloudCommand development.

A practical configuration for a base-model M4 Mac mini is:

Virtual Machine	CPU Cores	Memory	Storage
k8s-cp1	2	4 GB	30 GB
k8s-worker1	2	3 GB	30 GB
k8s-worker2	2	3 GB	30 GB

This allocation uses approximately 10 GB of guest memory while leaving the remaining host memory available to macOS and development tools.

Avoid assigning all available host memory to the virtual machines. Excessive host memory pressure can make macOS and the entire VM environment unstable or unresponsive.

⸻

Create the Control-Plane Virtual Machine

Open UTM and select the option to create a new virtual machine.

1. Select Virtualization

Choose:

Virtualize

Do not choose emulation.

The M4 Mac mini and the Ubuntu guest both use the ARM64 architecture, so emulation is unnecessary.

2. Select Linux

Choose:

Linux

as the guest operating-system type.

3. Select the Ubuntu ISO

Select the Ubuntu Server ARM64 ISO downloaded earlier.

Confirm that the selected filename includes:

arm64

4. Configure Hardware

Assign the following resources to the control-plane node:

CPU cores: 2
Memory:    4096 MB
Storage:   30 GB

5. Configure Networking

Use UTM shared networking for the initial environment.

Shared networking should provide:

* Internet access from the virtual machines
* Ubuntu package installation
* Communication between the Mac and the guests
* Communication among the guest virtual machines
* SSH access from the M4 Mac mini

The guest IP addresses may change unless static addresses or DHCP reservations are configured later.

6. Name the Virtual Machine

Use:

k8s-cp1

Save the virtual machine.

⸻

Install Ubuntu Server on the Control-Plane Node

Start k8s-cp1 and complete the Ubuntu Server installation.

During installation:

* Select the preferred language.
* Select the appropriate keyboard layout.
* Use the standard Ubuntu Server installation.
* Allow the VM to use DHCP during the initial installation.
* Use the entire virtual disk.
* Create the administrative user.
* Set the hostname to k8s-cp1.
* Enable the OpenSSH server.
* Do not install unnecessary server snaps or optional packages.
* Record the username and authentication credentials securely.

When prompted for a server name, enter:

k8s-cp1

When the installer offers to install OpenSSH, select:

Install OpenSSH server

After installation completes:

1. Reboot the virtual machine.
2. Stop the VM if it returns to the installer.
3. Detach the Ubuntu installation ISO if UTM does not remove it automatically.
4. Start the VM again.
5. Confirm that Ubuntu boots from the virtual disk.

⸻

Create the First Worker Node

Create another Ubuntu Server ARM64 virtual machine in UTM.

Use the following configuration:

Name:       k8s-worker1
CPU cores:  2
Memory:     3072 MB
Storage:    30 GB
Hostname:   k8s-worker1

Use the same Ubuntu Server ARM64 ISO and the same general installation options used for the control-plane node.

During the Ubuntu installation:

* Set the hostname to k8s-worker1.
* Create the same administrative username when practical.
* Enable OpenSSH server.
* Use the entire virtual disk.
* Avoid unnecessary optional packages.

After installation:

1. Reboot the virtual machine.
2. Detach the Ubuntu installation ISO.
3. Confirm that the VM boots from its virtual disk.

⸻

Create the Second Worker Node

Create the third Ubuntu Server ARM64 virtual machine.

Use:

Name:       k8s-worker2
CPU cores:  2
Memory:     3072 MB
Storage:    30 GB
Hostname:   k8s-worker2

Use the same Ubuntu Server version and installation options used for the other two nodes.

During installation:

* Set the hostname to k8s-worker2.
* Create the administrative user.
* Enable OpenSSH server.
* Use the entire virtual disk.
* Avoid unnecessary optional packages.

After installation:

1. Reboot the virtual machine.
2. Detach the Ubuntu installation ISO.
3. Confirm that Ubuntu boots normally.

⸻

Verify the UTM Environment

Start all three virtual machines.

The UTM interface should display:

k8s-cp1
k8s-worker1
k8s-worker2

Capture a screenshot showing all three virtual machines in UTM.

Suggested screenshot path:

docs/screenshots/utm-three-node-environment.png

Embed the screenshot in this document with:

![Three-node Kubernetes environment running in UTM](screenshots/utm-three-node-environment.png)

⸻

Verify Ubuntu and the Hostnames

Run the following command inside each virtual machine:

hostnamectl

Expected hostnames:

k8s-cp1
k8s-worker1
k8s-worker2

A shorter hostname check is:

hostname

Verify the Ubuntu release:

lsb_release -a

Alternatively:

cat /etc/os-release

The output should identify the system as Ubuntu Server 24.04 LTS or the selected Ubuntu 24.04 maintenance release.

⸻

Verify the Guest Architecture

Run:

uname -m

Expected output:

aarch64

The value aarch64 identifies the running system as 64-bit ARM.

You can also inspect the package architecture:

dpkg --print-architecture

Expected output:

arm64

⸻

Verify CPU Resources

Inside each virtual machine, run:

lscpu

Confirm that the guest operating system can see the CPU cores assigned through UTM.

For a compact result:

nproc

Expected result for this configuration:

2

⸻

Verify Memory

Run:

free -h

The control-plane node should report approximately 4 GB of memory.

Each worker node should report approximately 3 GB of memory.

Some memory is reserved or used by the operating system, so the displayed total may be slightly lower than the amount configured in UTM.

⸻

Verify Storage

Display the guest block devices:

lsblk

Display root-filesystem usage:

df -h /

Confirm that the root filesystem has sufficient free space for:

* Ubuntu packages
* Container images
* Kubernetes components
* Logs
* Application workloads

⸻

Identify the Virtual Machine IP Addresses

Inside each VM, run:

hostname -I

For detailed interface information:

ip addr

Record the address assigned to each node.

Example:

Hostname	IP Address
k8s-cp1	192.168.64.4
k8s-worker1	192.168.64.5
k8s-worker2	192.168.64.6

These addresses are examples only. UTM may assign different addresses.

⸻

Verify Network Connectivity

From each worker node, test connectivity to the control-plane node:

ping -c 4 <CONTROL_PLANE_IP>

Example:

ping -c 4 192.168.64.4

From the control-plane node, test both workers:

ping -c 4 <WORKER_1_IP>
ping -c 4 <WORKER_2_IP>

⸻

Verify Internet Connectivity

Test outbound IP connectivity:

ping -c 4 1.1.1.1

Verify DNS resolution:

ping -c 4 ubuntu.com

Both tests should succeed before continuing with Kubernetes installation.

⸻

Verify SSH Access from the M4 Mac Mini

From the macOS terminal, connect to each virtual machine:

ssh <USERNAME>@<VM_IP>

Example:

ssh dave@192.168.64.4

Repeat the test for both worker nodes.

Successful SSH access confirms that:

* The virtual machine is reachable.
* OpenSSH is running.
* The user account is valid.
* Authentication works.
* The guest network is functioning.

⸻

Optional SSH Configuration

To simplify access, create SSH aliases on the M4 Mac mini.

Open the SSH configuration file:

nano ~/.ssh/config

Add entries similar to:

Host k8s-cp1
    HostName 192.168.64.4
    User dave
Host k8s-worker1
    HostName 192.168.64.5
    User dave
Host k8s-worker2
    HostName 192.168.64.6
    User dave

Save the file.

The nodes can then be accessed with:

ssh k8s-cp1
ssh k8s-worker1
ssh k8s-worker2

If UTM assigns new IP addresses after a restart, update the SSH configuration accordingly.

⸻

Update Ubuntu

Run the following commands on all three nodes:

sudo apt update
sudo apt upgrade -y

Remove packages that are no longer required:

sudo apt autoremove -y

Reboot if required:

sudo reboot

After the reboot, reconnect to each machine and confirm that it is available.

⸻

Recommended Screenshot Checklist

Capture screenshots that demonstrate the environment without exposing sensitive information.

Recommended screenshots:

* Sanitized M4 Mac mini hardware summary
* UTM displaying all three VMs
* Control-plane VM resource allocation
* Worker VM resource allocation
* Ubuntu version verification
* ARM64 architecture verification
* Successful hostname verification
* Successful CPU and memory verification
* IP addresses assigned to all nodes
* Successful inter-node connectivity
* Successful SSH connection from the Mac

Store the images under:

docs/screenshots/

Use descriptive filenames such as:

m4-mac-mini-hardware-summary.png
utm-three-node-environment.png
k8s-cp1-resources.png
k8s-worker1-resources.png
k8s-worker2-resources.png
ubuntu-version-verification.png
arm64-architecture-verification.png
node-network-verification.png
ssh-access-verification.png

⸻

Final Validation Checklist

Before beginning the Kubernetes installation, confirm:

* UTM was downloaded from the official UTM website
* UTM is installed and working
* Ubuntu Server 24.04 LTS ARM64 is installed on all three VMs
* Each node reports the aarch64 or arm64 architecture
* The control-plane hostname is k8s-cp1
* The worker hostnames are k8s-worker1 and k8s-worker2
* Each VM has two virtual CPU cores
* The control-plane node has approximately 4 GB of memory
* Each worker node has approximately 3 GB of memory
* Each VM has sufficient virtual-disk space
* All three VMs have valid IP addresses
* The nodes can communicate with one another
* Each VM has outbound internet access
* DNS resolution works
* SSH access works from the M4 Mac mini
* Ubuntu package updates have been installed
* Screenshots do not expose serial numbers or unique identifiers

⸻

Result

The base-model M4 Mac mini now hosts a three-node Ubuntu Server ARM64 environment:

k8s-cp1       Kubernetes control-plane node
k8s-worker1   Kubernetes worker node
k8s-worker2   Kubernetes worker node

This virtual infrastructure is ready for:

* Ubuntu prerequisite configuration
* Container runtime installation
* Kubernetes package installation
* Control-plane initialization
* Worker-node enrollment
* Cluster validation
* CloudCommand integration testing
* Cluster lifecycle automation

⸻

Next Step

Continue with:

docs/kubernetes-prerequisites.md

The next document will cover:

* Disabling swap
* Loading the required kernel modules
* Configuring network forwarding
* Installing and configuring containerd
* Adding the Kubernetes package repository
* Installing Kubernetes node components
* Validating each prepared node
