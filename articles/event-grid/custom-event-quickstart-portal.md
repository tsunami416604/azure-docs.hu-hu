---
title: Egyéni események az Azure Event Gridhez az Azure Portal használatával | Microsoft Docs
description: Az Azure Event Grid és a PowerShell segítségével közzétehet egy témakört, és feliratkozhat a kapcsolódó eseményre.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/23/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: f1185c0b2d5d320cd712642f422408348bee7a37
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Egyéni események létrehozása és átirányítása az Azure Portallal és az Event Griddel

Az Azure Event Grid egy felhőalapú eseménykezelési szolgáltatás. Ebben a cikkben létrehozunk egy egyéni témakört az Azure Portallal, feliratkozunk a témakörre, majd kiváltjuk az eseményt az eredmény megtekintéséhez. Az eseményt elküldi az eseményadatokat naplózó Azure-függvénynek. A folyamat végén látni fogja, hogy a rendszer elküldte az eseményadatokat egy végpontnak, és naplózta őket.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-custom-topic"></a>Egyéni témakör létrehozása

Az Event Grid-témakörök egy felhasználó által meghatározott végpontot biztosítanak, amelyben közzéteheti az eseményeket. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Egyéni témakör létrehozásához válassza az **Erőforrás létrehozása** elemet. 

   ![Erőforrás létrehozása](./media/custom-event-quickstart-portal/create-resource.png)

1. Keressen rá az *Event Grid-témakör* kifejezésre, és válassza ki az elérhető lehetőségek közül.

   ![Event Grid-témakör keresése](./media/custom-event-quickstart-portal/search-event-grid.png)

1. Kattintson a **Létrehozás** gombra.

   ![Kezdeti lépések](./media/custom-event-quickstart-portal/select-create.png)

1. Adjon egy egyedi nevet az egyéni témakörnek. A témakör nevének egyedinek kell lennie, mert a nevet egy DNS-bejegyzés képviseli. Ne a képen látható nevet használja. Ehelyett hozzon létre egy saját nevet. Válasszon egyet a [támogatott régiók](overview.md) közül. Adja meg az erőforráscsoport nevét. Kattintson a **Létrehozás** gombra.

   ![Event Grid-témakör értékeinek megadása](./media/custom-event-quickstart-portal/create-custom-topic.png)

1. Az egyéni témakör létrehozása után láthatja a sikeres műveletről szóló értesítést.

   ![Sikeres műveletről szóló értesítés megtekintése](./media/custom-event-quickstart-portal/success-notification.png)

   Ha az üzembe helyezés nem sikerült, megtudhatja, mi okozta a hibát. Válassza az **Üzembe helyezés sikertelen** elemet.

   ![Az Üzembe helyezés sikertelen elem kiválasztása](./media/custom-event-quickstart-portal/select-failed.png)

   Válassza ki a hibaüzenetet.

   ![Az Üzembe helyezés sikertelen elem kiválasztása](./media/custom-event-quickstart-portal/failed-details.png)

   Az alábbi képen egy olyan üzembe helyezés látható, amely azért volt sikertelen, mert az egyéni témakör neve már használatban van. Ha ezt a hibát látja, próbáljon meg egy másik nevet használni az üzembe helyezéshez.

   ![Névütközés](./media/custom-event-quickstart-portal/name-conflict.png)

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

A témakörre való feliratkozás előtt hozzuk létre az eseményüzenet végpontját. Ebben a cikkben Azure-függvényekkel hozhat létre egy függvényalkalmazást a végponthoz.

1. Függvény létrehozásához válassza az **Erőforrás létrehozása** elemet.

   ![Erőforrás létrehozása](./media/custom-event-quickstart-portal/create-resource-small.png)

1. Válassza a **Számítás** és a **Függvényalkalmazás** elemet.

   ![Függvény létrehozása](./media/custom-event-quickstart-portal/create-function.png)

1. Adjon egy egyedi nevet az Azure-függvénynek. Ne a képen látható nevet használja. Válassza ki az ebben a cikkben létrehozott erőforráscsoportot. Szolgáltatási csomagként válassza a **Használatalapú csomagot**. Használja a javasolt új tárfiókot. Az értékek megadása után válassza a **Létrehozás** elemet.

   ![Függvényértékek megadása](./media/custom-event-quickstart-portal/provide-function-values.png)

1. Az üzembe helyezés befejezése után válassza az **Erőforrás megnyitása** elemet.

   ![Erőforrás megnyitása](./media/custom-event-quickstart-portal/go-to-resource.png)

1. A **Függvények** mellett válassza a **+** elemet.

   ![Függvény hozzáadása](./media/custom-event-quickstart-portal/add-function.png)

1. Válassza az **Egyéni függvény** elemet az elérhető lehetőségek közül.

   ![Egyéni függvény](./media/custom-event-quickstart-portal/select-custom-function.png)

1. Görgessen lefelé, amíg meg nem találja az **Event Grid-triggert**. Válassza a **C#** elemet.

   ![Event Grid-trigger kiválasztása](./media/custom-event-quickstart-portal/select-event-grid-trigger.png)

1. Fogadja el az alapértelmezett értékeket, és válassza a **Létrehozás** elemet.

   ![Új függvény](./media/custom-event-quickstart-portal/new-function.png)

A függvény készen áll az események fogadására.

## <a name="subscribe-to-a-topic"></a>Feliratkozás témakörre

A témakörre való feliratkozással lehet tudatni az Event Griddel, hogy mely eseményeket kívánja nyomon követni, és hová szeretné küldeni az eseményeket.

1. Az Azure-függvényben válassza az **Event Grid-előfizetés hozzáadása** elemet.

   ![Event Grid-előfizetés hozzáadása](./media/custom-event-quickstart-portal/add-event-grid-subscription.png)

1. Adja meg az előfizetés értékeit. A témakör típusánál válassza az **Event Grid-témakörök** elemet. Az előfizetéshez és az erőforráscsoporthoz válassza ki azt az előfizetést és erőforráscsoportot, amelyben létrehozta az egyéni témakört. Válassza ki például az egyéni témakör nevét. Az előfizető végpontja előre ki van töltve a függvény URL-jével.

   ![Előfizetés értékeinek megadása](./media/custom-event-quickstart-portal/provide-subscription-values.png)

1. Az esemény elindítása előtt nyissa meg a függvény naplóit, hogy lássa az eseményadatokat az elküldéskor. Az Azure-függvény alján válassza a **Naplók** elemet.

   ![Naplók kiválasztása](./media/custom-event-quickstart-portal/select-logs.png)

Most aktiváljunk egy eseményt, és lássuk, hogyan küldi el az üzenetet az Event Grid a végpontnak. A cikkben leírt folyamat leegyszerűsítése érdekében a Cloud Shell használatával küldjön mintául szolgáló eseményadatokat az egyéni témakörbe. Egy alkalmazás vagy Azure-szolgáltatás általában eseményadatokat küld el.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Esemény elküldése a témakörbe

Először szükségünk lesz a témakör URL-címére és kulcsára. A `<topic_name>` helyett használja a témakör nevét.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g myResourceGroup --query "key1" --output tsv)
```

Az alábbi példa mintául szolgáló eseményadatokat kér le:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

A teljes esemény megjelenítéséhez használja az `echo "$body"` elemet. A JSON `data` eleme az esemény hasznos adata. Bármilyen, megfelelően formált JSON megadható ebben a mezőben. A speciális útválasztáshoz és szűréshez használhatja a tárgy mezőt is.

A CURL egy olyan segédprogram, amely HTTP-kéréseket küld. Ebben a cikkben a CURL használatával küldünk egy eseményt az egyéni témakörbe. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Ön kiváltotta az eseményt, az Event Grid pedig elküldte az üzenetet a feliratkozáskor konfigurált végpontnak. Az eseményadatokat a naplókban tekintheti meg.

![Naplók megtekintése](./media/custom-event-quickstart-portal/view-log-entry.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tovább kívánja használni az eseményt, akkor ne törölje a cikkben létrehozott erőforrásokat. Ellenkező esetben törölje a cikkben létrehozott erőforrásokat.

Válassza ki az erőforráscsoportot, majd válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egyéni témaköröket és esemény-előfizetéseket, bővebben is tájékozódhat arról, hogy miben nyújthat segítséget az Event Grid:

- [Bevezetés az Event Grid használatába](overview.md)
- [Azure Blob Storage-események átirányítása egyéni webes végpontra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps segítségével](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Big Data típusú adatok streamelése adattárházba](event-grid-event-hubs-integration.md)
