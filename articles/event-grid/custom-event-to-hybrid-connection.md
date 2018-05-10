---
title: Egyéni események Azure esemény rács küldeni a hibrid kapcsolat |} Microsoft Docs
description: Az Azure Event Grid és az Azure CLI segítségével közzétehet egy témakört, és feliratkozhat a kapcsolódó eseményre. A végpont egy hibrid kapcsolat használható.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 05/04/2018
ms.topic: article
ms.service: event-grid
ms.openlocfilehash: 42b3e88d4bf411aa8a0d3bb129795f0d8ab98525
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Egyéni események átirányítása Azure hibrid kapcsolatok az Azure CLI és az esemény rács

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Az Azure hibrid kapcsolatok a támogatott eseménykezelők egyike. Hibrid kapcsolatok használják az eseménykezelő, amikor kell feldolgozni az eseményeket, amelyek nem rendelkeznek egy nyilvános végpontot alkalmazásokból. Előfordulhat, hogy ezeket az alkalmazásokat a vállalati hálózaton belül. Ebben a cikkben létrehozunk egy egyéni témakört az Azure CLI-vel, feliratkozunk a témakörre, majd elindítjuk az eseményt az eredmény megtekintéséhez. Az események küldése a hibrid kapcsolat.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már rendelkezik egy hibrid kapcsolat és egy figyelő alkalmazás. Első lépésként a hibrid kapcsolatok, lásd: [Ismerkedés a hibrid kapcsolatok - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) vagy [Ismerkedés a hibrid kapcsolatok - csomópont](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. 

A következő példában létrehozunk egy *gridResourceGroup* nevű erőforráscsoportot a *westus2* helyen.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. Az alábbi példa az erőforráscsoportban létrehozza az egyéni témakört. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>Feliratkozás témakörre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. A következő példa a témakör hozott létre, és adja át az erőforrás-azonosítója a végpont a hibrid kapcsolat számítógépcsoportra fizetett elő. A hibrid kapcsolat azonosítója a következő formátumban:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

A következő parancsfájl lekérdezi az erőforrás-azonosítója a továbbítási névtér. A hibrid kapcsolat Azonosítót hoz létre, és egy esemény rács témakör számítógépcsoportra fizetett elő. Azt állítja be a következő típusú végpont `hybridconnection` és a hibrid kapcsolat Azonosítóját használja a végpont.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először szükségünk lesz az egyéni témakör URL-címére és kulcsára. A `<topic_name>` helyett használja ismét a témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

A folyamat leegyszerűsítése érdekében használjon mintául szolgáló eseményadatokat, amelyeket elküldhet a témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldjük el az eseményt a témakörbe.  Az alábbi példában három az eseményeket az esemény a rács témakör küldi:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

A figyelő alkalmazás a következő esemény üzenetet kell látnia.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
