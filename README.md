

If (-NOT ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole(`
    [Security.Principal.WindowsBuiltInRole] "Administrator"))
{
    
    Start-Process powershell "-ExecutionPolicy Bypass -File `"$PSCommandPath`"" -Verb RunAs
    Exit
}

$guid = [guid]::NewGuid().ToString()
Write-Output "New Device ID (GUID): $guid"


Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\SQMClient" -Name "MachineId" -Value "{$guid}"


Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Cryptography" -Name "MachineGuid" -Value $guid


$random = Get-Random -Minimum 1000 -Maximum 9999
$newName = "Node-PC-" + $random
Rename-Computer -NewName $newName -Force

Write-Output "✅ Device ID changed."
Write-Output "✅ Device Name changed to: $newName"
Write-Output "🔁 Rebooting in 5 seconds..."
Start-Sleep -Seconds 5
Restart-Computer
