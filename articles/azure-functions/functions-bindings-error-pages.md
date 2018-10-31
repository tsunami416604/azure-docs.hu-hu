---
title: Az Azure Functions hibakezelési útmutató |} A Microsoft Docs
description: Nyújt általános útmutatást a függvények végrehajtása során előforduló hibákat és kötési vonatkozó hibákat témakörökre mutató hivatkozásokat.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bf54d312de5625a7fa44cea4d5107e83cf15583c
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248251"
---
# <a name="azure-functions-error-handling"></a>Az Azure Functions hibakezelés

Ez a témakör általános útmutatást a függvények végrehajtása közben fellépő hibák kezeléséhez nyújt. A kötés-specifikus előforduló hibákat leíró témakörök hivatkozásokat is tartalmaz. 

## <a name="handing-errors-in-functions"></a>A függvények hibák kezelése
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kötés hibakódok

Amikor integrálják az Azure-szolgáltatásokkal, előfordulhat, hogy meg, a hibákat, az API-k az alapul szolgáló szolgáltatások származik. Hivatkozások a hibás code dokumentáció esetében ezek a szolgáltatások megtalálhatók a **kivételeket és a visszatérési kódok** a következő eseményindító-és kötési referencia-témaköreit:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
