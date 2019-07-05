---
title: Az Azure Functions hibakezelési útmutató |} A Microsoft Docs
description: Nyújt általános útmutatást a függvények végrehajtása során előforduló hibákat és kötési vonatkozó hibákat témakörökre mutató hivatkozásokat.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: ef8f2d5a63f7924097362f6aa0ebc78cc0f6455f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480711"
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
