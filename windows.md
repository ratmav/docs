windows
=======

## shell swapping

most terminal emulators that **aren't** powershell will default to `cmd.exe`, which can be used to launch various tui shells that are installed:

* `bash` usually launches the bash binary that ships with wsl 2 that is installed as part of the docker desktop installation.
* `powershell` launches...powershell.
* `'C:\Program Files\Git\bin\bash.exe'` will launch the bash binary that ships with [Git for Windows](https://gitforwindows.org/).

## powershell permissions

to run scripts written locally, but verify signatures on scripts pulled from remote locations:

```powershell
Set-ExecutionPolicy RemoteSigned
```

## enable natural scrolling

```powershell
# has to be done for each mouse while it's connected.
Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Enum\HID\*\*\Device` Parameters FlipFlopWheel -EA 0 | ForEach-Object { Set-ItemProperty $_.PSPath FlipFlopWheel 1 }
```

## remap caps lock to control

```powershell
# may need to be done for each keyboard while it's connected.
$hexified = "00,00,00,00,00,00,00,00,02,00,00,00,1d,00,3a,00,00,00,00,00".Split(',') | % { "0x$_"};

$kbLayout = 'HKLM:\System\CurrentControlSet\Control\Keyboard Layout';

New-ItemProperty -Path $kbLayout -Name "Scancode Map" -PropertyType Binary -Value ([byte[]]$hexified);
```
