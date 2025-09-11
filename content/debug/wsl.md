# wsl没了

**问题描述：**

windows更新之后wsl没了，文件资源管理器左边栏只剩一个linux的图标，但是点进去是空文件夹

尝试在命令行中打开ubuntu或者运行wsl命令会出现：

```bash
PS C:\WINDOWS\system32> wsl --install 
wsl: WSL 安装似乎已损坏 (错误代码： Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG)。 
按任意键修复 WSL，或 CTRL-C 取消。 
此提示将在 60 秒后超时。
```

```bash
PS C:\WINDOWS\system32> wsl --update 
wsl: WSL 安装似乎已损坏 (错误代码： Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG)。 
按任意键修复 WSL，或 CTRL-C 取消。 
此提示将在 60 秒后超时。 
没有注册类 
错误代码: Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG
```

`SFC /scannow` 和 `DISM /Online /Cleanup-Image /RestoreHealth` 都没问题

**解决办法：禁用并重新启用 WSL 功能**

1. 以管理员身份打开 PowerShell
2. 运行以下命令禁用 WSL 功能：
```powershell
dism.exe /online /disable-feature /featurename:Microsoft-Windows-Subsystem-Linux /norestart
dism.exe /online /disable-feature /featurename:VirtualMachinePlatform /norestart
```
3. 重新启动计算机
4. 以管理员身份打开 PowerShell
5. 运行以下命令启用 WSL 功能
```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```