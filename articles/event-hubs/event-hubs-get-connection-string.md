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
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445100"
---
# <a name="get-an-event-hubs-connection-string"></a>Az Event Hubs kapcsolati sztring lekérése

Event Hubs használata szeretne létrehozni az Event Hubs-névtér. A névtér egy hatókörkezelési tároló az event hubs vagy a Kafka-témaköröket a. Ezt a névteret biztosít egy egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Névtér létrehozása után szerezheti be a kapcsolati karakterláncot az Event Hubs folytatott kommunikációhoz szükséges.

Az Azure Event Hubs kapcsolati karakterláncára van beágyazva, a következő összetevők

* FQDN = létrehozott EventHubs névtér teljes Tartományneve (tartalmazza a EventHubs névtér neve követ servicebus.windows.net)
* SharedAccessKeyName a SAS-kulcsok az alkalmazás a választott név =
* SharedAccessKey = a kulcs létrehozott értékét.

Néz ki a kapcsolati karakterlánc sablonjából
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Egy példát a kapcsolati karakterláncra hasonló lehet `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Ez a cikk végigvezeti a kapcsolati karakterlánc beszerzése különféle módjait.

## <a name="get-connection-string-from-the-portal"></a>Kapcsolati sztring lekérése a portálról
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. Válassza ki **minden szolgáltatás** a bal oldali navigációs menüben. 
3. Válassza ki **az Event Hubs** a a **Analytics** szakaszban. 
4. Az eseményközpontok listájában válassza ki az event hub.
6. Az a **Event Hubs-Namespace** lapon jelölje be **megosztott hozzáférési házirendek** a bal oldali menüben.

    ![Megosztott hozzáférési házirendek menüpont](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Válassza ki a **megosztott hozzáférési szabályzat** szabályzatok listájában. Az alapértelmezett egyik neve: **RootManageSharedAccessPolicy**. Adjon hozzá egy házirendet a megfelelő jogosultságokkal (olvasási, írási), és használja az adott házirendnek. 

    ![Az Event Hubs megosztott hozzáférési házirendek](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Válassza ki a **másolási** megjelenítő gombra a **kapcsolati karakterlánc – elsődleges kulcs** mező. 

    ![Az Event Hubs - kapcsolati sztring lekérése](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Bevezetés a kapcsolati karakterláncot az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Használhatja a [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) az alább látható módon a kapcsolati sztring lekérése az adott szabályzat/szabály neve:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Az Azure CLI-vel a kapcsolati karakterlánc beolvasása
A következő segítségével kérje le a kapcsolati karakterláncot a névtér:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Az Event Hubs Azure CLI-parancsokkal kapcsolatos további információkért lásd: [Event hubs Azure CLI-vel](/cli/azure/eventhubs).

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
