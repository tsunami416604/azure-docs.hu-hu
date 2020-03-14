---
title: Azure Event Grid eseményeinek szűrése
description: Ez a cikk bemutatja, hogyan szűrhetők az események (eseménytípus szerint, tárgy szerint, operátorok és adattípusok alapján) Event Grid előfizetés létrehozásakor.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 58da209c68449d3a28b08f52ec575f7db520f121
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244341"
---
# <a name="filter-events-for-event-grid"></a>Event Grid eseményeinek szűrése

Ez a cikk bemutatja, hogyan szűrheti az eseményeket Event Grid előfizetés létrehozásakor. Az események szűrésének lehetőségeiről az [Event Grid-előfizetések eseményeinek szűrését](event-filtering.md)ismertető cikk nyújt tájékoztatást.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Szűrés eseménytípus szerint

Event Grid-előfizetés létrehozásakor megadhatja, hogy mely [típusú események](event-schema.md) legyenek elküldve a végpontnak. Az ebben a szakaszban szereplő példák egy erőforráscsoport esemény-előfizetéseit hoznak létre, de korlátozzák a `Microsoft.Resources.ResourceWriteFailure` és `Microsoft.Resources.ResourceWriteSuccess`számára eljuttatott eseményeket. Ha az események eseménytípus szerinti szűrése nagyobb rugalmasságot igényel, tekintse meg a következő témakört: szűrés speciális operátorok és adatmezők alapján.

A PowerShell esetében használja az `-IncludedEventType` paramétert az előfizetés létrehozásakor.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Az Azure CLI esetén használja a `--included-event-types` paramétert. Az alábbi példa egy bash-rendszerhéjban az Azure CLI-t használja:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Resource Manager-sablonok esetén használja a `includedEventTypes` tulajdonságot.

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

Az eseményeket a tárgy szerint szűrheti az esemény adataiban. Megadhat egy értéket, amely megegyezik a tárgy elejéhez vagy végéhez. Ha több rugalmasságra van szüksége az események tárgy szerinti szűréséhez, tekintse meg a szűrés speciális operátorok és az adatmezők alapján című témakört.

A következő PowerShell-példában egy olyan esemény-előfizetést hoz létre, amely a tárgy elejétől szűr. A `-SubjectBeginsWith` paraméterrel egy adott erőforrásra korlátozza az eseményeket. Egy hálózati biztonsági csoport erőforrás-AZONOSÍTÓját adja meg.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

A következő PowerShell-példa létrehoz egy előfizetést a blob Storage-hoz. Az eseményeket a `.jpg`ban végződő tárgyat is korlátozza.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

A következő Azure CLI-példában létrehozunk egy esemény-előfizetést, amely a tárgy elejére szűri. A `--subject-begins-with` paraméterrel egy adott erőforrásra korlátozza az eseményeket. Egy hálózati biztonsági csoport erőforrás-AZONOSÍTÓját adja meg.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

A következő Azure CLI-példa létrehoz egy előfizetést a blob Storage-hoz. Az eseményeket a `.jpg`ban végződő tárgyat is korlátozza.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

A következő Resource Manager-sablonban példaként hozzon létre egy esemény-előfizetést, amely a tárgy kezdete alapján szűri. A `subjectBeginsWith` tulajdonsággal korlátozhatja az eseményeket egy adott erőforrásra vonatkozóan. Egy hálózati biztonsági csoport erőforrás-AZONOSÍTÓját adja meg.

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

A következő Resource Manager-sablon példa létrehoz egy előfizetést a blob Storage-hoz. Az eseményeket a `.jpg`ban végződő tárgyat is korlátozza.

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

## <a name="filter-by-operators-and-data"></a>Szűrés operátorok és adathalmazok alapján

A szűrés rugalmassága érdekében az operátorok és az Adattulajdonságok segítségével szűrheti az eseményeket.

### <a name="subscribe-with-advanced-filters"></a>Előfizetés speciális szűrőkkel

A speciális szűréshez használható operátorok és kulcsok megismeréséhez tekintse meg a [speciális szűrést](event-filtering.md#advanced-filtering)ismertető témakört.

Ezek a példák egyéni témakört hoznak létre. Előfizetnek az egyéni témakörre, és egy érték alapján szűrik az adatobjektumban. Azok az események, amelyeken a Color tulajdonság kék, vörös vagy zöld értékre van állítva, a rendszer elküldi az előfizetésnek.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
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

```azurepowershell-interactive
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

### <a name="test-filter"></a>Teszt szűrő

A szűrő teszteléséhez küldjön egy olyan eseményt, amelyben a szín mező zöld értékre van állítva. Mivel a zöld a szűrő egyik értéke, az eseményt a rendszer a végpontnak továbbítja.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
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

Egy olyan forgatókönyv teszteléséhez, amelyben az esemény nincs elküldve, küldjön egy olyan eseményt, amelyben a szín mező sárga értékre van állítva. A sárga nem az előfizetésben megadott értékek egyike, így az esemény nem érkezik meg az előfizetésbe.

Azure CLI esetén használja az alábbi parancsot:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
PowerShell esetén használja az alábbi parancsot:

```azurepowershell-interactive
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

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítésének figyeléséről: [Event Grid üzenet kézbesítésének figyelése](monitor-event-delivery.md).
* További információ a hitelesítési kulcsról: [Event Grid biztonság és hitelesítés](security-authentication.md).
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).
