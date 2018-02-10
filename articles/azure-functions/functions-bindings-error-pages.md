---
title: "Az Azure Functions hibakezelési útmutatást |} Microsoft Docs"
description: "Általános útmutatást a függvények futtatása során előforduló hibákat, és a kötés vonatkozó hibákat témakörökre mutató hivatkozásokat tartalmaz."
services: functions
cloud: 
documentationcenter: 
author: ggailey777
manager: cfowler
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-error-handling"></a>Az Azure Functions hibakezelés

Ez a témakör általános útmutatást a függvények futtatása közben fellépő hibák kezelése. A kötés-specifikus előforduló hibákat leíró témakörök hivatkozásokat is tartalmaz. 

## <a name="handing-errors-in-functions"></a>A funkciók kezelési hibák
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kötési hibakódok

Ha integrálja az Azure-szolgáltatásokkal, előfordulhat, hogy jelenik meg a hibákat, az API-kat az alapul szolgáló szolgáltatások származik. A hiba mutató hivatkozások code dokumentáció, ezeket a szolgáltatásokat is található az a **kivételeket és a visszatérési kódok** az alábbi eseményindító és kötés kapcsolatos témaköröket:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
