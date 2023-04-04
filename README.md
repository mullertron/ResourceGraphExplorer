# ResourceGraphExplorer

## Get Created Date of a VM
~~~~~
resources
| where type == "microsoft.compute/virtualmachines"
| extend createdDate = properties.timeCreated
| project  name, resourceGroup, location, createdDate
~~~~~

## Get Created date of a VM and order by time created
~~~~~
resources
| where type == "microsoft.compute/virtualmachines"
| extend createdDate = properties.timeCreated
| project  name, resourceGroup, location, tostring(createdDate)
| sort by createdDate asc 
~~~~~

## Get Tag Information
~~~~~

resources
| where type == "microsoft.compute/virtualmachines"
| extend Environment = tags.Environment, Application = tags.["Application Name"], Cost_Code = tags.["Cost Code"], Owner = tags.Owner, cs_owner = tags.owner
| project name, resourceGroup, subscriptionId, Environment, Application, Cost_Code, Owner, cs_owner, tags

~~~~~

## Get Disk Info
~~~~~
Resources
| where type == "microsoft.compute/disks"
| project diskName=name, 
          diskSKU=sku.name, diskTier = properties.tier, 
          diskSizeGB=properties.diskSizeGB,
          diskMBpsReadWrite = properties.diskMBpsReadWrite, diskIOPSReadWrite = properties.diskIOPSReadWrite,
          diskState=properties.diskState, 
          encryptionType=properties.encryption.type
| order by diskName
~~~~~
