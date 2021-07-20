powershell
==========

[documentation](https://docs.microsoft.com/en-us/powershell/)

## installation

### macos

* install: `brew cask install powershell`
* start: `pwsh`
* typical profile location: `$HOME/.config/powershell/Microsoft.PowerShell_profile.ps1`

## configuration

### location

* check if profile exists/locate profile: `Test-Path $profile`
    * **note**: powershell has _six_ possible locations for the flat file; let the shell tell you what it's using.
* create a new profile if one doesn't exist: `New-Item -Path $profile -Type File -Force`

### colors

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

### useful aliases

#### git bash

```powershell
# open git bash in powershell session (v. a new window).
Set-Alias -Name git-bash -Value "C:\Program Files\Git\bin\bash.exe"
```

**note**: the location of `bash.exe` may be be different; salt to taste.

## tools

* linter: [psscriptanalyzer](https://github.com/PowerShell/PSScriptAnalyzer)

## use

### common commands

|bash|powershell|
|----|----------|
|`ls`|`Get-ChildItem`|
|`grep -Rn foo ./`|`Select-String -Path .\* -Pattern foo`|
|`whoami`|`$env:UserName`|
|`pwd`|`$pwd`|
|`cd your/path`|`Set-Location your\path`|
|`history`|`Get-History`|
|`sha`, `shasum`, etc.| `Get-FileHash`|

### current pipeline item

For reference, the `$_` is the same as `$PSItem`, which is a reference to the current item in a powershell pipeline. in addition to the below debugging example, you can access the current item in an iterator in a functional way like so: `1,2,3 | %{ write-host $_ }` or `1,2,3 | %{ write-host $PSItem }`.

### error handling

powershell supports [terminating](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/terminating-errors?view=powershell-6) and [nonterminating](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/non-terminating-errors?view=powershell-6) errors. the key difference is that a terminating error will halt execution, where a nonterminating error will simply print an error message and allow execution to continue. use flags such as `-ErrorAction` in order to achieve desired behavior.

### debugging

powershell uses the `Set-PSBreakpoint` cmdlet:

```shell
$ pwsh
PS /Users/username> Set-PSBreakpoint -Line 221 ./your_script.ps1
```

on the next run of `your_script.ps1` from the interpreter, execution will pause at the specified line. To list all breakpoints, use `Get-PSBreakpoint`. To clear a specific breakpoint, use `Remove-PSBreakpoint`. To clear all breakpoints, chain the two: `Get-PSBreakpoint | Remove-PSBreakpoint`.

also, inspecting an object can be done the following way, demonstrated using an `Exception` object found inside a try/catch block: `$_.Exception | Select -Property * `.
