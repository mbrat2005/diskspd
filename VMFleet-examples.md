## VMFleet example commands

| Test description | Sample command |
| --- | ---|
| Produce a report of a generalized polynomial fit based on a TSV sweep output file | `Show-FleetCpuSweep -ResultFile ".\result.tsv"` |
| Load and run a previously created XML profile using VMFleet | `Start-FleetRun -ProfilePath ".\profile.xml"` |
| Given input data, generate a least square fitted polynomial equation | `Get-FleetPolynomialFit -Order 2 -X 1,2,3,4 -Y 0,1,4,9` |
| Given coefficients and a value of X, output the corresponding value of Y | `Use-FleetPolynomialFit -A 1,-2,1 -X 4` |
| Modify a predefined XML profile in VMFleet and generate a new profile (works on General and Peak workload) | `Get-FleetProfileXml -Name "General" -WriteRatio 0 -BlockSize 8KB -Sequential` |
| Convert an XML profile created via "Get-FleetProfileXml" into a string output | `Get-FleetProfileXml -Name "General" -WriteRatio 0 -BlockSize 8KB \| Convert-FleetXmlToString` |
| Modify a predefined XML profile | `$profile = Get-FleetProfileXml -Name "General" -Warmup 30 -Duration 60 -Cooldown 30` |
| Start a run with a previously created XML profile (see above) | `Start-FleetResultRun -ProfileXml $profile` |
| From the group formed by 25% of the total VMs, rotate 30% of the VMs such that they are misaligned from the owner node | `Move-Fleet -Cluster "Cluster01" -DistributeVMPercent 30 -VMPercent 25` |
| Calculate the size for a virtual machine's data disk in bytes (calculation based on cache size and/or capacity size). When CachePercent and CapacityPercent is used together, it outputs the smaller of the two | `Get-FleetDataDiskEstimate -CachePercent 200 -CapacityPercent 30 -Verbose -VMs 40` |
| Run four pre-defined VMFleet workloads (General, Peak, VDI, SQL) | `Measure-FleetCoreWorkload` |
| Estimate a cluster shared volume size (CSV) before creating a CSV for VMFleet. Depending on your environment configuration (e.g. node count), it will output a size you can use to create your CSV for your desired resiliency type | `Get-FleetVolumeEstimate` |
| Verify whether a measurement column (KeyColumn @{}) was already captured in the result-log.tsv file from Measure-FleetCoreWorkload | `Test-FleetResultRun -ResultLogFile ".\result-log.tsv" -KeyColumn @{ Workload = 'General4KWriteRatio0' }` |
| Return contents of the result log file, which maps the RunLabel to the characteristics of the test run from Measure-FleetCoreWorkload (e.g. workload type) | `Get-FleetResultLog ".\result-log.tsv" -KeyColumn @{ FleetVMPercent = 25 }` |

## Move-Fleet user scenarios
| Scenarios | Command |
| --- | --- |
| Realign both VMs and CSVs | `Move-Fleet` |
| Rotate both VMs and CSVs | `Move-Fleet –DistributeVMPercent <value> -ShiftCSV` |
| Rotate VM, but realign CSV | `Move-Fleet –DistributeVMPercent <value>` |
| Rotate VM and keep CSV constant | `Move-Fleet –DistributeVMPercent <value> -KeepCSV` |
| Rotate CSV, but realign VMs | `Move-Fleet -ShiftCSV` |
| Rotate CSV and keep VMs constant | `Move-Fleet –ShiftCSV –KeepVM` |
