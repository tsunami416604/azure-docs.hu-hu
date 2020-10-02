---
title: Kötések hozzáadása egy meglévő függvényhez Azure Functions
description: Megtudhatja, hogyan adhat hozzá kötéseket egy meglévő függvényhez a Azure Functions projektben.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 23b7b05f9a9f9da5a48511ee555e3b6184a74179
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654123"
---
# <a name="add-bindings-to-an-existing-function-in-azure-functions"></a>Kötések hozzáadása egy meglévő függvényhez Azure Functions

Amikor létrehoz egy függvényt, a rendszer egy nyelvspecifikus-sablonból adja hozzá a projekthez egy nyelvfüggő aktiváló kódot. Ha a függvényt a bemeneti vagy kimeneti kötések használatával más szolgáltatásokhoz szeretné kapcsolni, a függvényben meghatározott kötési definíciókat kell hozzáadnia. További információ a kötésekről: [Azure functions eseményindítók és kötések fogalmai](functions-triggers-bindings.md).

## <a name="local-development"></a>Helyi fejlesztés       

Amikor helyileg fejleszti a függvényeket, frissítenie kell a függvény kódját a kötések hozzáadásához. A Visual Studio Code használatával egyszerűbbé teheti a kötések hozzáadását egy függvényhez.  

### <a name="visual-studio-code"></a>Visual Studio Code

Ha a Visual Studio Code használatával fejleszti a függvényt, és a függvény egy function.jst használ a fájlon, akkor a Azure Functions bővítmény automatikusan hozzáadhat egy kötést egy meglévő function.jsfájlhoz. További információt a [bemeneti és kimeneti kötések hozzáadása](functions-develop-vs-code.md#add-input-and-output-bindings)című témakörben talál.   

### <a name="manually-add-bindings-based-on-examples"></a>Kötések manuális hozzáadása példák alapján

Ha egy meglévő függvényhez kötést ad hozzá, frissítenie kell a függvény kódját és a function.jsa konfigurációs fájlon, ha azt a nyelv használja. A .NET-osztály könyvtára és a Java-függvények is a function.jshelyett attribútumokat használnak, így helyette frissítenie kell.

A következő táblázat segítségével megtalálhatja a meglévő függvények frissítéséhez használható speciális kötési típusokra vonatkozó példákat. Először válassza ki a projektnek megfelelő nyelv fület. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure Portal

Amikor a [Azure Portalban](https://portal.azure.com)fejleszti a függvényeket, a bemeneti és kimeneti kötéseket egy adott függvény **integrálás** lapján adhatja hozzá. Az új kötések az adott nyelvtől függően a fájl function.jsvagy a metódus attribútumaihoz lesznek hozzáadva. A következő cikkek példákat mutatnak be, hogyan adhat hozzá kötéseket egy meglévő függvényhez a portálon:

+ [Üzenetsor-tároló kimeneti kötése](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB kimeneti kötés](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Következő lépések

+ [Azure Functions-eseményindítók és -kötések – fogalmak](functions-triggers-bindings.md)
