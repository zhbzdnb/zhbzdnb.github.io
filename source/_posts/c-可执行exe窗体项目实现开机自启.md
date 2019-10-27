---
title:  c#可执行exe窗体项目实现开机自启
date: 2019-10-27 17:55:50
tags: c#
categories: c#
---

由于公司项目是一个安装包形式的javaweb，安装完成后会给客户一个图形化启动web服务的界面，也就是c#窗体 一个exe；最近发现了一个问题我的项目和其他的类似项目不能共存，排查发现是mysql启动使用默认端口 默认服务名；改了端口和服务发现用窗体启动成功但显示服务没有启动。无奈要了份窗体源码。的确是窗体判断服务名是否存在打印相应提示，很简单的改写后发现原本已有的自启无效了。

弄了四天，本身这项目不是我写的，熟悉了一下找了c#设置应用程序的方法

3种 

1、 最简单最方便  给电脑启动文件夹放一个快捷方式，这个实测win7 win10 都可以

目录 C:\Users\你的用户名\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup

c#实现也就是生成一个快捷方式并放到这个目录

代码要引用 IWshRuntimeLibray

```
/// <summary>
/// 将文件放到启动文件夹中开机启动
/// </summary>
/// <param name="setupPath">启动程序</param>
/// <param name="linkname">快捷方式名称</param>
/// <param name="description">描述</param>
public void SetSetupWindowOpenRun(string setupPath, string linkname, string description)
{
 string desktop = Environment.GetFolderPath(Environment.SpecialFolder.Startup) + "\\" + linkname + ".lnk";
 if (System.IO.File.Exists(desktop))
  System.IO.File.Delete(desktop);
 IWshRuntimeLibrary.WshShell shell;
 IWshRuntimeLibrary.IWshShortcut shortcut;
 try
 {
  shell = new IWshRuntimeLibrary.WshShell();
  shortcut = (IWshRuntimeLibrary.IWshShortcut)shell.CreateShortcut(desktop);
  shortcut.TargetPath = setupPath;//程序路径
  shortcut.Arguments = "";//参数
  shortcut.Description = description;//描述
  shortcut.WorkingDirectory = System.IO.Path.GetDirectoryName(setupPath);//程序所在目录
  shortcut.IconLocation = setupPath;//图标   
  shortcut.WindowStyle = 1;
  shortcut.Save();
 }
 catch (Exception ex)
 {
  System.Windows.Forms.MessageBox.Show(ex.Message, "友情提示");
 }
 finally
 {
  shell = null;
  shortcut = null;
 }
}
```

简单快捷、

2、 使用写注册表方式实现

代码网上一堆我就不放了。

遇到的知识和坑

自启动注册表位置:

HKEY_CURRENT_USER/Software/Microsoft/Windows/CurrentVersion/Run

当前登录win用户

HKEY_LOCAL_MACHINE/SOFTWARE/Microsoft/Windows/CurrentVersion/Run

所有win用户

HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Microsoft/Windows/CurrentVersion/Run

win32位所有用户

为了兼容32位系统我们选择第3种

项目由32位调试以下代码获得的是

```
RegistryKey rk = Registry.LocalMachine;
                RegistryKey rk2 = rk.CreateSubKey(@"Software\Microsoft\Windows\CurrentVersion\Run");
```

HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Microsoft/Windows/CurrentVersion/Run

3、 将应用程序写成服务

这个我没用尝试应该也是可以的.

坑：最后我快绝望了，问了以前写了这个自启的老大。他给提示说是项目里面绝对路径和相对路径的问题，自启的时候 路径是在c系统盘的位置，相对路径取不到相应的东西，我一打日志还真是这样，改成绝对路径 ok 完成！