+++
title = 'Stop Hyper V'
date = 2024-09-12T14:38:59+08:00
categories = ["android开发"]
tags = ["配置","Hyper-V"]
+++
# 配置硬件加速
## 使用SDK 管理器安装
1.选择Tools>SDK Manger<br>
<img src="https://i.postimg.cc/g2zgzhDN/screenshot-9.png" />

2.点击 SDK Tools 标签页，然后选择 Android Emulator Hypervisor Driver<br>
<img src="https://i.postimg.cc/YCCxKHrP/screenshot-10.png" />

3.点击 OK，以下载并安装 Android Emulator Hypervisor Driver。<br>

4.安装后，返回命令行中使用以下命令，确认驱动程序能正常运行：
```powershell
sc query aehd
```
<img src="https://i.postimg.cc/HWt5phh1/screenshot-11.png" />
如何出现错误先尝试关闭 Hyper-V

### 禁用Hyper-V

#### 在控制面板中禁用 Hyper-V

1.在dos窗口或者powershell中运行命令

```powershell
control
```

2.点击程序

<img src="https://i.postimg.cc/x81RPD4M/screenshot-5.png" />

3.点击启用或者关闭Windos功能

<img src="https://i.postimg.cc/dVjgnvWS/screenshot-6.png"/>

4.展开 Hyper-V，展开 Hyper-V 平台，然后清除“Hyper-V 虚拟机监控程序”复选框。
<img src="https://i.postimg.cc/QxLP3Vwy/screenshot-7.png" />

### 在PowerShell 中禁用 Hyper-V

1.提升PowerShell 窗口的权限为管理员

```powershell
 sudo
```

2.运行以下命令

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor
```


`参考文献`<br>

[虚拟化应用程序无法与 Hyper-V、Device Guard 和 Credential Guard 协同工作](https://learn.microsoft.com/zh-cn/troubleshoot/windows-client/application-management/virtualization-apps-not-work-with-hyper-v)