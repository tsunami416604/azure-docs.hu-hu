---
title: "Egyéni események az Azure Event Gridhez az Azure Portal használatával | Microsoft Docs"
description: "Az Azure Event Grid és a PowerShell segítségével közzétehet egy témakört, és feliratkozhat a kapcsolódó eseményre."
services: event-grid
keywords: 
author: tfitzmac
ms.author: tomfitz
ms.date: 01/30/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: f37d496d43bb24c51d6e1c11b77d9ceba48b7b23
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Egyéni események létrehozása és átirányítása az Azure Portallal és az Event Griddel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk a témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan URL-címnek küldjük el, amely pusztán üzenetek gyűjtésével foglalkozik. Az URL-címet a [RequestBin](https://requestb.in/) vagy a [Hookbin](https://hookbin.com/) külső fejlesztésű eszközeivel fogjuk létrehozni.

>[!NOTE]
>A **RequestBin** és a **Hookbin** a nagy teljesítményt megkövetelő használati területekhez nem alkalmas. Az eszközök jelen használata kizárólag bemutató célt szolgál. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

A folyamat végén látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

![Eseményadatok](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

1. A bal oldali navigációs menüben válassza az **Erőforráscsoportok** lehetőséget. Ezután válassza a **Hozzáadás** lehetőséget.

   ![Erőforráscsoport létrehozása](./media/custom-event-quickstart-portal/create-resource-group.png)

1. Állítsa be a *gridResourceGroup* nevet és a *westus2* helyet az erőforráscsoportnak. Kattintson a **Létrehozás** gombra.

   ![Adja meg az erőforráscsoport értékeit](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

A témakör egy felhasználó által meghatározott végpontot biztosít, amelybe elküldheti az eseményeket. 

1. Ha létre kíván hozni egy témakört az erőforráscsoportban, válassza a **Minden szolgáltatás** lehetőséget, és keressen az *event grid* kifejezésre. Válassza az **Event Grid-témakörök** lehetőséget az elérhető lehetőségek közül.

   ![Event Grid-témakör létrehozása](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. Válassza a **Hozzáadás** lehetőséget.

   ![Event Grid-témakör hozzáadása](./media/custom-event-quickstart-portal/add-topic.png)

1. Adja meg a témakör nevét. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Válasszon egyet a [támogatott régiók](overview.md) közül. Válassza ki a korábban létrehozott erőforráscsoportot. Kattintson a **Létrehozás** gombra.

   ![Event Grid-témakör értékeinek megadása](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. A témakör létrehozását követően válassza a **Frissítés** lehetőséget a megtekintéséhez.

   ![Event Grid-témakör megtekintése](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Az eseményre reagáló kód írása helyett egy olyan végpontot hozzunk létre, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni. A RequestBin és a Hookbin külső gyártótól származó eszközök, amelyek végpontok létrehozását és az azokra küldött kérések megtekintését teszik lehetővé. Nyissa meg a [RequestBin](https://requestb.in/) eszközt, és kattintson a **Create a RequestBin** (Kéréstár létrehozása) elemre, vagy nyissa meg a [Hookbin](https://hookbin.com/) eszközt, és kattintson a **Create New Endpoint** (Új végpont létrehozása) elemre.  Másolja ki a tár URL-címét, mert szüksége lesz rá, amikor feliratkozik a témakörre.

## <a name="subscribe-to-a-topic"></a>Feliratkozás témakörre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. 

1. Event Grid-előfizetés létrehozásához válassza ismét a **Minden szolgáltatás** lehetőséget, és keressen az *event grid* kifejezésre. Válassza az **Event Grid-előfizetések** lehetőséget az elérhető lehetőségek közül.

   ![Event Grid-előfizetés létrehozása](./media/custom-event-quickstart-portal/create-subscription.png)

1. Válassza a **+ Esemény-előfizetés** lehetőséget.

   ![Event Grid-előfizetés hozzáadása](./media/custom-event-quickstart-portal/add-subscription.png)

1. Adjon egy egyedi nevet az esemény-előfizetésnek. A témakör típusánál válassza az **Event Grid-témakörök** lehetőséget. A példányhoz válassza ki a létrehozott egyéni témakört. Az eseményértesítés végpontjaként adja meg a RequestBinből vagy a Hookbinből átemelt URL-címet. Miután végzett az értékek megadásával, válassza a **Létrehozás** lehetőséget.

   ![Event Grid-előfizetés értékeinek megadása](./media/custom-event-quickstart-portal/provide-subscription-values.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. A folyamat leegyszerűsítése érdekében a Cloud Shell használatával küldjön mintául szolgáló eseményadatokat a témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Először szükségünk lesz a témakör URL-címére és kulcsára. A `<topic_name>` helyett használja a témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Az alábbi példa mintául szolgáló eseményadatokat kér le:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Ha kiadja az `echo "$body"` parancsot, a teljes eseményt megtekintheti. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is.

A CURL egy olyan segédprogram, amely HTTP-kéréseket hajt végre. Ebben a cikkben a CURL használatával küldünk egy eseményt a témakörbe. 

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

Ha azt tervezi, hogy folytatja az esemény használatát, akkor ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, törölje a cikkben létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményfeliratkozásokat, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
