# ResourceGraphExplorer

## Get Created Date of a VM
~~~~~
resources
| where type == "microsoft.compute/virtualmachines"
| extend createdDate = properties.timeCreated
| project  name, resourceGroup, location, createdDate
~~~~~
