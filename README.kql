resources
| where type =~ "microsoft.web/sites"
// Filter by the specific App Service name provided by your user
| where name =~ "YOUR-APP-SERVICE-NAME"
| extend privateEndpointConnections = properties.privateEndpointConnections
| mv-expand privateEndpointConnections
| extend privateEndpointId = tostring(privateEndpointConnections.properties.privateEndpoint.id)
| where isnotempty(privateEndpointId)
| project AppServiceName = name, privateEndpointId
// Join to find the Network Interface ID associated with that Private Endpoint
| join kind=leftouter (
    resources
    | where type =~ "microsoft.network/privateendpoints"
    | mv-expand networkInterfaces = properties.networkInterfaces
    | project privateEndpointId = id, networkInterfaceId = tostring(networkInterfaces.id)
) on privateEndpointId
// Join to find the actual Private IP on that Network Interface
| join kind=leftouter (
    resources
    | where type =~ "microsoft.network/networkinterfaces"
    | mv-expand ipConfigurations = properties.ipConfigurations
    | project networkInterfaceId = id, PrivateIP = tostring(ipConfigurations.properties.privateIPAddress)
) on networkInterfaceId
| project AppServiceName, PrivateIP
