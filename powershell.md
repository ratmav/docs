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

# error handling

powershell supports [terminating](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/terminating-errors?view=powershell-6) and [nonterminating](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/non-terminating-errors?view=powershell-6) errors. the key difference is that a terminating error will halt execution, where a nonterminating error will simply print an error message and allow execution to continue. use flags such as `-ErrorAction` in order to achieve desired behavior.

# debugging

powershell uses the `Set-PSBreakpoint` cmdlet, which can be run from a `pwsh` interpreter instance:

```shell
$ pwsh
PS /Users/username> Set-PSBreakpoint -Line 221 ./your_script.ps1
```

on the next run of `your_script.ps1` from the interpreter, execution will pause at the specified line. To list all breakpoints, use `Get-PSBreakpoint`. To clear a specific breakpoint, use `Remove-PSBreakpoint`. To clear all breakpoints, chain the two: `Get-PSBreakpoint | Remove-PSBreakpoint`.
