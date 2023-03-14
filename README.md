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
| where type == "microsoft.compute/virtualmachines" and subscriptionId == "d9ad2526-fba1-4657-a959-184ba3b1f5b0"
| extend Environment = tags.Environment, Application = tags.["Application Name"], Cost_Code = tags.["Cost Code"], Owner = tags.Owner, cs_owner = tags.owner
| project name, resourceGroup, subscriptionId, Environment, Application, Cost_Code, Owner, cs_owner, tags

~~~~~
