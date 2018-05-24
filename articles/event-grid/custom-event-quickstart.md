---
title: Egyéni események az Azure Event Gridhez parancssori felület használatával | Microsoft Docs
description: Az Azure Event Grid és az Azure CLI segítségével közzétehet egy témakört, és feliratkozhat a kapcsolódó eseményre.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: d68df064614c262bd9755be0688841fdb64af762
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/18/2018
---
# <a name="create-and-route-custom-events-with-azure-cli-and-event-grid"></a>Egyéni események létrehozása és átirányítása az Azure CLI-vel és az Event Griddel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure CLI-vel, feliratkozunk a témakörre, majd elindítjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan URL-címnek küldjük el, amely pusztán üzenetek gyűjtésével foglalkozik. Az URL-címet a [Hookbin](https://hookbin.com/) külső eszközével fogjuk létrehozni.

>[!NOTE]
>A **Hookbin** a nagy teljesítményt megkövetelő használati területekhez nem alkalmas. Az eszköz jelen használata kizárólag bemutató célt szolgál. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat egy végpontnak.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI legújabb verzióját (2.0.24 vagy újabb) kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` paranccsal.

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

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Az eseményre reagáló kód írása helyett egy olyan végpontot hozzunk létre, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni. A Hookbin egy külső eszköz, amely lehetővé teszi egy végpont létrehozását és a neki küldött kérések megtekintését. Nyissa meg a [Hookbin](https://hookbin.com/) eszközt, és kattintson a **Create New Endpoint** (Új végpont létrehozása) elemre.  Másolja ki a tár URL-címét, mert szüksége lesz rá, amikor feliratkozik az egyéni témakörre.

## <a name="subscribe-to-a-topic"></a>Feliratkozás témakörre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. Az alábbi példa feliratkozik a létrehozott témakörre, és az eseményértesítés végpontjaként átadja a Hookbinből származó URL-címet. Az `<event_subscription_name>` elemet a feliratkozás egyedi nevére cserélje le, az `<endpoint_URL>` elemet pedig az előző szakaszból származó értékre. Ha megadja a végpontot a feliratkozáskor, az Event Grid az adott végpontra irányítja az eseményeket. A `<topic_name>` elemnél a korábban létrehozott értéket adja meg. 

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először szükségünk lesz az egyéni témakör URL-címére és kulcsára. A `<topic_name>` helyett használja ismét a témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

A folyamat leegyszerűsítése érdekében használjon mintául szolgáló eseményadatokat, amelyeket elküldhet a témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. Az alábbi példa lekéri az eseményadatokat:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

A teljes esemény megjelenítéséhez használja az `echo "$body"` elemet. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is.

A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldjük el az eseményt a témakörbe. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Lépjen a végpont korábban létrehozott URL-címére, vagy kattintson a megnyitott böngésző frissítés gombjára. Megjelenik az imént elküldött esemény. 

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

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
