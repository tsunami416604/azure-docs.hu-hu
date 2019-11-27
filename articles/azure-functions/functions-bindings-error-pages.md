---
title: Azure Functions hibakezelés – útmutató
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functionsban, és meghatározott kötési hibákra mutató hivatkozásokat.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 06dc4c5c1713ee10f263e573a698e9ea36ca2662
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227318"
---
# <a name="azure-functions-error-handling"></a>Azure Functions hibakezelés

A Azure Functions hibák elhárítása fontos az elveszett adat, a kihagyott események és az alkalmazás állapotának figyelése érdekében.

Ez a cikk a hibák kezelésére vonatkozó általános stratégiákat, valamint a kötésekre vonatkozó hibákra mutató hivatkozásokat ismerteti.

## <a name="handling-errors"></a>Kezelési hibák

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során a hibák a mögöttes szolgáltatások API-jai alapján származhatnak. A kötési hibákkal kapcsolatos információk a következő cikkek **kivételek és visszatérési kódok** szakaszában érhetők el:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Azure Event Hubs-eseményközpontok](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
