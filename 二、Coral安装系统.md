# 二、Coral安装系统（Windows）

## 1.1硬件软件准备

```
Windows 10 主机（安装Python3环境）
8GB容量以上的micro SD卡
USB-C电源线
USB-C转USB-A线
WiFi/可用以太网电缆
```

## 1.2其他要求

>1.不建议将键盘和显示器连接到主板，因为系统不是为作为桌面环境运行而设计的，这样做会影响系统性能。因此，我们的文档强调在与开发板（通过串行控制台或 SSH）交互时使用终端。
>2.为了与我们的命令行指令和示例代码中的 shell 脚本兼容，我们建议您在 Windows 上使用 Git Bash 终端，它包含在Git for Windows 中。您应该立即安装它并打开 Git Bash 终端以获取以下所有命令行说明。
>
>>从 Git for Windows v2.28 开始，Git Bash 终端无法直接访问 Python。因此，在安装 Git Bash 后，打开它并运行以下命令以使 Python 可访问：
>>
>>```
>>echo "alias python3='winpty python3.exe'" >> ~/.bash_profile
>>source ~/.bash_profile
>>```
>>
>>

## 2.系统安装

开发板的出厂设置不包括系统映像（它仅包含 U-Boot 引导加载程序），因此您需要使用[Mendel Linux](https://coral.googlesource.com/docs/+/refs/heads/master/ReadMe.md)刷新开发板。

为了简化刷机过程，我们创建了一个运行时系统，该系统在 microSD 卡上运行，并使用 Mendel 系统映像刷机。与 Raspberry Pi 等其他开发板不同，Mendel Linux *不在*microSD 卡上运行——我们仅使用 microSD 卡在内置闪存上安装操作系统——您将在此过程结束时移除 microSD 卡。（如果您没有 microSD 卡，您可以改为[通过 USB 手动刷新电路板](https://coral.ai/docs/dev-board/reflash/#flash-a-new-board)。）

>**注意：** 此过程仅适用于新板。如果您已经安装了 Mendel，这将 **删除所有系统和用户数据**— 如果您想保留您的用户数据

使用micro SD卡安装步骤如下:

> 1.下载并解压SD卡镜像文件：[镜像文件](H:\zhuomian\AI Edge Computing\Related Software\coral刷机系统\enterprise-eagle-flashcard-20210518200035.zip)
>
> 2.使用[balenaEtcher](H:\zhuomian\人工智能      边缘计算\Related SoftwarebalenaEtcher-Setup-1.5.122.exe)程序将 `flashcard_arm64.img`文件[刷](https://www.balena.io/etcher/)入您的 microSD 卡。这需要 5-10 分钟，具体取决于您的 microSD 卡和适配器的速度。
>
> 3.刷卡时，请确保您的开发板已完全拔下，并将启动模式开关更改为从 SD 卡启动，如图 1 所示。
>
> | 引导模式 | 开关1 | 开关2 | 开关3 | 开关4 |
> | -------- | ----- | ----- | ----- | ----- |
> | SD卡     | ON    | OFF   | ON    | ON    |
>
> ![图1：启动开关模式为SD卡模式](H:\zhuomian\AI Edge Computing\Related Software\coral刷机系统\SD卡模式-开关.jpg)
>
> 4.成功将映像刷入SD卡之后，安全地将其从计算机中取出并将其插入开发板（卡的针脚朝向开发板）——开发板应该还**没有**通电。
>
> ![microSD卡插槽在底部](H:\zhuomian\AI Edge Computing\Related Software\coral刷机系统\microSD 卡插槽.jpg)
>
> 5.【**可选**】：如果想要在刷新板子时查看引导加载程序日志，请将显示器连接到板的 HDMI 端口或[连接到板的串行控制台](https://coral.ai/docs/dev-board/serial-console)。在下一步为电路板供电之前，什么都不会出现。
>
> 6.通过将 2-3 A 电源线连接到标有“PWR”的 USB-C 端口为电路板供电（见图 3）。电路板的红色 LED 应该亮起。
>
> **注意：**请勿尝试通过将电路板连接到计算机来为电路板供电。
>
> 当电路板启动时，它会加载 SD 卡映像并开始刷新电路板的 eMMC 内存。（它应该在 5-10 分钟内完成，具体取决于您的 microSD 卡的速度。）
>
> 当电路板关闭并且红色 LED 熄灭时，您就会知道它已完成。
>
> ![连接到电源的开发板](H:\zhuomian\AI Edge Computing\Related Software\coral刷机系统\devboard-power-co.jpg)
>
> 7.当Dev Board板子上的LED电源灯熄灭时，拔下电源线以及microSD卡。
>
> 8.将启动模式切换到eMMC模式，如下：
>
> | 引导模式 | 开关1 | 开关2 | 开关3 | 开关4 |
> | -------- | ----- | ----- | ----- | ----- |
> | eMMC     | ON    | OFF   | OFF   | OFF   |
>
> ![eMMC启动模式](二、Coral安装系统.assets/Dev board eMMC.PNG)
>
> 9.将电路板连接到电源，它现在应该会启动 Mendel Linux。
>
> 刷机后第一次启动大约需要 3 分钟（后续启动时间要快得多）。

## 3.安装MDT

MDT 是一个命令行工具，可帮助您与开发板进行交互。例如，MDT 可以列出连接的设备，在板上安装 Debian 软件包，并在板上打开一个 shell 终端。

您可以在您的主机上安装 MDT，如下所示：

```
python -m pip install --user mendel-development-tool
```

您可能会看到`mdt`安装在`PATH` 环境变量中的警告。如果是这样，请确保将给定的位置添加到您的 中`PATH`，以适合您的操作系统。

>**Windows 用户：** 如果你使用 Git Bash，你需要一个别名来运行 MDT。在您的 Git Bash 终端中运行此命令：
>
>```
>echo "alias mdt='winpty mdt'" >> ~/.bash_profile
>source ~/.bash_profile
>```

# 三、Coral安装系统（USB手动刷新--虚拟机Linux-Ubuntu18.04）

## 1.硬件软件要求

```
一台运行 Linux (安装了 Python 3)
一个 USB-C 电源线（2 A / 5 V）
一根 USB-C 转 USB-A 电缆
一根 USB-micro-B 转 USB-A 线缆——确保该线缆支持数据传输（Dev board-1904之前版本需要）
```

主机上还需要以下软件工具：

>（1）串行控制台程序`screen`， `picocom`或PuTTY（以及许多其他）。Linux系统建议使用screen,screen的安装方式如下：
>
>```
>sudo apt-get install screen
>```
>
>（2）最新的`fastboot`工具。
>
>在 Linux 上，您可以按如下方式安装：
>
>```
>sudo  apt-get install fastboot
>```
>
>现在验证它是否有效：
>
>```
>fastboot --version
>```
>
>注意：官网版本要求需要28.0.2或更高，--version之后如果不是可以通过一下升级即可：
>
>```
>sudo apt-get update
>sudo apt-get dist-upgrade
>```
>
>（3）还要配置您的Linux系统以与电路板通信：
>
>运行以下命令添加fastboot所需的udev规则：
>
>```
>sudo sh -c "echo 'SUBSYSTEM==\"usb\", ATTR{idVendor}==\"0525\", MODE=\"0664\", \GROUP=\"plugdev\", TAG+=\"uaccess\"' >> /etc/udev/rules.d/65-edgetpu-board.rules"
>sudo udevadm control --reload-rules && sudo udevadm trigger
>```
>
>还要通过运行以下命令确保您的 Linux 用户帐户在`plugdev`和 `dialout`系统组中：
>
>```
>sudo usermod -aG plugdev,dialout <username>
>```
>
>注意：有些版本的Linux系统可能提示错误（Ubuntu18.04），只需将<>去掉即可.

## 2.设置正确的启动模式

在开始刷机过程之前，请验证以下内容：

```
该板已完全拔下（未通电且未连接到您的计算机）。
启动模式开关设置为 eMMC 模式（见图 ）：
```

| 引导模式 | 开关1 | 开关2 | 开关3 | 开关4 |
| -------- | ----- | ----- | ----- | ----- |
| eMMC     | ON    | OFF   | OFF   | OFF   |

![eMMC启动模式](C:\Users\Administrator\Pictures\Dev board eMMC.PNG)

>**注意：**除非得到指示，否则请勿为电路板供电或连接任何电缆。

## 3.启动fastboot模式

Dev board的序列号以“9410”或更高开头，那么它是在 2019 年 4 月 10 日或之后生产的，可以自动启动fastboot模式，因此可以直接执行第4步的flash脚本。

## 4.执行flash脚本

准备好刷写电路板

#### 1.**连接 USB-C 电缆。**

>使用 USB-C 电缆将主机连接到开发板上标有“OTG”的 USB-C 数据端口。（如果您跳过上述过程来[启动 fastboot](https://coral.ai/docs/dev-board/reflash/#1-initiate-fastboot-mode)，那么您还需要将 2 - 3A 电源线插入标有“PWR”的 USB-C 端口，并等待片刻让开发板通电。）连接到开发板的 USB-C 数据线和电源线如图
>
>![连接到开发板的 USB-C 数据线和电源线](H:\zhuomian\MobileFile\devboard-power-data-co.jpg)

#### 2.**验证 fastboot 是否可以看到您的设备。**

>在您的主机上打开一个新终端并执行以下命令：
>
>```
>fastboot devices
>```
>
>您应该会看到这样打印的一行（您的数字会有所不同）：
>
>```
>151e99d6f6c99eed      fastboot
>```
>
>![fastboot启动图](C:\Users\Administrator\Pictures\fast.PNG)
>
>

#### 3.**下载并刷新系统映像。**

>从同一个终端，执行以下命令：
>
>```
>cd ~/Downloads
>curl -O https://mendel-linux.org/images/enterprise/eagle/enterprise-eagle-20210518200035.zip
>unzip enterprise-eagle-20210518200035.zip
>cd enterprise-eagle-20210518200035
>bash flash.sh
>```
>
>整个过程开发板会进行重启等各种过程，大概敬礼五分钟可以完成。期间会在终端显示刷新过程，刷新完成会在终端提示。以下是正常过程图：
>
>![过程图](C:\Users\Administrator\Pictures\boot.PNG)
>
>![过程图](C:\Users\Administrator\Pictures\Dev.PNG)
>
>![过程图](C:\Users\Administrator\Pictures\b.PNG)
>
>完成后，开发板再次重启,重新连接虚拟机Linux系统。至此系统刷新结束。

## 5.安装MDT（Linux-Ubuntu）

开发板再次重启之后，可以在主机安装MDT工具实现与Google Dev board交互的命令行窗口，通过mdt命令行可以查看在线设备、实现shell终端和安装Debian软件包等功能。

安装MDT工具可以通过一下命令:

```
python3 -m pip install --user mendel-development-tool
```

>安装后部分可能提示一条警告，提示mdt安装没有有效的环境变量，如果有此情况，确保将指定位置添加到环境变量PATH中。在Linux系统可以通过一下命令实现环境变量的添加：
>
>```
>echo 'export PATH="$PATH:$HOME/.local/bin"' >> ~/.bash_profile
>source ~/.bash_profile
>```

# 四、Google Dev Board连接与配置（Windows/Linux通用）

## 1.通过MDT工具连接到Dev board

通过上一步已经完成Mendel系统与MDT工具安装，现在可以通过MDT工具与板子进行shell交互，使用MDT是一种生成OpenSSH公钥/私钥对建立SSH连接的简单方法。（根据上述步骤完成MDT工具安装即可接下来的连接与配置）

首先，通过Type-C连接Dev Board的标有OTG的数据口，并且连接电源线。（如下图所示）



![MDT连接](H:\zhuomian\AI Edge Computing\Coral\汇报截图\devboard-power-data-co.jpg)

现在可以通过您的主机运行以下命令来显示您的设备：

```
mdt devices
```

正常情况下你应该看到您的设备名称和IP地址，如下所示。如果没有，先确保上一步MDT工具是否正确安装，然后检查设备正常运行之后再次运行上面的命令。

如果全部检查之后，仍然没有看到您的设备打印出来，可能是因为系统正在配置Mendel,可以通过运行*mdt wait-for-device*命令查看。当您的设备准备就绪时，它会打印“找到一个设备”。（主机名称是随机生成的，这样做是为了确保每台设备拥有唯一的主机名，当然后期也可以通过Linux命令更改主机名）

```
lime-finch        (192.168.100.2)
```

现在可以通过以下命令连接到Dev Borad：

```
mdt shell
```

稍等一会您就可以看到开发板的shell提示以及Linux命令行主机名更换为mendel系统名称。

>注意：您必须至少通过USB上的MDT连接到Dev Board一次，之后才可以通过其他方法建立SSH连接，使用USB允许MDT生成SSH公钥/私钥对并将其推送到板子的*authorized_keys*文件中，然后就可以使用SSH进行身份验证。

## 2.连接到网络

连接到网络的目的是您需要在线下载开发板系统更新、模型和示例文件。

网络连接有两种方法：1.通过以太网电缆直接连接到开发板。2.通过WiFi连接。（WiFi可以通过以下命令进行连接）

```
nmtui
```

然后会弹出一个窗口选择*Activate a connection*之后回车，然后显示的就是可以连接的WiFi（界面类似手机连接WiFi），选择WiFi(wlan0)并进行连接之后*OK*退出。

或者，可以通过以下命令行直接连接到已知名称的网络中：

```
nmcli dev wifi connect <NETWORK_NAME> password <PASSWORD> ifname wlan0
```

检验是否连接成功，可以在shell终端输入一下命令：

```
nmcli connection show
```

成功连接之后，您应该会在输出中看到您选择网络，如下所示：

```
NAME                UUID                                  TYPE             DEVICE
MyNetworkName       61f5d6b2-5f52-4256-83ae-7f148546575a  802-11-wireless  wlan0
```

设备名称是wlan0（表示WiFi连接）或者ether0（表示以太网连接）

## 3.更新Mendel软件

其中部分软件是通过与系统映像分开的Debian软件包提供的，因此可以运行以下命令确保您拥有最新的软件：

```
sudo apt-get update
sudo apt-get dist-upgrade
```

现在您已经准备好在Edge TPU上运行TensorFlow Lite模型！

## 4.运行视频demo

可以通过Dev Board的shell终端运行以下命令进行演示Edge TPU的视频demo：

>注意：在运行之前可以在浏览器中输入第一步获得的设备IP（192.168.100.2）然后加上端口号**：4664**进行视频demo访问，正常是无法看到视频demo的演示，然后可以运行以下命令，您应该会在浏览器中看到正在播放的交通车辆视频检测，MobileNet模型会在您的开发板上执行实时检测每辆车。

```
edgetpu_demo --stream
```

然后在您连接到开发板之后，通过USB使用MDT连接到开发板，打开浏览器输入第一步获得的设备IP（192.168.100.2）然后加上端口号**：4664**。如果您使用其他方式（LAN上的SSH或者以太网）连接到开发板，可以直接使用端口4664在浏览器中输入设备IP地址进行访问。

或者，您也可以通过将显示器连接到开发板，就可以在显示器上面查看演示，命令如下;

```
edgetpu_demo --device
```

>注意：操作过程需要避免直接接触散热器。无论风扇是否运行都会使散热器很热，应避免触碰导致烫伤。
