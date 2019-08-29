---
title: Azure Functions hiba a kezelési útmutatóban | Microsoft Docs
description: Általános útmutatást nyújt a függvények végrehajtásakor előforduló hibák kezeléséhez, valamint a kötési hibákra vonatkozó témakörökre mutató hivatkozásokat.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: fdfee3442986322f242da730bb9ceccbc9f9e250
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097487"
---
# <a name="azure-functions-error-handling"></a>Azure Functions hibakezelés

Ez a témakör általános útmutatást nyújt a függvények végrehajtásakor előforduló hibák kezelésére. Emellett az esetlegesen előforduló kötési hibákat leíró témakörökre mutató hivatkozásokat is tartalmaz. 

## <a name="handling-errors-in-functions"></a>Hibák feldolgozása a functions szolgáltatásban
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kötési hibakódok

Az Azure-szolgáltatásokkal való integráció során hibák merülhetnek fel, amelyek az alapul szolgáló szolgáltatások API-jai felől származnak. Az ezen szolgáltatások hibakód-dokumentációjának hivatkozásait a következő trigger-és kötési referenciákkal foglalkozó témakör **kivételek és visszatérési kódok** szakaszában találja:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Értesítési központ](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Szolgáltatásbusz](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
