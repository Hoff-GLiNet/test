# Under development, please do not use

## Development

### Requirements

You need the following tools to compile OpenWrt, the package names vary between distributions. A complete list with distribution specific packages is found in the [Build System Setup](https://openwrt.org/docs/guide-developer/build-system/install-buildsystem) documentation.

```
$ sudo apt install binutils bzip2 diff find flex gawk gcc-6+ getopt grep install libc-dev libz-dev make4.1+ perl python3.6+ rsync subversion unzip which libncurses5-dev zlib1g-dev gawk gcc-multilib g++-multilib flex git-core gettext libssl-dev ocaml sharutils re2c -y
```

And This development tools requires Python 3.6 or higher! Please use the following command to check,

```
$ python3 --version
Python 3.6.13

$ ls -l /usr/bin/python3*
 /usr/bin/python3 -> python3.6
 /usr/bin/python3.5
 /usr/bin/python3.5-config -> x86_64-linux-gnu-python3.5-config
 /usr/bin/python3.5m
 /usr/bin/python3.5m-config -> x86_64-linux-gnu-python3.5m-config
 /usr/bin/python3.6
 /usr/bin/python3.6-config -> x86_64-linux-gnu-python3.6-config
 /usr/bin/python3.6m
 /usr/bin/python3.6m-config -> x86_64-linux-gnu-python3.6m-config
 /usr/bin/python3.9
 /usr/bin/python3.9-config -> x86_64-linux-gnu-python3.9-config
 /usr/bin/python3-config -> python3.5-config
 /usr/bin/python3m -> python3.5m
 /usr/bin/python3m-config -> python3.5m-config
```

### Quickstart

1. Clone repository.

```
$ git clone https://github.com/gl-inet/gl-infra-builder.git
```

```
$ cd gl-infra-builder
```

2. If you have a [offline dl folder](https://sources.openwrt.org/) and corresponding packages, you can make a symlink to feeds/dl (assuming the dl location is /data/dl), it will make compilation easier. If there is no dl and corresponding packages, ignore this command.

```
$ ln -sf /data/dl feeds/
```

3. Setup, the command will auto download openwrt-19.07.7 by default and auto config, and then patch all the the GL product Patches

```
$ python3 setup.py
$ cd openwrt-19.07/openwrt-19.07.7/
```

Note: If you want to use different branch, please use the -c parameter, for example. If you want to use 18.06

```
$ python3 setup.py -c config-18.x.yml
```

4. Generate your target configuration. (For the following content, we will continue to take 19.07 as an example)

```
$ ./scripts/gen_config.py list		# show available profile
$ ./scripts/gen_config.py <target_profile> <function_profile>
```
Note: excute ./scripts/gen_config.py list command, you can get Target Profiles and Function Profiles. Target Profiles is you want to compile products, don't modify. Function Profiles is you want to add/delete packages, you can modify. Those files in the **profiles** directory.

For example, If you want to compile GL-AR150 product you can use command:
```
$ ./scripts/gen_config.py target_ar71xx_gl-ar150
```

If you want to compile GL-AR150 product and add some packages, you can modify profiles/glinet_ar150.yml
For example, you can add curl kmod-use-serial-ch341 qos-scripts and delete dnsmasq
```
---
description: Add the glinet dependencies
feeds:
  - name: glinet
    path: ../feeds_dir/glinet

packages:
  - helloworld
  - curl
  - kmod-use-serial-ch341

diffconfig: 
  CONFIG_PACKAGE_qos-scripts=y
  CONFIG_PACKAGE_dnsmasq=n
```
and then  excute the following command

```
$ ./scripts/gen_config.py target_ar71xx_gl-ar150 glinet_ar150
```

If you feel use Target Profiles and Function Profiles is complicated, you can excute **make menuconfig** to chose the product and other packages.

6. Run `make` to build your firmware.

