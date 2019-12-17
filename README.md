## tt-demo

### Summary:

This is an Ansible demo which configures four Cumulus VX switches in a two spine, two leaf configuration with BGP using J2 / Jinja2 templates

### Network Diagram:

![Network Diagram](https://github.com/cloudofyou/tt-demo/blob/master/documentation/TT-Diag-01.jpg)

### Initializing the Linux / libvirt demo environment:

First, make sure that the following is currently running on your machine:

1. This demo was tested on a Ubuntu 16.04 VM w/ 4 processors and 32Gb of Diagram

2. Following the instuctions at the following link:

https://docs.cumulusnetworks.com/cumulus-vx/Development-Environments/Vagrant-and-Libvirt-with-KVM-or-QEMU/

3. Download the latest Vagrant, 2.2.5, from the following location:

    https://www.vagrantup.com/

4 Copy the Git repo to your local machine:

    ```git clone https://github.com/cloudofyou/tt-demo```

5. Change directories to the following

    ```tt-demo```

6. Run the following:

    ```./start-vagrant-libvirt-poc.sh```

### Running the Ansible Playbook

1. SSH into the oob-mgmt-server:

    ```cd vx-libvirt-simulation```   
    ```vagrant ssh oob-mgmt-server```

2. Copy the Git repo unto the oob-mgmt-server:

    ```git clone https://github.com/cloudofyou/tt-demo```

3. Change directories to the following

    ```tt-demo/automation```

4. Run the following:

    ```./provision.sh```

This will bring run the automation script and configure the two switches with BGP.

### Troubleshooting

Helpful NCLU troubleshooting commands:

- net show route
- net show bgp summary
- net show interface | grep -i UP
- net show lldp

Helpful Linux troubleshooting commands:

- ip route
- ip link show
- ip address <interface>

The BGP Summary command will show if each switch had formed a neighbor relationship:

```
cumulus@t-leaf-02:mgmt-vrf:~$ net show bgp summary
show bgp ipv4 unicast summary
=============================
BGP router identifier 10.12.12.12, local AS number 65012 vrf-id 0
BGP table version 6
RIB entries 9, using 1368 bytes of memory
Peers 2, using 39 KiB of memory

Neighbor          V         AS MsgRcvd MsgSent   TblVer  InQ OutQ  Up/Down State/PfxRcd
t-spine-01(swp12) 4      65111     251     252        0    0    0 00:12:14            3
t-spine-02(swp22) 4      65111     251     252        0    0    0 00:12:14            3

Total number of neighbors 2

```

One should see that the corresponding loopback route is installed with two next hops / ECMP:

```
cumulus@t-leaf-02:mgmt-vrf:~$ net show route
show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR,
       > - selected route, * - FIB route

K>* 0.0.0.0/0 [0/0] via 10.255.1.1, vagrant, 00:12:42
B>* 10.1.1.1/32 [20/0] via fe80::4638:39ff:fe00:3, swp12, 00:12:39
B>* 10.2.2.2/32 [20/0] via fe80::4638:39ff:fe00:7, swp22, 00:12:38
B>* 10.11.11.11/32 [20/0] via fe80::4638:39ff:fe00:3, swp12, 00:12:38
  *                       via fe80::4638:39ff:fe00:7, swp22, 00:12:38
C>* 10.12.12.12/32 is directly connected, lo, 00:12:42
C>* 10.255.1.0/24 is directly connected, vagrant, 00:12:42
```

### Shutdown

1. To shutdown the demo, run the following command from the vx-simulation directory:

    ```vagrant destroy -f```
