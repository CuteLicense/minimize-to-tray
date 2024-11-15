# Minimize to tray

本程序将应用程序最小化到 Windows 任务栏通知区域（也非正式地称为“系统托盘”）。

这对于长时间运行的程序可能很有用，可以将其留在后台而无需关注。

它被设计为可编写脚本——如果您想要更具交互性/点击式的东西，您可能更喜欢： [RBTray](http://rbtray.sourceforge.net/).


## 用法

用法:

    minimize-to-tray [/MIN|/NOMIN] [/NOTIFY|/NONOTIFY] "title"|"*suffix"|"prefix*"|"*substring*"

Where:

* `/MIN` - 立即隐藏窗口（默认）。 
* `/NOMIN` - 不立即隐藏窗口（等到您手动最小化它）。 
* `/NOTIFY` -  提醒用户窗口已最小化到通知区域。 
* `/NONOTIFY` - 最小化时不发出警报。 
* `"title"` - 通过精确匹配查找的窗口标题（例如 "计算器" 找到一个具有完全相同标题的窗口）。
* `"*suffix"` - 通过在标题前放置星号来搜索匹配的后缀（例如 "*记事本" 比赛 "未命名 - 记事本"). 
* `"prefix*"` - 通过在标题后放置星号来搜索匹配的前缀（例如 "Signal*" 比赛 "Signal (1)").  
* `"*substring*"` - 通过在标题两侧放置星号来搜索标题中的任何匹配部分（例如 "*watch*" 比赛 "C:\WINDOWS\system32\cmd.exe - wsl watch ps"). 

Effect:

* The window that has a title matching the given suffix will appear to be minimized as an icon in the taskbar notification area (system tray).
* The notification icon and tooltip should match the window's icon and title.
* Left-clicking on the notification icon restores the window.
* When you minimize the window yourself, it will disappear back to the taskbar notification area.
* Right-clicking on the notification icon shows a menu:
  * *Restore* - restores the minimized window.  When you minimize it again, it will return to the notification area.
  * *Close* - sends the window a close signal (it restores it first in case it needs to show UI).
  * *Stop Hiding* - takes the window out of the notification area (it will now be minimized normally) and does not track any future minimizes.  This closes this utility for that window.


## Troubleshooting

* Some windows (e.g. _modern_ UWP apps from Windows Store) don't properly give their icon through the standard API, so a default one from the app is used.

* If using a terminal, your current command could be used as the window's title, so it could match the window being found -- just place a space after the command, or put the title in quotes to prevent this.

* The window must exist before running the utility, so you may need delay during any automated scripts. If using a batch file, you could try something like: `TIMEOUT 5` -- or, if you have an older version of Windows: `CHOICE /C 0 /D 0 /T 5 >NUL`

* You should not need to, but if you have any issues where your application does not automatically go back to the tray when it is later minimized, you can force a hacky *polling mode* with the option `/POLL`.

* If there are any old instances of this tool running, you can clean them up by running the following (do not use the 'force' mode as any tracked application windows will stay hidden!): `taskkill /im minimize-to-tray.exe`

* Additional output for debugging can be seen with these options (which must be the first option given):

  * `/CONSOLE:ATTACH` - attach stdio to the parent console
  * `/CONSOLE:CREATE` - create a console (if 'attach' also specified, then only if that fails)
  * `/CONSOLE:ATTACH-CREATE` - attach stdio to the parent console but, if that fails, create a console

* If the history of the taskbar notification area (*Taskbar settings* / *Select which icons appear on the taskbar*) becomes too polluted with old entries, you can manually clear the cache with the following command (WARNING: this restarts Windows Explorer so you may lose some Explorer windows): 

```bat
reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\CurrentVersion\TrayNotify" /v PastIconsStream /f && taskkill /im explorer.exe /f && start "Restarting" /d "%systemroot%" /i /normal explorer.exe
```

---

  * [danielgjackson.github.io/minimize-to-tray](https://danielgjackson.github.io/minimize-to-tray)
