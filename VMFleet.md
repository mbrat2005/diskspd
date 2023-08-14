## What is VMFleet 

VMFleet is a storage load generator to stress test hyper-converged Storage Spaces Direct (S2D) environments. The tool is specifically designed for performance analysis work and allows an analyst to inject near real-time changes into the load. Specifically, VMFleet works by utilizing a set of commands (previously scripts) to run DiskSpd in a distributed set of virtual machines (the 'fleet') and send/receive I/O requests. VMFleet is also useful for testing and validating performance on an Azure Stack hyper-converged cluster.

## Quick startup guide
These instructions assume you already have a Windows Server 2019 Server Core VHD for the virtual machines.

First, install the PowerShell module from the PowerShell Gallery:
* `Install-Module -Name "VMFleet"`
* `Import-Module VMFleet`

Next, create a "Collect" volume:
* `New-Volume -StoragePoolFriendlyName S2D* -FriendlyName "Collect" -FileSystem CSVFS_ReFS -Size 200GB`

Create one cluster shared volume per node. To determine the appropriate CSV size you may use the Get-FleetVolumeEstimate command:
* `$size = Get-FleetVolumeEstimate`

Select the CSV size that fits your needs (depends on your resiliency type) and create the new volumes. Please replace the "New-Volume..." command with your desired resiliency volume creation command:
* `Get-ClusterNode | ForEach-Object { New-Volume -FriendlyName $_.name -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames Mirror -StorageTierSizes $size[0].MirrorSize }`

Set up the VMFleet directory structure within the Collect CSV. This creates four folders: Control, Flag, Result and Tools. DiskSpd.exe will come preinstalled in the Tools folder:
* `Install-Fleet`

Create the "fleet" of virtual machines:
* `New-Fleet -BaseVhd $VhdPath -AdminPass $AdminPass -ConnectUser $NodeUser -ConnectPass $NodePassword`
    * `$AdminPass` is the administrator password for the Server Core image. This is the password you configured for the virtual machine earlier when creating the VHD.
    * `$NodeUser` is a domain account with access to the cluster.
    * `$NodePassword` is the password for the above domain account.

Finally, you may wish to install the NuGet Package if you have not previously done so and if you plan on using the Measure-FleetCoreWorkload command. Please note that in doing so, you will temporarily set the PSGallery as a trusted repository source, relaxing the security boundary:

``` 
$repo = Get-PSRepository -Name PSGallery
if ($null -eq $repo) { Write-Host "The PSGallery is not configured on this system, please address this before continuing" }
else {
    if ($repo.InstallationPolicy -ne 'Trusted') {
        Write-Host "Setting the PSGallery repository to Trusted, original InstallationPolicy: $($repo.InstallationPolicy)"
        Set-PSRepository -Name PSGallery -InstallationPolicy Trusted
    } 

    ### Installing the pre-requisite modules

    Install-PackageProvider NuGet -Force
    Install-Module -Name PrivateCloud.DiagnosticInfo -Force -AllowClobber -SkipPublisherCheck
    Install-Module -Name MSFT.Network.Diag -Force

    if ($repo.InstallationPolicy -ne 'Trusted') {
        Write-Host "Resetting the PSGallery repository to $($repo.InstallationPolicy)"
        Set-PSRepository -Name PSGallery -InstallationPolicy $repo.InstallationPolicy
    } 
}  
```

That's it! Feel free to explore the VMFleet commands below and begin your desired test.

## VMFleet module commands
| Command` | Description |
| --- | --- |
| `Clear-FleetPause` | Clear pause flag so that the VMs can run a sweep based on the most recent *.ps1 script.  |
| `Clear-FleetRunState` | Remove all VM run response flag files. |
| `Convert-FleetXmlToString` | Convert the XML profile you created via "Get-FleetProfileXml" into a string that is displayed on the command line. Simply pipe the profile into Convert-FleetXmlToString. |
| `Get-FleetComputeTemplate` | Return the processor count and memory size of an Azure VM template. Supported VM templates include: A1v2, A2mv2, A2v2, A4mv2, A4v2, A8mv2, A8v2 |
| `Get-FleetDataDiskEstimate` | Calculate the size for a data disk per VM in bytes, based on your cache capacity and the provided integer percentage. If cache capacity does not exist, it uses the capacity drive size. The two types of possible percentages are -CachePercent and -CapacityPercent. |
| `Get-FleetDisk` | Inspect and validate the presence of data disks on the virtual machines and their characteristics such as their size. |
| `Get-FleetPath` | Output the path to the indicated file. |
| `Get-FleetPause` | Return a Boolean value that indicates whether the fleet is paused.  |
| `Get-FleetPolynomialFit` | Given some input data, generate a least squares fitted polynomial equation.  |
| `Get-FleetPowerScheme` | Query and get the current power scheme setting of host. |
| `Get-FleetProfileXml` | Allows the user to modify a predefined XML profile via VMFleet. You may specify two possible profile names: General, and Peak. You can provide most of the normal parameters that you would normally use in DiskSpd. For example, write ratio, block size, thread count, etc. The output profile is returned as an XML document which can then be passed into other commands. |
| `Get-FleetResultLog` | Return the contents of the log file on the terminal and allow user to map the file name label (RunLabel) to the characteristics of the test run. |
| `Get-FleetVersion` | Check the current DiskSpd and VMFleet version number. |
| `Get-FleetVM` | Retrieve the aggregate VM health status. |
| `Get-FleetVolumeEstimate` | Calculate the CSV size for the user before running Measure-FleetCoreWorkload. This avoids running into an insufficient CSV size or an extremely large CSV size. Calculation is based on reserve size, raw pool size, storage efficiency, node count and existing data size. |
| `Install-Fleet` | A one-time function to install the VMFleet tools and create the control directory structure. |
| `Measure-FleetCoreWorkload` | Runs VMFleet across four pre-defined workload profiles: General, Peak (IOPS maxed out), VDI and SQL. |
| `Move-Fleet` | Rotate CSVs and virtual machines away from the original owner node. |
| `New-Fleet` | Create the per-node internal VM switches and deploys the VMFleet virtual machines from a pre-created VHD master image. |
| `Remove-Fleet` | Remove all the VMs currently on the Hyper-V hosts. |
| `Repair-Fleet` | Auto-restart the VMs that are not responding to control loop actions as seen in Get-FleetVM. |
| `Set-Fleet` | Adjust the number of virtual processors per VM and memory size/type per VM. |
| `Set-FleetPause` | Set the pause flag so that the VMs are not running a sweep. |
| `Set-FleetPowerScheme` | Set the power scheme setting of host. |
| `Set-FleetProfile` | Change certain flags (throughput, warmup, duration, cooldown) in a DiskSpd XML profile, without having to open the file and manually editing. |
| `Set-FleetQoS` | A wrapper for Set-VMHardDiskDrive, which takes a predefined Storage QoS Policy and applies it to all VMs within the hyperconverged cluster. |
| `Show-Fleet` | Check the operational state of VMs hosted throughout the cluster. |
| `Show-FleetCPUSweep` | Produce report containing polynomial fit based on the output file of the sweep. |
| `Show-FleetPause` | Check how many pause acknowledgements have been received from the host node. Enumerates non-paused VMs. |
| `Show-FleetVersion` | Check the current DiskSpd and VMFleet version number. |
| `Start-Fleet` | Launch all the VMs currently on the host nodes in OFF state. Specifically, this shifts the VM state from offline to online. |
| `Start-FleetReadCacheWarmup` | Run a read cache warmup. This is meant for HDD capacity devices with read cache enabled. |
| `Start-FleetResultRun` | Run VMFleet using an XML template profile in memory. |
| `Start-FleetRun` | Run VMFleet by providing an XML profile, an XML profile string, or the run.ps1 script. |
| `Start-FleetSweep` | Run VMFleet Sweep that uses DiskSpd parameters. All matrix combinations of parameter values are executed if providing multiple values for a single flag (separated by commas).  |
| `Start-FleetWriteWarmup` | Run a write I/O warmup. This warmup is a way to guarantee expansion of storage allocations for the working set. For instance, mitigate an initial performance bump that may occur as storage spaces tracks whether a parity stripe has or has not been written for a MAP resiliency type. |
| `Stop-Fleet` | Shut down all the VMs currently in an online state. |
| `Test-FleetResultRun` | This is a utility that provides the user a way to ask if a measurement column (KeyColumn @{}) was already captured in the result-log.tsv file from the Measure-FleetCoreWorkload command. It essentially counts the number of iterations that match KeyColumn and asks if there are any results that match. If so, it returns true. Otherwise, false. |
| `Use-FleetPolynomialFit` | Given coefficients of A and independent value of X, output the corresponding value of Y.  |
| `Watch-FleetCluster` | Display text-console performance monitor across a cluster. It displays the CSVFS IOPS, bandwidth and latency counters and aggregates them per-node and whole-cluster. |
| `Watch-FleetCPU` | Display text-console performance monitor for CPU utilization distributions. Each asterisk represents a single CPU. |

Table 1. VMFleet module commands
