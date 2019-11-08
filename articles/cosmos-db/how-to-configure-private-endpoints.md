---
title: Azure Private-hivatkozás konfigurálása Azure Cosmos-fiókhoz
description: Ismerje meg, hogyan állíthatja be az Azure-beli privát hivatkozást egy virtuális hálózat privát IP-címének használatával való hozzáféréshez egy Azure Cosmos-fiókhoz.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 6602a47a9d1d34b04f37c6b65a3c3f84cd60c845
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796089"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Azure Private-hivatkozás konfigurálása Azure Cosmos-fiókhoz (előzetes verzió)

Az Azure Private link használatával privát végponton keresztül csatlakozhat egy Azure Cosmos-fiókhoz. A magánhálózati végpont a virtuális hálózaton belüli alhálózat magánhálózati IP-címeinek halmaza. A privát hivatkozás használatával korlátozhatja a hozzáférést egy adott Azure Cosmos-fiókhoz magánhálózati IP-címeken keresztül. Korlátozott NSG-szabályzatok együttes használata esetén a privát kapcsolat segít csökkenteni az adatkiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](../private-link/private-link-overview.md) ismertető cikket.

Emellett a privát hivatkozás lehetővé teszi, hogy egy Azure Cosmos-fiók elérhető legyen a virtuális hálózatról vagy bármely társ virtuális hálózatból. A privát kapcsolathoz rendelt erőforrások a helyi hálózaton keresztül, VPN-en vagy ExpressRoute keresztül is elérhetők. 

Az "automatikus" vagy a "manuális" jóváhagyási módszerek használatával kapcsolódhat egy privát hivatkozással konfigurált Azure Cosmos-fiókhoz. További információ: a privát hivatkozás dokumentációjának [jóváhagyási munkafolyamat](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) szakasza. Ez a cikk bemutatja, hogyan hozhat létre egy privát hivatkozást, feltételezve, hogy automatikus jóváhagyási módszert használ.

## <a name="create-a-private-link-using-the-azure-portal"></a>Privát hivatkozás létrehozása a Azure Portal használatával

A következő lépésekkel hozhat létre privát hivatkozást egy meglévő Azure Cosmos-fiókhoz Azure Portal használatával:

1. A **minden erőforrás** ablaktáblán keresse meg a védeni kívánt Azure Cosmos db fiókot.

1. Válassza a **privát végponti kapcsolatok** elemet a beállítások menüben, és válassza a **privát végpont** lehetőséget:

   ![Privát végpont létrehozása Azure Portal használatával](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. A **privát végpont létrehozása (előzetes verzió) – alapok**, ablaktábla adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válasszon ki egy erőforráscsoportot.|
    | **Példány részletei** |  |
    | Név | Adja meg a privát végpont nevét. Ha ezt a nevet hozza, hozzon létre egy egyedit. |
    |Régió| Válassza ki azt a régiót, ahová a privát hivatkozást telepíteni szeretné. A magánhálózati végpontot ugyanazon a helyen kell létrehozni, ahol a virtuális hálózat található.|
    |||
1. Válassza a **Tovább: erőforrás**elemet.
1. A **privát végpont létrehozása – erőforrás**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |Kapcsolati módszer  | Válassza a kapcsolódás egy Azure-erőforráshoz a címtárban lehetőséget. <br/><br/> Ezzel a beállítással kiválaszthatja az egyik erőforrást egy privát hivatkozás beállításához, vagy kapcsolódhat valaki másnak az erőforráshoz egy olyan erőforrás-AZONOSÍTÓval vagy aliassal, amelyet Önnel osztottak meg.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. AzureCosmosDB/databaseAccounts**lehetőséget. |
    | Erőforrás |Azure Cosmos-fiók kiválasztása |
    |Cél alerőforrása |Válassza ki a leképezni kívánt Cosmos DB API-típust. Ez az alapértelmezett érték az SQL, a MongoDB és a Cassandra API-k esetében csak egyetlen választás. A Gremlin és a Table API-k esetében is választhatja az *SQL* -t, mivel ezek az API-k az SQL API-val együttműködnek. |
    |||

1. Válassza a **Tovább: konfigurálás**lehetőséget.
1. A **privát végpont létrehozása (előzetes verzió) – konfiguráció**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    |**Hálózat**| |
    | Virtuális hálózat| Válassza ki a virtuális hálózatot. |
    | Alhálózat | Válassza ki az alhálózatot. |
    |**saját DNS integráció**||
    |Integrálás saját DNS-zónával |Válassza az **Igen** lehetőséget. <br><br/> A magánhálózati végponthoz való kapcsolódáshoz DNS-rekordra van szükség. Javasoljuk, hogy a privát végpontot egy privát DNS-zónával integrálja. Saját DNS-kiszolgálókat is használhat, vagy létrehozhat DNS-rekordokat a virtuális gépeken található gazdagépek használatával. |
    |saját DNS zóna |*Privatelink.Documents.Azure.com* kiválasztása <br><br/> A saját DNS zóna automatikusan meg van határozva, és jelenleg nem módosítható a Azure Portal használatával.|
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.
1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

Ha jóváhagyta az Azure Cosmos-fiókhoz tartozó privát hivatkozásokat, akkor a **tűzfal és a virtuális hálózatok** panel **minden hálózata** lehetőség Azure Portal szürkén jelenik meg.

Az alábbi táblázat a különböző Azure Cosmos-fiók API-típusai, a támogatott alerőforrások és a hozzájuk tartozó saját zónák neve közötti leképezést mutatja. A Gremlin és a Table API fiók az SQL API-n keresztül érhető el, így ezek az API-k 2 bejegyzést is kapnak:

|Azure Cosmos-fiók API-típusa  |Támogatott alerőforrások (vagy groupIds) |Saját zóna neve  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tábla    |    Tábla     |   privatelink.table.cosmos.azure.com    |
|Tábla     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>A magánhálózati IP-címek beolvasása

A magánhálózati végpont kiépítés után lekérdezheti az IP-címeket. A Azure Portal IP-címeinek megtekintése. Válassza az **összes erőforrás**lehetőséget, keresse meg a korábban létrehozott privát végpontot ebben az esetben: "dbPrivateEndpoint3", és válassza az Áttekintés lapot a DNS-beállítások és IP-címek megtekintéséhez.

![Magánhálózati IP-címek a Azure Portalban](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Több IP-cím jön létre egy privát végponton:

* Az egyik az Azure Cosmos-fiók globális (régió-agnosztikus) végpontja.
* Egy minden olyan régió esetében, ahol az Azure Cosmos-fiók telepítve van.

## <a name="create-a-private-link-using-azure-powershell"></a>Privát hivatkozás létrehozása Azure PowerShell használatával

Futtassa a következő PowerSehll-szkriptet egy "MyPrivateEndpoint" nevű privát hivatkozás létrehozásához egy meglévő Azure Cosmos-fiókhoz. Ügyeljen arra, hogy a változó értékeit a környezetének megfelelő adatokkal cserélje le.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Privát végpont integrálása privát DNS-zónával

A privát végpont létrehozása után a következő PowerSehll parancsfájl használatával integrálhatja azt egy privát DNS-zónával:

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

### <a name="fetch-the-private-ip-addresses"></a>A magánhálózati IP-címek beolvasása

A magánhálózati végpont kiosztása után a következő PowerShell-parancsfájl használatával kérdezheti le az IP-címeket és a teljes tartománynevek megfeleltetését:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Privát hivatkozás létrehozása Resource Manager-sablon használatával

A magánhálózati kapcsolatot úgy is beállíthatja, hogy létrehoz egy magánhálózati végpontot egy virtuális hálózati alhálózatban. Ez egy Azure Resource Manager sablon használatával érhető el. Hozzon létre egy "PrivateEndpoint_template. JSON" nevű Resource Manager-sablont a következő kóddal. Ez a sablon létrehoz egy magánhálózati végpontot meglévő Azure Cosmos SQL API-fiókhoz egy meglévő virtuális hálózatban:

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

### <a name="define-the-template-parameters-file"></a>A sablon paramétereit tartalmazó fájl megadása

Hozzon létre egy paramétereket tartalmazó fájlt a sablonhoz, és nevezze el "PrivateEndpoint_parameters. JSON" néven. Adja hozzá a következő kódot a parameters (paraméterek) fájlhoz:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>A sablon üzembe helyezése PowerShell-parancsfájl használatával

Ezután hozzon létre egy PowerShell-parancsfájlt a következő kóddal. A szkript futtatása előtt mindenképp cserélje le az előfizetés-azonosítót, az erőforráscsoport nevét és az egyéb változó értékeket a környezetére vonatkozó részletekre:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

A PowerShell-parancsfájlban a "GroupId" változó csak egy értéket tartalmazhat, amely a fiók API-típusa. Az engedélyezett értékek: SQL, MongoDB, Cassandra, Gremlin és Table. Néhány Azure Cosmos-fióktípus több API-n keresztül érhető el. Például:

* A Gremlin API-fiókok a Gremlin és az SQL API-fiókokból is elérhetők.
* Table API fiók a tábla-és SQL API-fiókokból is elérhető.

Ilyen fiókok esetében minden egyes API-típushoz létre kell hoznia egy privát végpontot, a "GroupId" tömbben megadott API-típussal.

A sablon sikeres üzembe helyezését követően az alábbi képen láthatóhoz hasonló kimenet jelenik meg. A provisioningState értéke "sikeres", ha a magánhálózati végpontok megfelelően vannak beállítva.

![Resource Manager-sablon telepítési kimenete](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

A sablon üzembe helyezése után a magánhálózati IP-címek az alhálózaton belül vannak lefoglalva. Az Azure Cosmos-fiók tűzfalszabály úgy van konfigurálva, hogy csak a magánhálózati végpontról fogadjon kapcsolatokat.

## <a name="configure-custom-dns"></a>Egyéni DNS konfigurálása

A privát hivatkozás előnézete alatt egy privát DNS-t kell használnia azon az alhálózaton belül, ahol a magánhálózati végpont létrejött. És konfigurálja a végpontokat úgy, hogy a magánhálózati IP-címek mindegyike egy DNS-bejegyzésre legyen leképezve (lásd a fent látható válasz "fqdns" tulajdonságát).

A privát végpont létrehozásakor integrálhatja azt az Azure-beli privát DNS-zónával. Ha úgy dönt, hogy nem integrálja a privát végpontot az Azure-beli magánhálózati DNS-zónával, hanem egyéni DNS-t használ, úgy kell konfigurálnia a DNS-t, hogy új DNS-rekordot adjon hozzá az új régiónak megfelelő magánhálózati IP-címhez.

## <a name="firewall-configuration-with-private-link"></a>Tűzfal-konfiguráció magánhálózati kapcsolattal

A következő helyzetekben különböző helyzetek és eredmények jelennek meg, amikor a tűzfal szabályaival együtt használja a privát hivatkozást:

* Ha nincsenek konfigurálva tűzfalszabályok, akkor alapértelmezés szerint az Azure Cosmos-fiók minden forgalom számára elérhető.

* Ha a nyilvános forgalom vagy a szolgáltatási végpont konfigurálva van, és a magánhálózati végpontok létrejönnek, akkor a megfelelő tűzfalszabály engedélyezi a bejövő forgalom különböző típusait.

* Ha nincs konfigurálva nyilvános forgalom vagy szolgáltatási végpont, és a magánhálózati végpontok létrejönnek, akkor az Azure Cosmos-fiók csak a privát végpontokon keresztül érhető el. Ha nincs konfigurálva nyilvános forgalom vagy szolgáltatási végpont, az összes jóváhagyott privát végpont elutasítása vagy törlése után a fiók nyitva van az összes hálózat számára.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Privát végpont frissítése régió hozzáadásakor vagy eltávolításakor

A régiók Azure Cosmos-fiókhoz való hozzáadásával vagy eltávolításával ehhez a fiókhoz DNS-bejegyzéseket kell hozzáadnia vagy eltávolítania. Ezeket a módosításokat ennek megfelelően kell frissíteni a privát végponton. A következő lépések végrehajtásával jelenleg manuálisan kell elvégezni ezt a változást:

1. A Azure Cosmos DB-rendszergazda hozzáadja vagy eltávolítja a régiókat. Ezután a hálózati rendszergazdák értesítést kapnak a függőben lévő változásokról. Az Azure Cosmos-fiókhoz hozzárendelt privát végpont "ActionsRequired" tulajdonságát a "None" értékről az "újbóli létrehozás" értékre módosították. Ezután a hálózati rendszergazda úgy frissíti a privát végpontot, hogy létrehoz egy PUT kérelmet ugyanazzal a Resource Manager-adattartalommal, amelyet a létrehozásához használ.

1. A művelet elvégzése után az alhálózat privát DNS-ét is frissíteni kell, hogy tükrözze a hozzáadott vagy eltávolított DNS-bejegyzéseket, valamint a hozzájuk tartozó magánhálózati IP-címeket.

Ha például egy Azure Cosmos-fiókot helyez üzembe 3 régióban: "USA nyugati régiója", "Közép-USA" és "Nyugat-Európa". Ha saját fiókhoz hoz létre privát végpontot, a rendszer 4 magánhálózati IP-címet foglal le az alhálózaton. Egy minden régióhoz, amely összesen 3, a globális/régió-agnosztikus végponthoz pedig egy.

Később, ha új régiót ad hozzá (például "az USA keleti régiója") az Azure Cosmos-fiókhoz. Alapértelmezés szerint az új régió nem érhető el a meglévő privát végpontról. Az Azure Cosmos-fiók rendszergazdájának frissítenie kell a magánhálózati végponti kapcsolatokat, mielőtt az új régióhoz fér hozzá. 

Az ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` parancs futtatásakor a parancs kimenete tartalmazza a `actionsRequired` paramétert, amely az "újbóli létrehozás" értékre van beállítva. Ez az érték azt jelzi, hogy a magánhálózati végpontot frissíteni kell. Ezután az Azure Cosmos-fiók rendszergazdája a `Set-AzPrivateEndpoint` parancsot futtatja a privát végpont frissítésének elindításához.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Az új magánhálózati IP-cím automatikusan le van foglalva a privát végpont alatti alhálózatban, és a `actionsRequired` érték `None`válik. Ha nem rendelkezik privát DNZ-integrációval (azaz egyéni magánhálózati DNS-t használ), úgy kell konfigurálnia a privát DNS-t, hogy új DNS-rekordot adjon hozzá az új régiónak megfelelő magánhálózati IP-címhez.

A régió eltávolításakor ugyanezeket a lépéseket használhatja. Az eltávolított régió magánhálózati IP-címe automatikusan visszaigényelt, és a `actionsRequired` jelző `None`válik. Ha nem rendelkezik privát DNZ-integrációval, a magánhálózati DNS-t úgy kell konfigurálnia, hogy eltávolítsa az eltávolított régió DNS-rekordját.

A magánhálózati DNS-zónában lévő DNS-rekordokat a rendszer nem távolítja el automatikusan a privát végpont törlésekor, vagy eltávolítja az Azure Cosmos-fiókból származó régiót. Manuálisan el kell távolítania a DNS-rekordokat.

## <a name="current-limitations"></a>Aktuális korlátozások

Az alábbi korlátozások érvényesek a privát hivatkozás Azure Cosmos-fiókkal való használata esetén:

* Ha közvetlen módú kapcsolattal használ privát hivatkozásokat az Azure Cosmos-fiókkal, akkor csak a TCP protokollt használhatja. A HTTP protokoll még nem támogatott

* Ha a MongoDB-fiókok Azure Cosmos DB API-ját használja, a magánhálózati végpont csak a 3,6-es verziójú kiszolgálókon használható (azaz a végpontot a (z) `*.mongo.cosmos.azure.com`formátumban). A magánhálózati hivatkozás nem támogatott a (z) 3,2-es verziójú kiszolgálókon lévő fiókok esetében (azaz a végpontot használó fiókok a következő formátumban: `*.documents.azure.com`). A privát hivatkozás használatához át kell telepítenie a régi fiókokat az új verzióra.

* Ha a Azure Cosmos DB API-ját privát kapcsolattal rendelkező MongoDB-fiókokhoz használja, nem használhat olyan eszközöket, mint például a Robo 3T, a Studio 3T, a mongúz stb. A végpont csak akkor rendelkezhet privát hivatkozásokkal, ha meg van adva a appName =<account name> paraméter. Például: replicaSet = globaldb & appName = mydbaccountname. Mivel ezek az eszközök nem adják át az alkalmazás nevét a kapcsolati sztringben a szolgáltatáshoz, így nem használhat privát hivatkozást. Ezeket a fiókokat azonban továbbra is elérheti az SDK-illesztőprogramok 3,6-es verziójával.

* Az Azure Cosmos-fiókok és a virtuális hálózatok privát kapcsolatának támogatása csak meghatározott régiókban érhető el. A támogatott régiók listájáért tekintse meg a privát hivatkozás [elérhető régiók](../private-link/private-link-overview.md#availability) című szakaszát. A **VNET és az Azure Cosmos-fióknak a támogatott régiókban kell lennie ahhoz, hogy létre lehessen hozni egy privát végpontot**.

* Egy virtuális hálózat nem helyezhető át vagy törölhető, ha privát hivatkozást tartalmaz.

* Egy Azure Cosmos-fiók nem törölhető, ha privát végponthoz van csatolva.

* Egy Azure Cosmos-fiókot nem lehet átadni olyan régióhoz, amely nincs hozzárendelve az összes hozzá csatolt privát végponthoz. További információ: régiók hozzáadása vagy eltávolítása az előző szakaszban.

* A rendszergazdának meg kell adni legalább a "*/PrivateEndpointConnectionsApproval" engedélyt az Azure Cosmos-fiók hatókörében a rendszergazda által automatikusan jóváhagyott privát végpontok létrehozásához.

### <a name="private-dns-zone-integration-limitations"></a>saját DNS zónák integrációs korlátai

A magánhálózati DNS-zónában lévő DNS-rekordokat a rendszer nem távolítja el automatikusan a privát végpont törlésekor, vagy eltávolítja az Azure Cosmos-fiókból származó régiót. A DNS-rekordokat manuálisan kell eltávolítania a következő előtt:

* Ehhez a magánhálózati DNS-zónához társított új privát végpont hozzáadása.
* Új régió hozzáadása bármely olyan adatbázis-fiókhoz, amely az ehhez a magánhálózati DNS-zónához csatolt privát végpontokkal rendelkezik.

A DNS-rekordok tisztítása nélkül nem várt adatforgalmi problémák merülhetnek fel, mint például az adatkimaradás a privát végpont eltávolítása vagy a régió eltávolítása után hozzáadott régióknál

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Cosmos DB biztonsági funkcióiról, tekintse meg a következő cikket:

* Ha Azure Cosmos DB tűzfalat szeretne konfigurálni, tekintse meg a [tűzfal támogatása](firewall-support.md)című témakört.

* [Virtuális hálózati szolgáltatás végpontjának konfigurálása az Azure Cosmos-fiókhoz.](how-to-configure-vnet-service-endpoint.md)

* Ha többet szeretne megtudni a privát hivatkozásokról, tekintse meg az [Azure Private link](../private-link/private-link-overview.md) dokumentációját.
