# Overview
Occasionally Azure IaaS VMs (Microsoft.Compute/virtualMachines) may not start because there is something wrong with the operating system (OS) disk preventing it from booting up correctly.In such cases it is a common practice to recover the problem VM by performing the following steps.

 - Stop VM
- Take a snapshot of the OS Disk
- Create a Temporary Rescue VM
- Attach the OS Disk to the Rescue VM
- RDP to RescueVM
- From the Rescue VM and fix the disks manually.
- Detach the Data disk from Rescue VM
- Perform Disk Swap to point the OsDisk.Vhd.Uri to the recovered OS Disk Uri
- Finally Remove all the resources that were created for the Rescue VM

## Current version supports 
    Microsoft.Compute/virtualMachines (Non-Managed VM's)
    Supports Windows OS

## Scenarios

###  When would you use the script?
    If VM in Azure does not boot. Typically in this scenario VM screenshot from [boot diagnostics](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) 
    does not show login screen but a boot issue.

## Execution guidance
### CloudShell
- Start Azure Cloudshell for more info https://docs.microsoft.com/en-us/azure/cloud-shell/overview
- From the cloudshell prompt ==> Type c:
- Download the files run ==>  git clone https://github.com/sebdau/azure-support-scripts.git c:\azscripts\
- CD c:\azscripts\RecoverRMVm
- Phase 1 Run New-AzureRMRescueVM -ResourceGroup <ResourceGroup> -VmName <-VmName> -SubID <SUBID> 
- Step Fix the disk issue
- Phase 2 Run After running phase 1, it will give the parameters needed to run Restore-AzureRMOriginalVM.PS1


### Powershell
- The script must be executed in two phases
- Phase 1  From Powershell Execute => Get-Help New-AzureRMRescueVM #For details
            New-AzureRMRescueVM -ResourceGroup <ResourceGroup> -VmName <-VmName> -SubID <SUBID>
- Fix OS Disk issue            
            in addition to any other additional manual steps (To be provided by support)
- Phase 2 - From Powershell Execute =>  Get-Help Restore-AzureRMOriginalVM.PS1 #For details
            Restore-AzureRMOriginalVM.PS1  -ResourceGroup <ResourceGroup> -VmName <-VmName> -SubID <SUBID> -FixedOsDiskUri <FixedOsDiskUri-This will be provided in the console output plus Log after executing first step>
            After the OS Disk has been recovered, execute the Restore-AzureRMOriginalVM.PS1
## Version of Rescue VM
- For Windows Rescue VM is created with the latest version of 2016 image(GUI)
- For Linux   Rescue VM is created with the latest version of Canonical.UbuntuServer.16.04-LTS.latest

Follow the instructions and be patient (it may take between 15mins to an hour)

## Parameters or input
- ResourceGroup Name of the Problem VM
- VM name of the problem VM
- Subscription ID

## To get help on the scripts and its parameters run the following
- get-help .\New-AzureRMRescueVM.ps1
- get-help .\Restore-AzureRMOriginalVM.ps1

