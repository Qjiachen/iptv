# vscode

## 一、vscode的安装

直接在终端窗口中输入命令：

```shell
sudo snap install code --classic
```

可以免去配置环境变量的麻烦，在任何路径下执行`code .`指令



---

## 二、对于`#include <stdio.h>`报错的问题

1. 可能是未安装gcc库，可在终端中运行：

   ```shell
   sudo apt install gcc
   ```

2. 可能是未更新路径，可在设置的command palette中选择reload windows

3. 如果仍未解决，需要在`c_cpp_properties.jison`中手动添加路径，可根据黄色小灯泡的提示操作



---

# python setupstool

当运行含有`python`指令报错时，可将`python`改为`python3`，或者执行语句：

```shell
sudo apt install python-is-python3
```

或者手动创建symlink：

```shell
sudo ln -s /usr/bin/python3 /usr/bin/python
```

`python`文件安装工具的安装命令：

```shell
sudo apt-get install python3-setuptools
```



---

# Typora

在终端中运行下列指令来安装Typora：

```shell
sudo apt-get update && sudo snap install typora
```

通过这样的方式安装后，就可以直接在终端运行` typora`，例如

```shell
typora ~/Document/test.md
```



----

# firefox

卸载ubuntu自带的firefox：

```shell
sudo snap remove firefox
```

检查firefox是否有残留：

```shell
snap list firefox
```

安装另一个firefox版本：

```shell
sudo apt update && sudo apt install firefox -y
```

修改主题

```shell
./tweaks --firefox
```



# 反馈"失败：拒绝连接"

若碰到正在连接`raw.githubuserconnect.com`拒绝连接的报错，可尝试proxy，格式如：

```shell
export http_proxy=http://192.168.3.250:7890 && export https_proxy=http://192.168.3.250:7890
```

或者不使用proxy，而是在网上查出能与网站连接的节点修改节点

首先去网站http://site.ip138.com/输入raw.githubusercontent.com进行查询

然后将ip加入/etc/hosts

```shell
sudo nano /etc/hosts
```

加入形如：`151.101.76.133 raw.githubusercontent.com`的文本

当sudo的权限不够时，可切换成管理员身份执行

```shell
su
```

出现认证失败是因为密码没有初始化，要先初始化密码：

```shell
sudo passwd root
```



---

# 执行`sudo`时的认证配置

执行`sudo`时不再需要密码的设置

```shell
whoami
sudo vim /etc/sudoers
```

修改成形如以下几行：

```sh
root  ALL=(ALL:ALL) ALL
****  ALL=(ALL:ALL) NOPASSWD:ALL

#%admin ALL=(ALL) ALL

#%sudo ALL=(ALL:ALL) ALL
```

如果不小心改错，不能在获取管理员身份，则需要进入ubuntu recovery mode，选root，执行：

```shell
monut -o remount, rw /
chmod u+w /etc/sudoers
vim /etc/sudoers
mount -o remount, ro /
reboot
```



---

# 文件管理

创建文件夹

```shell
mkdir filename
```

创建文件

```shell
touch filename
```

删除文件夹

```shell
rm -r filename
```

删除文件

```shell
rm filename
```

删除软件

```shell
sudo apt-get install aptitude && sudo aptitude purge name
```

复制文件

```shell
cp filename filename
cp path_filename path_filename
```

复制文件夹

```shell
cp -r path path
```

移动文件

```shell
mv filename path
mv pathfilename path
```

返回上一级目录

```shell
cd ..
```



---

# .desktop文件的创建

在终端中执行以下命令：

```shell
cd /usr/share/applications
sudo touch ./application_name.desktop
sudo vim ./application_name.desktop
```

编辑文本示例：

```sh
[Desktop Entry]
Name=Clash
Type=Application
Exec=/usr/local/clash/Clash-for-Windows-0.20.28-x64-linux/cfw
Icon=/usr/local/clash/Clash-for-Windows-0.20.28-x64-linux/clash.png
```



---

# 双系统时间同步

双系统时间机制存在不足，需要进行以下配置：

```shell
sudo apt update && sudo apt install ntpdate
sudo ntpdate time.windows.com
sudo hwclock --localtime --systohc
```

一次不成功可多试一次





# openwrt配置



```bash
sudo apt update
sudo apt install build-essential clang flex bison g++ gawk gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev python3-distutils rsync unzip zlib1g-dev file wget


# Download and update the sources
git clone https://github.com/openwrt/openwrt

## 下载OpenClash
wget https://github.com/vernesong/OpenClash/archive/master.zip

## 解压
unzip master.zip

## 复制OpenClash软件包到OpenWrt
cp -r OpenClash-master/luci-app-openclash openwrt/package
cd openwrt

# Select a specific code revision
git branch -a
git tag
git checkout v22.03.5

# Update the feeds
./scripts/feeds update -a
./scripts/feeds install -a

# Configure the firmware image and the kernel
make menuconfig

## 选择系统(以 x86_64 为例)
Target System -> x86
Subtarget -> x86_64

## 选择固件的文件系统
## https://openwrt.org/docs/techref/filesystems
Target Images -> squashfs

## 选择构建X86_X64的GRUB固件
Target Images -> Build GRUB images (Linux x86 or x86_64 host only) 

## 选择更小的压缩格式固件，方便复制
Target Images -> GZip images

## 修改软件包可用空间，默认安装会占用100M左右，建议修改扩大，为后续安装其他软件打基础
Target Images -> Root filesystem partition size

## 添加web界面(y键选择n键排除)
LuCI > Collections -> Luci

## 添加兼容性依赖
LuCI > Modules -> luci-compat

## 添加中文
LuCI > Modules -> Translations -> Chinese Simplified

## 添加openclash
LuCI > Applications -> luci-app-openclash  

## 添加主题
LuCI -> Themes

## 添加wget
Nerwork -> File Transfer -> wget-ssl

## 添加kmod-tun，TUN模式必须
Kernel modules -> Network Support -> kmod-tun

## 排除dnsmasq，由于默认会安装dnsmasq-full，这里需要排除dnsmasq，否则会冲突报错。
Base system -> dnsmasq 


make clean

make targetclean

make dirclean

make distclean
```

```bash
#用管道将编译日志传递给tee命令，最终写入到compile.log文件里
make -jN V=s | tee ../compile.log

cat ../compile.log | grep ERROR:
cat ../compile.log | grep failed
```



```shell
./scripts/feeds update -a
./scripts/feeds install -a

make menuconfig

make -j8 download V=s
make -j8 V=s

##WARNING: Install qemu-img to create VDI/VMDK images
sudo apt-get install qemu-utils


//重新配置
rm -rf ./tmp && rm -rf .config
make menuconfig

//完全重新编译
rm -rf ./tmp && rm -rf .config //清理编译配置和缓存
make clean //清理旧的编译固件及文件（在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除/bin和/build_dir目录中的文件）
make dirclean //清理工具及链接（更换架构编译前必须执行。此操作会删除/bin和/build_dir中的文件（make clean)以及/staging-dir、/toolchain、/tmp和/logs中的文件）
make distclean //清理openwrt源码以为的文件（除非是做开发，并打算push到GitHub这样的远程仓库，否则几乎用不到此操作相当于make dirclean外加删除/dl、/feeds目录和.config文件）
git clean -xdf //还原openwrt到初始化状态（如果把源码改坏了，或者长时间没有进行编译时使用）
rm -rf tmp //清理临时文件（删除可执行make menuconfig后产生的临时文件，包括一些软件检索信息，删除后会重新加载package目录下的软件包。若不删除会导致一些新加入的软件包不显示）
rm -rf .config //删除编译配置文件（在不删除的情况下如果取消选择某些组件它的依赖组件不会自动取消，所以对于需要调整组件的情况下建议删除）
find dl -size -1024c -exec ls -l {} \;

```

添加插件

```shell
make menuconfig
//选M
make package/appname/compile V=99

cd /tmp/uploag
opkg update
opkg install appname

//解决依赖
opkg info name

qemu-img convert -f raw 2024.01.20-full-x86-64-kernel-6.1-squashfs-efi.img -O vmdk openwrt.vmdk

```

