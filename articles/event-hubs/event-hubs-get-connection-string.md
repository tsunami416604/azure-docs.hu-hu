---
title: Kapcsolati karakterlánc beletöltése – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk utasításokat tartalmaz egy kapcsolati karakterlánc beszerzéséhez, amelyet az ügyfelek az Azure Event Hubs-hoz való csatlakozáshoz használhatnak.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264933"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs kapcsolati karakterlánc ának beszereznie

Az Event Hubs használatához létre kell hoznia egy Eseményközpontok névterét. A névtér több eseményközpont vagy Kafka-témakör hatókör-tárolója. Ez a névtér egyedi [teljes tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)biztosít. A névtér létrehozása után beszerezheti az Event Hubs-szal való kommunikációhoz szükséges kapcsolati karakterláncot.

Az Azure Event Hubs kapcsolati karakterlánca a következő összetevőket ágyazja be,

* FQDN = a létrehozott EventHubs-névtér teljes tartományneve (tartalmazza az EventHubs névtér nevét, amelyet servicebus.windows.net követ)
* SharedAccessKeyName = az alkalmazás SAS-kulcsaihoz választott név
* SharedAccessKey = a kulcs generált értéke.

A kapcsolati karakterlánc sablon így néz ki:
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Egy példa kapcsolati karakterlánc így nézhet ki:`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Ez a cikk végigvezeti a kapcsolati karakterlánc megszerzésének különböző módjain.

## <a name="get-connection-string-from-the-portal"></a>Kapcsolati karakterlánc bekésezése a portálról
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. Válassza a bal oldali navigációs menü **Minden szolgáltatás lehetőséget.** 
3. Válassza **az Eseményközpontok lehetőséget** az **Analytics** szakaszban. 
4. Az eseményközpontok listájában válassza ki az eseményközpontot.
6. Az **Event Hubs Namespace (Eseményközpontok névtér) lapján** válassza a bal oldali menü **Megosztott hozzáférési házirendek parancsát.**

    ![Megosztott hozzáférési házirendek menüelem](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Válasszon egy **megosztott hozzáférési szabályzatot** a házirendek listájában. Az alapértelmezett neve: **RootManageSharedAccessPolicy**. Hozzáadhat egy megfelelő engedélyekkel rendelkező házirendet (olvasás, írás), és használhatja azt a házirendet. 

    ![Az Event Hubs megosztott hozzáférési házirendjei](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Jelölje ki a **Másolás** gombot a **Kapcsolatkarakterlánc-elsődleges kulcs** mező mellett. 

    ![Event Hubs - kapcsolati karakterlánc bekésezése](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>A kapcsolati karakterlánc beszerzése az Azure PowerShelllel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) segítségével lejuthat az adott házirend/szabály nevének kapcsolati karakterláncára az alábbiak szerint:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>A kapcsolati karakterlánc beszerzése az Azure CLI-vel
Az alábbiakban lejuthat a névtér kapcsolati karakterláncának lekérni:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Vagy használhatja a következőket egy EventHub-entitás kapcsolati karakterláncának lekérni:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Az Azure CLI-parancsok az Event Hubs szolgáltatáshoz című témakörben talál további információt [az Azure CLI for Event Hubs című témakörben.](/cli/azure/eventhubs)

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
