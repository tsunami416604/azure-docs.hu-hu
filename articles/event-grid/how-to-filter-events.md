---
title: Események szűrése az Azure Event Gridhöz
description: Ez a cikk bemutatja, hogyan szűrheti az eseményeket (eseménytípus, tárgy, operátorok és adatok stb.) az Event Grid-előfizetés létrehozásakor.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 63a5cdbff79af52d9f96cf410a820c6cfc530066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454023"
---
# <a name="filter-events-for-event-grid"></a>Eseményrács eseményeinek szűrése

Ez a cikk bemutatja, hogyan szűrheti az eseményeket az Event Grid-előfizetés ek létrehozásakor. Az eseményszűrés lehetőségeiről az [Eseményrács-előfizetések eseményszűrésének ismertetése](event-filtering.md)című témakörben olvashat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Szűrés eseménytípus szerint

Event Grid-előfizetés létrehozásakor megadhatja, hogy mely [eseménytípusokat](event-schema.md) küldje el a végpontra. Ebben a szakaszban szereplő példák esemény-előfizetéseket hoznak létre `Microsoft.Resources.ResourceWriteFailure` `Microsoft.Resources.ResourceWriteSuccess`egy erőforráscsoporthoz, de korlátozzák a rendszerbe küldött eseményeket. Ha nagyobb rugalmasságra van szüksége az események eseménytípusok szerinti szűrésekénél, olvassa el a Szűrés speciális operátorok és adatmezők szerint.

A PowerShell esetében `-IncludedEventType` használja a paramétert az előfizetés létrehozásakor.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Az Azure CLI `--included-event-types` esetében használja a paramétert. A következő példa az Azure CLI-t használja egy Bash rendszerhéjban:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Erőforrás-kezelő sablon esetén `includedEventTypes` használja a tulajdonságot.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Szűrés tárgy szerint

Az eseményeket az eseményadatok tárgya szerint szűrheti. Megadhatja a téma kezdetének vagy végének megfelelő értéket. Ha nagyobb rugalmasságra van szüksége az események tárgy szerinti szűrésekénél, olvassa el a Szűrés speciális operátorok és adatmezők szerint.

A következő PowerShell-példában létrehoz egy esemény-előfizetést, amely a tárgy elejére szűr. A paraméter `-SubjectBeginsWith` segítségével egy adott erőforrás eseményeire korlátozhatja az eseményeket. Egy hálózati biztonsági csoport erőforrás-azonosítóját adja át.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

A következő PowerShell-példa létrehoz egy-egy blob storage-előfizetést. Az eseményeket azokra korlátozza, amelyek `.jpg`alanya a .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

A következő Azure CLI-példában hozzon létre egy esemény-előfizetést, amely a téma elejére szűr. A paraméter `--subject-begins-with` segítségével egy adott erőforrás eseményeire korlátozhatja az eseményeket. Egy hálózati biztonsági csoport erőforrás-azonosítóját adja át.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

A következő Azure CLI-példa létrehoz egy blob storage-előfizetést. Az eseményeket azokra korlátozza, amelyek `.jpg`alanya a .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

A következő Erőforrás-kezelő sablonpéldában olyan esemény-előfizetést hoz létre, amely a tárgy elejére szűr. A tulajdonság `subjectBeginsWith` segítségével egy adott erőforrás eseményeire korlátozhatja az eseményeket. Egy hálózati biztonsági csoport erőforrás-azonosítóját adja át.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

A következő Erőforrás-kezelő sablon példában létrehoz egy előfizetést egy blob storage. Az eseményeket azokra korlátozza, amelyek `.jpg`alanya a .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Szűrés operátorok és adatok szerint

A szűrés nagyobb rugalmassága érdekében operátorok és adattulajdonságok használatával szűrheti az eseményeket.

### <a name="subscribe-with-advanced-filters"></a>Feliratkozás speciális szűrőkkel

A speciális szűréshez használható operátorokról és kulcsokról a [Speciális szűrés című témakörben olvashat.](event-filtering.md#advanced-filtering)

Ezek a példák egyéni témakört hoznak létre. Előfizetnek az egyéni témakörre, és az adatobjektum egy értéke szerint szűrnek. Az előfizetés azokkal az eseményekkel, amelyek színtulajdonsága kékre, pirosra vagy zöldre van állítva.

Azure CLI esetén használja az alábbi parancsot:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Vegye figyelembe, hogy az előfizetéshez [lejárati dátum](concepts.md#event-subscription-expiration) tartozik.

PowerShell esetén használja az alábbi parancsot:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Tesztszűrő

A szűrő teszteléséhez küldjön egy eseményt, amelynek színmezője zöldre van állítva. Mivel a zöld a szűrő egyik értéke, az esemény a végpontra kerül.

Azure CLI esetén használja az alábbi parancsot:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

PowerShell esetén használja az alábbi parancsot:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Ha olyan esetet szeretne tesztelni, amelyben az esemény nem kerül elküldésre, küldjön egy eseményt, amelyben a színmező sárga lesz. A sárga nem az előfizetésben megadott értékek egyike, így az esemény nem lesz kézbesítve az előfizetéshez.

Azure CLI esetén használja az alábbi parancsot:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
PowerShell esetén használja az alábbi parancsot:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésfigyelésről az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakörben talál további információt.
* A hitelesítési kulcsról további információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
