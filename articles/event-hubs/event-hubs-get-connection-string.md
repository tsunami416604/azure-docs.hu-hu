---
title: Kérje le a kapcsolati karakterláncot – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk ismerteti, amellyel az ügyfelek csatlakozni az Azure Event Hubs kapcsolati karakterlánc beolvasása.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 263dba970b015d466b3fd46f390a063e87de2e2c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093315"
---
# <a name="get-an-event-hubs-connection-string"></a>Az Event Hubs kapcsolati sztring lekérése

Event Hubs használata szeretne létrehozni az Event Hubs-névtér. A névtér egy hatókörkezelési tároló, amely több Event hubs szolgáltatást is tartalmazhat az / Kafka-témaköreit. Ezt a névteret biztosít egy egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Névtér létrehozása után szerezheti be a kapcsolati karakterláncot az Event Hubs folytatott kommunikációhoz szükséges.

Az Azure Event Hubs kapcsolati karakterláncára van beágyazva, a következő összetevők

* FQDN = létrehozott EventHubs névtér teljes Tartományneve (Ez tartalmazza a EventHubs névtér neve követ servicebus.windows.net)
* SharedAccessKeyName a SAS-kulcsok az alkalmazás a választott név =
* SharedAccessKey = a kulcs létrehozott értékét.

Néz ki a kapcsolati karakterlánc sablonjából
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Egy példát a kapcsolati karakterláncra hasonló lehet `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Ez a cikk végigvezeti a kapcsolati karakterlánc beszerzése különféle módjait.

## <a name="get-connection-string-from-the-portal"></a>Kapcsolati sztring lekérése a portálról

Miután az Event Hubs-névtér, az Áttekintés szakaszban a portál adhat meg a kapcsolati karakterlánc alább látható módon:

![Event Hubs kapcsolati karakterlánc](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Amikor az Áttekintés szakaszban, a kapcsolati karakterlánc hivatkozásra kattint, megnyílik a SAS-szabályzatok lapon az alábbi ábrán látható módon:

![Eseményközpont SAS-szabályzatok](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Adjon hozzá egy új SAS-szabályzatot, és kérje le a kapcsolati karakterláncot, vagy használja az alapértelmezett házirendet, az Ön számára létrehozott. A szabályzat megnyitásakor, a kapcsolati karakterlánc kapjuk meg, ahogyan az alábbi ábrát:

![Az Event Hubs kapcsolati sztring lekérése](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Bevezetés a kapcsolati karakterláncot az Azure PowerShell használatával
A Get-AzureRmEventHubNamespaceKey segítségével a kapcsolati sztring lekérése a adja meg a házirend/szabály neve alább látható módon:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Tekintse meg [Azure Event Hubs PowerShell-modul](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) további részletekért.

## <a name="getting-the-connection-string-with-azure-cli"></a>Az Azure CLI-vel a kapcsolati karakterlánc beolvasása
A következő segítségével kérje le a kapcsolati karakterláncot a névtér:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Tekintse meg [Event hubs Azure CLI-vel](https://docs.microsoft.com/cli/azure/eventhubs) további.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
