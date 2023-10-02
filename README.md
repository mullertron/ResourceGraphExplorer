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

## Get NSG Info

~~~~~
resources
| where type =~ "microsoft.network/networksecuritygroups"
| join kind=leftouter (resourcecontainers | where type =='microsoft.resources/subscriptions' | project SubscriptionName=name, subscriptionId) on subscriptionId
|mv-expand rules=properties.securityRules
|extend direction=tostring(rules.properties.direction)
|extend priority=toint(rules.properties.priority)
|extend rule_name = rules.name
|extend nsg_name = name
|extend description=rules.properties.description
|extend destination_prefix=iif(rules.properties.destinationAddressPrefixes=='[]', rules.properties.destinationAddressPrefix, strcat_array(rules.properties.destinationAddressPrefixes, ","))
|extend destination_asgs=iif(isempty(rules.properties.destinationApplicationSecurityGroups), '', strcat_array(parse_json(rules.properties.destinationApplicationSecurityGroups), ","))
|extend destination=iif(isempty(destination_asgs), destination_prefix, destination_asgs)
|extend destination=iif(destination=='*', "Any", destination)
|extend destination_port=iif(isempty(rules.properties.destinationPortRange), strcat_array(rules.properties.destinationPortRanges,","), rules.properties.destinationPortRange)
|extend source_prefix=iif(rules.properties.sourceAddressPrefixes=='[]', rules.properties.sourceAddressPrefix, strcat_array(rules.properties.sourceAddressPrefixes, ","))
|extend source_asgs=iif(isempty(rules.properties.sourceApplicationSecurityGroups), "", strcat_array(parse_json(rules.properties.sourceApplicationSecurityGroups), ","))
|extend source=iif(isempty(source_asgs), source_prefix, tostring(source_asgs))
|extend source=iif(source=='*', 'Any', source)
|extend source_port=iif(isempty(rules.properties.sourcePortRange), strcat_array(rules.properties.sourcePortRanges,","), rules.properties.sourcePortRange)
|extend action=rules.properties.access
|extend subnets = strcat_array(properties.subnets, ",")
|project SubscriptionName, resourceGroup, nsg_name, rule_name, subnets, direction, priority, action, source, source_port, destination, destination_port, description, subscriptionId, id
|sort by SubscriptionName, resourceGroup asc, nsg_name, direction asc, priority asc
~~~~~
