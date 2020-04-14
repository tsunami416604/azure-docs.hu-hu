---
title: Azure Private Link konfigurálása Azure Cosmos-fiókhoz
description: Megtudhatja, hogyan állíthatja be az Azure Private Linket egy Azure Cosmos-fiók eléréséhez egy virtuális hálózatban lévő privát IP-cím használatával.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: thweiss
ms.openlocfilehash: 4b49d2aa61587d0156755bdd5c47b3eeb90090a5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270689"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Private Link konfigurálása Azure Cosmos-fiókhoz

Az Azure Private Link használatával privát végponton keresztül csatlakozhat egy Azure Cosmos-fiókhoz. A privát végpont a virtuális hálózaton belüli alhálózatban lévő privát IP-címek készlete. Ezután korlátozhatja az Azure Cosmos-fiókhoz való hozzáférést a privát IP-címekre. Ha a Private Link korlátozott NSG-házirendekkel van kombinálva, ez segít csökkenteni az adatok kiszivárgásának kockázatát. A privát végpontokról az [Azure Private Link](../private-link/private-link-overview.md) cikkben olvashat bővebben.

A Private Link lehetővé teszi a felhasználók számára, hogy hozzáférjenek egy Azure Cosmos-fiókhoz a virtuális hálózaton belülről vagy bármely társviszonyt létesített virtuális hálózatról. A Privát kapcsolathoz leképezett erőforrások a helyszínen is elérhetők a VPN-en vagy az Azure ExpressRoute-on keresztüli privát társviszony-létesítésen keresztül. 

Csatlakozhat egy Azure Cosmos-fiókhoz, amely a Private Link szolgáltatással konfigurálva van az automatikus vagy manuális jóváhagyási módszer rel. További információ: A Privát hivatkozás [dokumentációjának Jóváhagyási munkafolyamat](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) szakasza. 

Ez a cikk a privát végpont létrehozásának lépéseit ismerteti. Feltételezi, hogy az automatikus jóváhagyási módszert használja.

> [!NOTE]
> A privát végpont támogatása jelenleg csak átjárókapcsolati módban érhető el. Közvetlen módban előnézeti funkcióként érhető el.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Privát végpont létrehozása az Azure Portal használatával

Az alábbi lépésekkel hozzon létre egy privát végpontot egy meglévő Azure Cosmos-fiókhoz az Azure Portal használatával:

1. A **Minden erőforrás** ablaktáblán válasszon egy Azure Cosmos-fiókot.

1. A beállítások listájából válassza a **Privát végpontkapcsolatok** elemet, majd a **Privát végpont**lehetőséget:

   ![Beállítások egy privát végpont létrehozásához az Azure Portalon](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. A Saját végpont létrehozása **- Alapjai** ablaktáblában adja meg vagy válassza ki a következő részleteket:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válasszon ki egy erőforráscsoportot.|
    | **Példány részletei** |  |
    | Név | Adja meg a saját végpont nevét. Ha ez a név foglalt, hozzon létre egy egyedit. |
    |Régió| Válassza ki azt a régiót, ahol a Privát hivatkozást telepíteni szeretné. Hozza létre a privát végpontot ugyanazon a helyen, ahol a virtuális hálózat létezik.|
    |||
1. Válassza a **Tovább: Erőforrás**lehetőséget.
1. A **Saját végpont - Erőforrás létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza **a Csatlakozás egy Azure-erőforráshoz a címtárban**lehetőséget. <br/><br/> Ezután kiválaszthatja az egyik erőforrást a Privát hivatkozás beállításához. Vagy kapcsolódhat valaki más erőforrásához egy önnel megosztott erőforrás-azonosító vagy alias használatával.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft.AzureCosmosDB/databaseAccounts lehetőséget.** |
    | Erőforrás |Válassza ki az Azure Cosmos-fiókot. |
    |Cél alerőforrás |Válassza ki az Azure Cosmos DB API-típus, amely le szeretné képezni. Ez alapértelmezés szerint csak egy választás az SQL, MongoDB és Cassandra API-k. A Gremlin és a Table API-k, sql is **választhat,** mert ezek az API-k interoperábilisaz SQL API-t. |
    |||

1. Válassza a **Tovább lehetőséget: Konfiguráció**.
1. A **Saját végpont létrehozása - Konfiguráció**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki a virtuális hálózatot. |
    | Alhálózat | Válassza ki az alhálózatot. |
    |**Privát DNS-integráció**||
    |Integrálás privát DNS-zónával |Válassza az **Igen** lehetőséget. <br><br/> A privát végponthoz való privát csatlakozáshoz DNS-rekordra van szükség. Azt javasoljuk, hogy integrálja a privát végpont egy privát DNS-zóna. Használhatja saját DNS-kiszolgálóit is, vagy dns-rekordokat hozhat létre a virtuális gépeken lévő gazdafájlok használatával. |
    |Privát DNS-zóna |Válassza **a privatelink.documents.azure.com**lehetőséget. <br><br/> A privát DNS-zóna automatikusan meghatározásra kerül. Az Azure Portal használatával nem módosíthatja.|
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **Véleményezés + létrehozás** lapon az Azure érvényesíti a konfigurációt.
1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

Ha jóváhagyta az Azure Cosmos-fiók privát hivatkozását, az Azure Portalon a tűzfal és a **virtuális hálózatok** ablaktáblán található Minden **hálózat** beállítás nem érhető el.

Az alábbi táblázat bemutatja a különböző Azure Cosmos-fiók API-típusok, támogatott al-erőforrások és a megfelelő privát zónanevek leképezése között. A Gremlin és table API-fiókok az SQL API-n keresztül is elérhetők, így ezekhez az API-khoz két bejegyzés tartoz.

|Azure Cosmos-fiók API-típusa  |Támogatott alerőforrások (vagy csoportazonosítók) |Privát zóna neve  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo között   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tábla    |    Tábla     |   privatelink.table.cosmos.azure.com    |
|Tábla     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>A privát IP-címek beolvasása

A privát végpont kiépítése után lekérdezheti az IP-címeket. Az IP-címek megtekintése az Azure Portalon:

1. Válassza az **Összes erőforrás** elemet.
1. Keresse meg a korábban létrehozott privát végpontot. Ebben az esetben ez **cdbPrivateEndpoint3**.
1. A DNS-beállítások és AZ IP-címek megtekintéséhez **kattintson** az Áttekintés fülre.

![Privát IP-címek az Azure Portalon](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Privát végpontonként több IP-cím jön létre:

* Az Azure Cosmos-fiók globális (régió-agnosztikus) végpontjának egyike
* Minden olyan régióhoz, ahol az Azure Cosmos-fiók telepítve van

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Saját végpont létrehozása az Azure PowerShell használatával

Futtassa a következő PowerShell-parancsfájlt egy "MyPrivateEndpoint" nevű privát végpont létrehozásához egy meglévő Azure Cosmos-fiókhoz. Cserélje le a változóértékeket a környezet adataira.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>A privát végpont integrálása magánDNS-zónával

A privát végpont létrehozása után integrálhatja azt egy privát DNS-zónával a következő PowerShell-parancsfájl használatával:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>A privát IP-címek beolvasása

A privát végpont kiépítése után lekérdezheti az IP-címeket és az FQDN-hozzárendelést a következő PowerShell-parancsfájl használatával:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Saját végpont létrehozása az Azure CLI használatával

Futtassa a következő Azure CLI-parancsfájlt egy "myPrivateEndpoint" nevű privát végpont létrehozásához egy meglévő Azure Cosmos-fiókhoz. Cserélje le a változóértékeket a környezet adataira.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>A privát végpont integrálása magánDNS-zónával

A privát végpont létrehozása után integrálhatja azt egy privát DNS-zónával a következő Azure CLI parancsfájl használatával:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Saját végpont létrehozása Erőforrás-kezelő sablon használatával

A Privát kapcsolat beállításához hozzon létre egy privát végpontot egy virtuális hálózati alhálózatban. Ezt egy Azure Resource Manager-sablon használatával érheti el.

A következő kód segítségével hozzon létre egy "PrivateEndpoint_template.json" nevű Erőforrás-kezelő sablont. Ez a sablon létrehoz egy privát végpontot egy meglévő Azure Cosmos SQL API-fiókhoz egy meglévő virtuális hálózatban.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**A sablon paraméterfájljának meghatározása**

Hozzon létre egy paraméterfájlt a sablonhoz, és nevezze el "PrivateEndpoint_parameters.json" névnek. Adja hozzá a következő kódot a paraméterfájlhoz:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**A sablon központi telepítése PowerShell-parancsfájl használatával**

Hozzon létre egy PowerShell-parancsfájlt a következő kód használatával. A parancsfájl futtatása előtt cserélje le az előfizetés-azonosítót, az erőforráscsoport nevét és más változóértékeket a környezet adataira.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

A PowerShell-parancsfájlban `GroupId` a változó csak egy értéket tartalmazhat. Ez az érték a fiók API-típusa. Az engedélyezett `Sql`értékek `MongoDB` `Cassandra`a `Gremlin`következők: , , , , és `Table`. Egyes Azure Cosmos-fióktípusok több API-n keresztül érhetők el. Például:

* A Gremlin API-fiók gremlin és SQL API-fiókokból is elérhető.
* A Table API-fiók tábla- és SQL API-fiókokból is elérhető.

Ezekhez a fiókokhoz létre kell hoznia egy privát végpontot minden API-típushoz. A megfelelő API-típus a `GroupId` tömbben van megadva.

A sablon sikeres üzembe helyezése után az alábbi képen láthatóhoz hasonló kimenet et láthat. Az `provisioningState` érték `Succeeded` akkor, ha a magánvégpontok megfelelően vannak beállítva.

![Az Erőforrás-kezelő sablon telepítési kimenete](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

A sablon telepítése után a privát IP-címek az alhálózaton belül lesznek fenntartva. Az Azure Cosmos-fiók tűzfalszabálya úgy van konfigurálva, hogy csak a privát végpontból származó kapcsolatokat fogadjon.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>A privát végpont integrálása magánDNS-zónával

A következő kód segítségével hozzon létre egy "PrivateZone_template.json" nevű Erőforrás-kezelő sablont. Ez a sablon létrehoz egy privát DNS-zónát egy meglévő Azure Cosmos SQL API-fiókhoz egy meglévő virtuális hálózatban.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

A következő kód segítségével hozzon létre egy "PrivateZoneRecords_template.json" nevű Erőforrás-kezelő sablont.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**A sablon paraméterfájljának meghatározása**

Hozza létre a következő két paraméterfájlt a sablonhoz. Hozza létre a "PrivateZone_parameters.json" kifejezést. a következő kóddal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Hozza létre a "PrivateZoneRecords_parameters.json" kifejezést. a következő kóddal:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**A sablon központi telepítése PowerShell-parancsfájl használatával**

Hozzon létre egy PowerShell-parancsfájlt a következő kód használatával. A parancsfájl futtatása előtt cserélje le az előfizetés-azonosítót, az erőforráscsoport nevét és más változóértékeket a környezet adataira.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Egyéni DNS konfigurálása

Használjon egy privát DNS-zónát az alhálózaton belül, ahol létrehozta a privát végpontot. Konfigurálja úgy a végpontokat, hogy minden magánhálózati IP-cím DNS-bejegyzéshez van rendelve. (Lásd `fqdns` a tulajdonságot a korábban bemutatott válaszban.)

Amikor létrehozza a privát végpontot, integrálhatja azt egy privát DNS-zónával az Azure-ban. Ha úgy dönt, hogy ehelyett egyéni DNS-zónát használ, konfigurálnia kell azt, hogy dns-rekordokat adjon hozzá a magánhálózati végpontszámára fenntartott összes magánhálózati IP-címhez.

## <a name="private-link-combined-with-firewall-rules"></a>Privát kapcsolat tűzfalszabályokkal kombinálva

A következő helyzetek és eredmények akkor lehetségesek, ha a Privát kapcsolat szolgáltatást tűzfalszabályokkal kombinálja:

* Ha nem konfigurálja a tűzfalszabályokat, majd alapértelmezés szerint az összes forgalom hozzáférhet egy Azure Cosmos-fiókhoz.

* Ha konfigurálja a nyilvános forgalmat vagy egy szolgáltatásvégpontot, és privát végpontokat hoz létre, akkor a megfelelő típusú tűzfalszabály különböző típusú bejövő forgalmat engedélyez.

* Ha nem konfigurál semmilyen nyilvános forgalmat vagy szolgáltatásvégpontot, és privát végpontokat hoz létre, majd az Azure Cosmos-fiók csak a privát végpontokon keresztül érhető el. Ha nem konfigurálja a nyilvános forgalmat vagy a szolgáltatás végpontját, miután az összes jóváhagyott magánvégpontot elutasították vagy törölték, a fiók a teljes hálózat számára megnyílik.

## <a name="blocking-public-network-access-during-account-creation"></a>Nyilvános hálózati hozzáférés letiltása a fiók létrehozása során

Az előző szakaszban leírtak szerint, és ha nincs beállítva konkrét tűzfalszabályok, egy privát végpont hozzáadása az Azure Cosmos-fiók csak magánvégpontokon keresztül érhető el. Ez azt jelenti, hogy az Azure Cosmos-fiók a nyilvános forgalomból érhető el a létrehozása után, és mielőtt egy privát végpont hozzáadása. Annak érdekében, hogy a nyilvános hálózati hozzáférés már a magánhálózati `publicNetworkAccess` végpontok létrehozása előtt is le legyen tiltva, beállíthatja a jelzőt a fiók létrehozása `Disabled` során. Tekintse meg [ezt az Azure Resource Manager sablont](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) egy példa, amely bemutatja, hogyan használja ezt a jelzőt.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Saját végpont frissítése régió hozzáadásakor vagy eltávolításakor

Régiók hozzáadása vagy eltávolítása egy Azure Cosmos-fiókhoz, dns-bejegyzések hozzáadását vagy eltávolítását kell igényelnie az adott fiókhoz. A régiók hozzáadása vagy eltávolítása után frissítheti az alhálózat privát DNS-zónáját, hogy tükrözze a hozzáadott vagy eltávolított DNS-bejegyzéseket és a hozzájuk tartozó privát IP-címeket.

Tegyük fel például, hogy egy Azure Cosmos-fiókot három régióban telepít: "USA nyugati régiója", "USA középső régiója" és "Nyugat-Európa". Amikor privát végpontot hoz létre a fiókjához, négy privát IP-hely van lefoglalva az alhálózatban. A három régióhoz egy-egy IP-cím tartozik, és egy IP a globális/régió-agnosztikus végponthoz.

Később előfordulhat, hogy egy új régiót (például "USA keleti régiója") ad hozzá az Azure Cosmos-fiókhoz. Az új régió hozzáadása után hozzá kell adnia egy megfelelő DNS-rekordot a saját DNS-zónához vagy az egyéni DNS-hez.

A terület eltávolításakor ugyanazokat a lépéseket használhatja. A régió eltávolítása után el kell távolítania a megfelelő DNS-rekordot a saját DNS-zónájából vagy az egyéni DNS-ből.

## <a name="current-limitations"></a>Aktuális korlátozások

A következő korlátozások érvényesek, ha privát kapcsolatot használ egy Azure Cosmos-fiókkal:

* Ha egy Azure Cosmos-fiókkal használja a Privát kapcsolatot egy közvetlen módú kapcsolat használatával, csak a TCP protokollt használhatja. A HTTP protokoll még nem támogatott.

* A privát végpont támogatása jelenleg csak átjárókapcsolati módban érhető el. Közvetlen módban előnézeti funkcióként érhető el.

* Ha az Azure Cosmos DB API-ját használja a MongoDB-fiókokhoz, a rendszer csak a 3.6-os kiszolgálón lévő `*.mongo.cosmos.azure.com`fiókok (azaz a végpontot formátumban használó fiókok) támogatja a privát végpontot. A Private Link nem támogatott a 3.2-es kiszolgálón lévő fiókok `*.documents.azure.com`(azaz a végpontot formátumban használó fiókok) esetében. A Privát hivatkozás használatához telepítse át a régi fiókokat az új verzióra.

* Ha az Azure Cosmos DB API-t használja a Privát hivatkozással rendelkező MongoDB-fiókokhoz, nem használhatja az olyan eszközöket, mint a Robo 3T, a Studio 3T és a Mongúz. A végpont csak akkor rendelkezhet `appName=<account name>` Private Link támogatással, ha a paraméter meg van adva. Például: `replicaSet=globaldb&appName=mydbaccountname`. Mivel ezek az eszközök nem adják át az alkalmazás nevét a kapcsolati karakterláncban a szolgáltatásnak, nem használhatja a Privát hivatkozást. De továbbra is elérheti ezeket a fiókokat az SDK-illesztőprogramok használatával a 3.6-os verzióval.

* Nem helyezhet át és nem törölhet virtuális hálózatot, ha az privát kapcsolatot tartalmaz.

* Nem törölheti az Azure Cosmos-fiókot, ha egy privát végponthoz van csatolva.

* Nem feladatátvételegy Azure Cosmos-fiók egy olyan régióban, amely nincs leképezve a fiókhoz csatolt összes privát végpontok.

* A hálózati rendszergazdának legalább a "*/PrivateEndpointConnectionsApproval" engedélyt kell adni az Azure Cosmos-fiók hatókörén az automatikusan jóváhagyott magánvégpontok létrehozásához.

### <a name="limitations-to-private-dns-zone-integration"></a>A privát DNS-zóna integrációjának korlátai

A magánDNS-zónában lévő DNS-rekordok nem törlődnek automatikusan, ha töröl egy privát végpontot, vagy eltávolít egy régiót az Azure Cosmos-fiókból. A DNS-rekordokat manuálisan kell eltávolítania a következő előtt:

* A magánDNS-zónához kapcsolódó új privát végpont hozzáadása.
* Új régió hozzáadása minden olyan adatbázisfiókhoz, amelyhez privát végpontok vannak csatolva ehhez a privát DNS-zónához.

Ha nem törli a DNS-rekordokat, váratlan adatsík-problémák léphetnek fel. Ezek a problémák közé tartozik a privát végponteltávolítása vagy a régió eltávolítása után hozzáadott régiók adatkimaradása.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Cosmos DB biztonsági funkcióiról, olvassa el az alábbi cikkeket:

* Az Azure Cosmos DB tűzfalának konfigurálásához olvassa el a Tűzfal támogatása című [témakört.](firewall-support.md)

* Ha tudni szeretné, hogyan konfigurálhat virtuális hálózati szolgáltatásvégpontot az Azure Cosmos-fiókhoz, olvassa el a [Hozzáférés konfigurálása virtuális hálózatokból](how-to-configure-vnet-service-endpoint.md)című témakört.

* Ha többet szeretne megtudni a Privát hivatkozásról, tekintse meg az [Azure Private Link](../private-link/private-link-overview.md) dokumentációját.
