# Ruvchain for Ubiquiti EdgeOS / VyOS

### Introduction

This package provides ruvchain support on supported Ubiquiti EdgeOS 2.x, VyOS 1.3 and potentially other Vyatta-based routers.  It is integrated with the command line interface (CLI) allowing ruvchain to be configured through the standard configuration system.

### Compatibility

|                                  | Architecture | Tested |                      Notes                                    |
|----------------------------------|:------------:|:------:|:-------------------------------------------------------------:|
|    EdgeRouter X (ER-X/ER-X-SFP)  |    mipsel    |  Yes   | Tested with EdgeOS 2.0.9, requires EdgeOS 2.x                 |
|    EdgeRouter (ER-Lite/ER-4 etc) |    mips      |  No    | Requires EdgeOS 2.x                                           |
|    VyOS                          |    amd64     |  Yes   | Tested with VyOS 1.3-rolling-202101                           |
|    VyOS                          |    i386      |  No    |                                                               |

### Install / Upgrade

Either download or build a release and copy it to the router, then install/upgrade it:
```
sudo dpkg -i ruvchain-edgeos2x-x.x.x-xxxxxx.deb # EdgeOS
sudo dpkg -i ruvchain-vyos13-x.x.x-xxxxxx.deb   # VyOS
```

### Initial

Start by creating the default configuration on the interface (replacing `tunX` with your chosen TUN adapter):
```
configure
set interfaces ruvchain tunX
set interfaces ruvchain tunX description ruvchain
commit
```
This automatically generates a new private key and then populates the IPv6 address, public key and private key into the config.

### Configuration

Configuration changes should be made to `/config/ruvchain.tunX.conf` by hand. To make effective, restart ruvchain (replacing `tunX` with your chosen TUN adapter):
```
restart ruvchain tunX
```

### Masquerade

If you want to allow other IPv6 hosts on your network to communicate through ruvchain, you can configure an IPv6 masquerade rule. All traffic sent from other hosts on the network through the ruvchain interface will be NAT'd.

For example:
```
configure
set interfaces ruvchain tun0 masquerade from xxxx:xxxx:xxxx::/48
commit
```
If you have multiple IPv6 subnets, then they can be configured individually by setting multiple `masquerade from` source ranges. Both private/ULA and public IPv6 subnets are acceptable.

