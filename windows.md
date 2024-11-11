windows
=======

## on aws ec2

### rdp pki on aws

* [on aws, windows ami's do not support ed25510 keys.](https://github.com/aws/aws-cli/discussions/7074)
* [aws windows ami's support 4096-bit rsa, and private keys must be in .pem format.](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html#how-to-generate-your-own-key-and-import-it-to-aws)

### user data scripts

* can't use `Start-BitsTransfer` in an user data script, because `Start-BitsTransfer` doesn't wait for the network connection to come up.
    * have to use `Invoke-WebRequest` or `System.Net.Webclient` (faster) instead.
* for some reason, powershell user data scripts have to get wrapped in tags (bash scripts do not). example:

    ```powershell
    <powershell>
    Write-Host "why?"
    </powershell>
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
