---
title: Az Azure Functions hibakezelési útmutató |} A Microsoft Docs
description: Nyújt általános útmutatást a függvények végrehajtása során előforduló hibákat és kötési vonatkozó hibákat témakörökre mutató hivatkozásokat.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091838"
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

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
