---
title: Azure Functions hibakezelés – útmutató
description: Megtudhatja, hogyan kezelheti a hibákat a Azure Functionsban, és meghatározott kötési hibákra mutató hivatkozásokat.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155284"
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

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
