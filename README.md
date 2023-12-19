# CMPE 283 Assignment 2

#### **Performed the step to generate the RSA key-pair**

![assignment2-1](https://github.com/manikantanynala97/CMPE283-Assignment2/assets/90610801/9676ca01-05db-49ce-892a-d2e4e7ae2fdd)


- Assignment 2 changes the behavior of the **cpuid** instruction for the following cases:
  - CPUID leaf node(`%eax=0x4fffffff`)
    - Return the total number of exits for all types in **%eax**.
  - CPUID lead node(`%eax=0x4ffffffe`)
    - Return the high 32 bits of the total time spent processing all exits in **%ebx**.
    - Return the low 32 bits of the total time spent processing all exits in **%ecx**.
    - **%ebx** and **%ecx** return values are measured in processor cycles, across all vCPUs.

<br />

## Question 1:

Established the kernel successfully and installed required kernel modules in the virtual machine. Analyzed and researched the code for the assignment, implementing necessary modifications as discussed. Updated the codebase to incorporate the changes, ensuring alignment with the project requirements. Also, revised the README.md file and created comprehensive documentation to provide clear instructions and insights into the project updates and functionality.

Established the kernel successfully and comprehensively reviewed the relevant Canvas lecture, gaining a clear understanding of the required steps. Conducted in-depth research on CPUID instructions and CPU leaf nodes, pinpointing the specific modifications essential for completing the assignment. Implemented the necessary changes in the cpuid.c and vmx.c files, aligning the codebase with the project specifications.

The Linux kernel code is forker and is in [this repository](https://github.com/manikantanynala97/linux.git)
<br />

## Question 2: (Steps used to complete the assignment)

Run this code in the VM

```
git clone https://github.com/manikantanynala97/linux.git
```

Get the correct Linux version

```
ls /boot/
```

Copy the config file to root directory

```
cp /boot/config-5.11.0-1029-gcp .config
```

```
sudo apt install make
```

```
make oldconfig
```

Install all the required libraries

```
sudo apt install gcc
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install bison
sudo apt-get install qemu-kvm qemu virt-manager virt-viewer libvirt-daemon
```

Make the modules parallely using 8 cores in our machine
Update the cpuid.c and vmx.c files and make the modules

```
cd arch
cd x86
cd kvm
vi cpuid.c
vi vmx/vmx.c
cd ..
make modules
sudo make -j 8 modules
sudo make install
```

Create a user-data file and save the nested VM login config

```
#cloud-config
chpasswd: { expire: False }
ssh_pwauth: True
```

Download and Install the nested VM

```
wget https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img
sudo virt-customize -a bionic-server-cloudimg-amd64.img --root-password password:newpass
sudo virt-customize -a bionic-server-cloudimg-amd64.img --uninstall cloud-init

cloud-localds user-data.img user-data
sudo qemu-system-x86_64 -enable-kvm -hda bionic-server-cloudimg-amd64.img -drive "file=user-data.img,format=raw" -m 512 -curses -nographic
```

- username: root
- password: newpass

  Inside the Nested VM Install cpuid to test

  ```
  sudo apt-get update
  sudo apt-get install cpuid
  ```

  Test using CPUID

  ```
  cpuid -l 0x4FFFFFFF
  cpuid -l 0x4FFFFFFE
  ```

### **Output:**

![output1](![assignment2-2](https://github.com/manikantanynala97/CMPE283-Assignment2/assets/90610801/7caa5c99-c820-4b35-8683-fc0de53f5f08)
)

![output2](![assignment2-3](https://github.com/manikantanynala97/CMPE283-Assignment2/assets/90610801/fbb1d01b-84e3-4f2d-9fbc-1713373d5632)
)
