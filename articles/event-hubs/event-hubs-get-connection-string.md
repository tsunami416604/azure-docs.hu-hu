---
title: Kérje le a kapcsolati karakterláncot – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk ismerteti, amellyel az ügyfelek csatlakozni az Azure Event Hubs kapcsolati karakterlánc beolvasása.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264933"
---
# <a name="get-an-event-hubs-connection-string"></a>Az Event Hubs kapcsolati sztring lekérése

Event Hubs használata szeretne létrehozni az Event Hubs-névtér. A névtér egy több Event hub-vagy Kafka-témakör hatókör-tárolója. Ez a névtér egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)-t biztosít. Névtér létrehozása után szerezheti be a kapcsolati karakterláncot az Event Hubs folytatott kommunikációhoz szükséges.

Az Azure Event Hubs kapcsolati karakterláncára van beágyazva, a következő összetevők

* FQDN = a létrehozott EventHubs-névtér teljes tartományneve (tartalmazza a EventHubs-névtér nevét, majd a servicebus.windows.net-t)
* SharedAccessKeyName a SAS-kulcsok az alkalmazás a választott név =
* SharedAccessKey = a kulcs létrehozott értékét.

Néz ki a kapcsolati karakterlánc sablonjából
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Egy példaként szolgáló kapcsolatok karakterlánca a következőhöz hasonló lehet: `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Ez a cikk végigvezeti a kapcsolati karakterlánc beszerzése különféle módjait.

## <a name="get-connection-string-from-the-portal"></a>Kapcsolati sztring lekérése a portálról
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget. 
3. Az **elemzés** szakaszban válassza a **Event Hubs** lehetőséget. 
4. Az Event hubok listájában válassza ki az Event hub elemet.
6. A **Event Hubs névtér** lapon válassza a bal oldali menüben a **megosztott elérési házirendek** elemet.

    ![Közös hozzáférésű házirendek menüelem](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Válasszon ki egy **megosztott hozzáférési házirendet** a szabályzatok listájában. Az alapértelmezett név: **RootManageSharedAccessPolicy**. Hozzáadhat egy szabályzatot a megfelelő engedélyekkel (olvasás, írás), és használhatja a szabályzatot. 

    ![Event Hubs megosztott hozzáférési házirendek](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Válassza a **kapcsolódási karakterlánc – elsődleges kulcs** mező melletti **Másolás** gombot. 

    ![Event Hubs – a kapcsolatok karakterláncának beolvasása](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Bevezetés a kapcsolati karakterláncot az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) használatával lekérheti az adott házirend/szabály neve számára a kapcsolódási karakterláncot az alább látható módon:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Az Azure CLI-vel a kapcsolati karakterlánc beolvasása
A következő segítségével kérje le a kapcsolati karakterláncot a névtér:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Vagy az alábbi paranccsal kérheti le a EventHub entitáshoz tartozó kapcsolódási karakterláncot:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Az Event Hubs Azure CLI-parancsaival kapcsolatos további információkért lásd: [Az Azure CLI a Event Hubshoz](/cli/azure/eventhubs).

## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
