powershell
==========

# setup

## macos

* install: `brew cask install powershell`
* start: `pwsh`
* create profile: `New-Item -Path $Profile -Type File -Force`
    * profile is written to `$HOME/.config/powershell/Microsoft.PowerShell_profile.ps1`

# configure

powershell terminal and readline colors may conflict with other terminal colorschemes, such as zenburn, solarized, etc. the below snippet should be zenburn-compatible, but at least provides example code for customizing a powershell profile.

```powershell
#!/usr/bin/env pwsh

Set-PSReadlineOption -Colors @{
  Command             = "`e[37m";
  Comment             = "`e[37m";
  ContinuationPrompt  = "`e[37m";
  DefaultToken        = "`e[37m";
  Emphasis            = "`e[37m";
  Error               = "`e[37m";
  Keyword             = "`e[37m";
  Member              = "`e[37m";
  Number              = "`e[37m";
  Operator            = "`e[37m";
  Parameter           = "`e[37m";
  Selection           = "`e[37m";
  String              = "`e[37m";
  Type                = "`e[37m";
  Variable            = "`e[37m"
}

$Host.PrivateData.ErrorForegroundColor    = "DarkRed"
$Host.PrivateData.WarningForegroundColor  = "DarkYellow"
$Host.PrivateData.DebugForegroundColor    = "DarkCyan"
$Host.PrivateData.VerboseForegroundColor  = "Gray"
$Host.PrivateData.ProgressForegroundColor = "DarkCyan"
```
