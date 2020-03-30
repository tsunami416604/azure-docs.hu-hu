---
title: Eseményközpont létrehozása az Azure CLI - Azure Event Hubs használatával | Microsoft dokumentumok
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy eseményközpontot az Azure CLI-vel, majd hogyan küldhet és fogadhat eseményeket a Java használatával.
services: event-hubs
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: seodec18
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 92fd7d15ee5bc54cc41b78f4ba0d078d3f8fac6b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162633"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Rövid útmutató: Eseményközpont létrehozása az Azure CLI-vel

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az Azure CLI használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió ellenőrzéséhez. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az alábbi lépések nem szükségesek, ha a parancsokat a Cloud Shellben futtatja. Ha helyileg futtatja a CLI-t, az alábbi lépések elvégzésével jelentkezzen be az Azure-ba, és állítsa be az aktuális előfizetést:

Az alábbi parancs futtatásával jelentkezzen be az Azure-ba:

```azurecli-interactive
az login
```

Állítsa be az aktuális előfizetési környezetet. A `MyAzureSub` értéket cserélje le a használni kívánt Azure-előfizetés nevére:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Minden erőforrás üzembe helyezése és kezelése erőforráscsoportban történik. Futtassa az alábbi parancsot egy erőforráscsoport létrehozásához:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Egy Event Hubs-névtér egyedi hatókörkezelési tárolót biztosít, amelyre a teljes tartománynevével lehet hivatkozni, és ebben a tárolóban egy vagy több eseményközpontot is létrehozhat. Egy névtér az erőforráscsoportban való létrehozásához futtassa az alábbi parancsot:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása
Egy eseményközpont létrehozásához futtassa az alábbi parancsot:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

Gratulálunk! Az Azure CLI segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy erőforráscsoportot, egy Event Hubs-névteret és egy eseményközpontot hozott létre. Az események eseményközpontból történő (vagy) fogadására vonatkozó részletes utasításokért tekintse meg az **Események küldése és fogadása** oktatóanyagokat: 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)

[hozzon létre egy ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
