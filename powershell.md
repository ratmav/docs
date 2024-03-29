powershell
==========

[documentation](https://docs.microsoft.com/en-us/powershell/)

## installation

### macos

* install: `brew cask install powershell`
* start: `pwsh`
* typical profile location: `$HOME/.config/powershell/Microsoft.PowerShell_profile.ps1`

## configuration

### permissions

to run scripts written locally, but verify signatures on scripts pulled from remote locations:

```powershell
Set-ExecutionPolicy RemoteSigned
```

### profile

**note**: powershell has _six_ possible profile locations; let the shell tell you what it's using.

* check if profile exists: `Test-Path $profile`
* create a new profile if one doesn't exist: `New-Item -Path $profile -Type File -Force`
* list all profile locations: `$profile | Format-List -Force`
* open current profile in default editor: `ii $profile`

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

### aliases

#### shell hopping

most terminal emulators that **aren't** powershell will default to `cmd.exe`, which can be used to launch various tui shells that are installed:

* `bash` usually launches the bash binary that ships with wsl 2 that is installed as part of the docker desktop installation.
* `powershell` launches...powershell.
* `'C:\Program Files\Git\bin\bash.exe'` will launch the bash binary that ships with [Git for Windows](https://gitforwindows.org/).

```powershell
# open git bash in powershell session (v. a new window).
Set-Alias -Name git-bash -Value "C:\Program Files\Git\bin\bash.exe"
```

**note**: the location of `bash.exe` may be be different; salt to taste.

#### using arguments with an alias

this is...interesting, but how it seems to work. say you'd like to alias to a command **with arguments** in your profile. a wrapper function is a way to handle this:

```powershell
function fooBarWrapperFunction {
  foo -myArg bar
}
Set-Alias -Name foo-bar -Value fooBarWrapperFunction
```

useful when there's a steady reuse of long arguments for a particular shell call.

## tools

* linting: [psscriptanalyzer](https://github.com/PowerShell/PSScriptAnalyzer)
* testing: [pester](https://github.com/pester/Pester)

## use

**version check**: `$PSVersionTable.PSVersion` or `$host.Version`.

### common commands

|bash|powershell|
|----|----------|
|`clear`|`cls`|
|`ls`|`Get-ChildItem`|
|`whoami`|`$env:UserName`|
|`pwd`|`$pwd`|
|`cd your/path`|`Set-Location your\path`|
|`history`|`Get-History`|
|`sha`, `shasum`, etc.|`Get-FileHash`|
|`rm`|`Remove-Item`|
|`mv`|`Move-Item`|
|`man`|`Get-Help`|
|`cp`|`Copy-Item`|
|`less`, `more`|`Out-Host -paging`|
|`cat`|`Get-Content`|
|`mkdir`|`New-Item -Path . -Name "foo" -ItemType "directory"`|
|`touch`|`New-Item -Path . -Name "foo" -ItemType "file"`|
|`wget`, `curl`, etc.|`Invoke-WebRequest`|
|`which`|`(Get-Command foo -ErrorAction “SilentlyContinue”).Path`|

#### `grep`

"grepping" in powershell is a little more interesting. for example, to get results similar to `grep -Rn foo ./`, run:

```powershell
Get-ChildItem -Recurse *.* `
  | Select-String -Pattern 'foo' `
  | Select-Object -Unique Path
```
#### `source`

to import functions, etc. from another script, use the dot operator at the top of target script. for example, add `. path\to\source\script.ps1` to the top of the target script to import the script located at path\to\source\script.ps1.

sourcing `.bashrc` (and friends), declaring variables, and calling `unset` is typically how the environment is reloaded in bash. powershell has `refreshenv` to handle all that, which is an alias to `Update-SessionEnvironment`.

#### `sudo`

to elevate to an interactive admin shell, which will open in a new window (...), use `Start-Process powershell -Verb RunAs`.

to run a command with admin rights as part of a script, use the `-ArgumentList` parameter to force a confimation dialogue, then run the list of arguments in a new subshell (which will exit on completion):

```powershell
function install_psscriptanalyzer {
  Start-Process `
    -FilePath "powershell" `
    -Verb runAs `
    -ArgumentList "Install-Module -Name PSScriptAnalyzer -Force"
}
```

### current pipeline item

For reference, the `$_` is the same as `$PSItem`, which is a reference to the current item in a powershell pipeline. in addition to the below debugging example, you can access the current item in an iterator in a functional way like so: `1,2,3 | %{ write-host $_ }` or `1,2,3 | %{ write-host $PSItem }`.

### job management

to manage functions as background processes, the following snipped might be useful:

```powershell
$functions = {
  # notice that -Bar is the second argument.
  function wrapper {
    Param(
      [string[]] $Foo,
      [string[]] $Bar
    )

    Write-Host "${Bar}" # heads up, Write-Host might not be the best call here.
  }
}

function main {
  # notice that -Bar is the first argument.
  $job = Start-Job `
    -InitializationScript $functions `
    -ScriptBlock { wrapper -Bar "oow" -Foo "woo" }

  # wait on all jobs.
  Get-Job | Wait-Job

  # get output from specific job
  Receive-Job -Id $job.Id | Write-Host

  # remove all jobs in session.
  Get-Job | Remove-Job
}

main
```

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

### metaprogramming

powershell supports using anonymous functions as well as strings via `New-Item` and the `function:` drive for some light reflection:

```powershell
function dynamicFunction {
  $name = 'foo'

  # can't pass in parameter to anonymous function; outputs empty string.
  $parametrizedMessage = 'bar'
  $code = { param($pm) Write-Host -ForegroundColor Yellow $pm }
  $null = New-Item `
    -Force
    -Path function: `
      -Name "script:anonymous-$name" `
      -Value $code

  # can *set* parameter via string interpolation; outputs "baz."
  $interpolatedMessage = 'baz'
  $null = New-Item `
    -Force `
    -Path function: `
      -Name "script:interpolated-$name" `
      -Value "Write-Host -ForegroundColor Yellow '$interpolatedMessage`"
}
```

## windows subsytem for linux (wsl)

* install: `wsl --install`
* help: `wsl --help`
