---
title: "Egyéni események az Azure Event Gridhez a PowerShell használatával | Microsoft Docs"
description: "Az Azure Event Grid és a PowerShell segítségével közzétehet egy témakört, és feliratkozhat a kapcsolódó eseményre."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 89c71194c2ef3c34b3356040c2e252fc09ba09c3
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Egyéni események létrehozása és átirányítása az Azure PowerShell-lel és az Event Griddel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure PowerShell-lel, feliratkozunk a témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Általában olyan végpontoknak szoktunk eseményeket küldeni, amelyek reagálnak az eseményre, például egy webhooknak vagy egy Azure-függvénynek. A cikk egyszerűsítése érdekében azonban az eseményeket egy olyan URL-címnek küldjük el, amely pusztán üzenetek gyűjtésével foglalkozik. Az URL-címet a [RequestBin](https://requestb.in/) nevű nyílt forráskódú, külső fél által biztosított eszközzel fogjuk létrehozni.

>[!NOTE]
>A **RequestBin** egy nyílt forráskódú eszköz, amely nagy teljesítményt megkövetelő használati területekhez nem alkalmas. Az eszköz használata ebben az esetben kizárólag bemutató célt szolgál. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

A folyamat végén látni fogja, hogy az eseményadatokat egy végpontnak küldte el a rendszer.

![Eseményadatok](./media/custom-event-quickstart-powershell/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Ehhez a cikkhez az Azure PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Event Grid-témakörök Azure-erőforrások, amelyeket egy Azure-erőforráscsoportba kell helyezni. Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal.

A következő példában létrehozunk egy *gridResourceGroup* nevű erőforráscsoportot a *westus2* helyen.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

A témakör egy felhasználó által meghatározott végpontot biztosít, amelybe elküldheti az eseményeket. Az alábbi példa az erőforráscsoportban hozza létre a témakört. A `<topic_name>` elemet a témakör egyedi nevére cserélje le. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Az előzetes verzió esetén az Event Grid a **westus2** és a **westcentralus** helyet támogatja.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Üzenetvégpont létrehozása

A témakörre való előfizetés előtt hozzuk létre az eseményüzenet végpontját. Az eseményre reagáló kód írása helyett egy olyan végpontot hozzunk létre, amely gyűjti az üzeneteket, hogy meg tudja őket tekinteni. A RequestBin egy nyílt forráskódú, külső gyártótól származó eszköz, amely lehetővé teszi egy végpont létrehozását és a neki küldött kérések megtekintését. Nyissa meg a [RequestBin](https://requestb.in/) eszközt, és kattintson a **Create a RequestBin** (Kéréstár létrehozása) elemre.  Másolja ki a tár URL-címét, mert szüksége lesz rá, amikor feliratkozik a témakörre.

## <a name="subscribe-to-a-topic"></a>Előfizetés témakörre

A témakörre való előfizetéssel lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni. Az alábbi példa feliratkozik a létrehozott témakörre, és az eseményértesítés végpontjaként adja át a RequestBinből átemelt URL-címet. Az `<event_subscription_name>` elemet az előfizetés egyedi nevére cserélje le, az `<URL_from_RequestBin>` elemet pedig az előző szakaszból származó értékre. Ha megadja a végpontot az előfizetéskor, az Event Grid az adott végpontra irányítja az eseményeket. A `<topic_name>` elemnél a korábban létrehozott értéket adja meg.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <URL_from_RequestBin> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. Először szükségünk lesz a témakör URL-címére és kulcsára. A `<topic_name>` helyett használja ismét a témakör nevét.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

A folyamat leegyszerűsítése érdekében hozzon létre mintául szolgáló eseményadatokat, amelyeket elküldhet a témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el. Az alábbi példa lekéri az eseményadatokat:

```powershell
$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$body = "[{`"id`": `"$eventID`",`"eventType`": `"recordInserted`",`"subject`": `"myapp/vehicles/motorcycles`",`"eventTime`": `"$eventDate`",`"data`":{`"make`": `"Ducati`",`"model`": `"Monster`"}}]"
```

Az `$body` használatával a teljes eseményt láthatja. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is.

Most küldje el az eseményt a témakörbe.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet az előfizetéskor konfigurált végpontnak. Lépjen a RequestBin eszközben korábban létrehozott URL-címre, vagy kattintson a megnyitott RequestBin-böngésző frissítés elemére. Megjelenik az imént elküldött esemény.

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
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az esemény használatát, akkor ne törölje a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett vele, hogyan hozhat létre témaköröket és eseményelőfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
