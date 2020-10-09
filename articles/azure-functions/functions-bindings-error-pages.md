---
title: Azure Functions hibakezelés – útmutató
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functionsban, és meghatározott kötési hibákra mutató hivatkozásokat.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586189"
---
# <a name="azure-functions-error-handling"></a>Azure Functions hibakezelés

A Azure Functions hibák elhárítása fontos az elveszett adat, a kihagyott események és az alkalmazás állapotának figyelése érdekében.

Ez a cikk a hibák kezelésére vonatkozó általános stratégiákat, valamint a kötésekre vonatkozó hibákra mutató hivatkozásokat ismerteti.

## <a name="handling-errors"></a>Hibák kezelése

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során a hibák a mögöttes szolgáltatások API-jai alapján származhatnak. A kötési hibákkal kapcsolatos információk a következő cikkek **kivételek és visszatérési kódok** szakaszában érhetők el:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT hubok](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
