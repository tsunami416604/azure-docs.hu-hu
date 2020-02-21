---
title: Azure Functions hibakezelés – útmutató
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functionsban, és meghatározott kötési hibákra mutató hivatkozásokat.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 0da8322a57d925608f7b52054c7a52e6cb3e5d06
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484655"
---
# <a name="azure-functions-error-handling"></a>Azure Functions hibakezelés

A Azure Functions hibák elhárítása fontos az elveszett adat, a kihagyott események és az alkalmazás állapotának figyelése érdekében.

Ez a cikk a hibák kezelésére vonatkozó általános stratégiákat, valamint a kötésekre vonatkozó hibákra mutató hivatkozásokat ismerteti.

## <a name="handling-errors"></a>Kezelési hibák

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során a hibák a mögöttes szolgáltatások API-jai alapján származhatnak. A kötési hibákkal kapcsolatos információk a következő cikkek **kivételek és visszatérési kódok** szakaszában érhetők el:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
