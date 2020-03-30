---
title: Az Azure Functions hibakezelési útmutatója
description: Ismerje meg az Azure Functions hibáinak kezelését adott kötési hibákra mutató hivatkozásokkal.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586189"
---
# <a name="azure-functions-error-handling"></a>Az Azure Functions hibakezelése

Az Azure Functions hibáinak kezelése fontos az elveszett adatok, a kihagyott események elkerülése és az alkalmazás állapotának figyelése érdekében.

Ez a cikk a hibakezelés általános stratégiáit, valamint a kötésspecifikus hibákra mutató hivatkozásokat ismerteti.

## <a name="handling-errors"></a>Hibák kezelése

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során hibák származhatnak az alapul szolgáló szolgáltatások API-iból. A kötelező érvényű hibákra vonatkozó információk a következő cikkek **Kivételek és visszaküldési kódok** szakaszában érhetők el:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob-tároló](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Eseményközpontok](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT-központok](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Várólista-tárolás](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
