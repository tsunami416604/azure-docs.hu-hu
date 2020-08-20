---
title: Lekérdezési egységek az Azure Digital Twinsban
titleSuffix: Azure Digital Twins
description: A lekérdezési egységek számlázási koncepciójának megismerése az Azure Digital Twinsban
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5334a1196ac8044c56e615cf8894b44646b48fb4
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88615108"
---
# <a name="query-units-in-azure-digital-twins"></a>Lekérdezési egységek az Azure Digital Twinsban 

Az Azure Digital Twins **lekérdezési egység (Qu)** egy igény szerinti számítási egység, amely az [Azure Digital Twins-lekérdezések](how-to-query-graph.md) futtatására szolgál a [lekérdezési API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)használatával. 

Elvonta a rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát, amely az Azure Digital Twins által támogatott lekérdezési műveletek elvégzéséhez szükséges, így a használat nyomon követhető a lekérdezési egységekben.

A lekérdezés bonyolultsága befolyásolja, hogy hány QUs használ a lekérdezés végrehajtásához. 

Ez a cikk a lekérdezési egységek értelmezését és a lekérdezési egység felhasználásának nyomon követését ismerteti.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>A lekérdezési egység felhasználásának megkeresése az Azure Digital Ikrekben 

Ha az Azure Digital Twins [lekérdezési API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)használatával futtat lekérdezést, a válasz fejlécében ellenőrizheti a lekérdezés által felhasznált QUs számát. Keresse meg a "lekérdezési díj" kifejezést az Azure digitális ikrektől visszaküldött válaszban. 

Az Azure Digital Twins [SDK](how-to-use-apis-sdks.md) -k lehetővé teszik a lekérdezési díj fejlécének kinyerését a lapozható válaszból. Ez a szakasz bemutatja, hogyan lehet lekérdezni a digitális ikreket, és hogyan lehet megismételni a lapozható választ a lekérdezési díj fejlécének kinyeréséhez. 

A következő kódrészlet azt mutatja be, hogyan lehet kibontani a lekérdezési API meghívásakor felmerülő lekérdezési díjakat. Első lépésként megismétli a lekérdezési fejlécet, majd megismétli a digitális dupla eredményeket az egyes lapokon belül. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure digitális Twins lekérdezéséről, látogasson el ide:
* [*Fogalmak: lekérdezési nyelv*](concepts-query-language.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)
* [A lekérdezési API-referenciák dokumentációja](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Az Azure digitális Twins lekérdezésekkel kapcsolatos korlátait a [*dokumentációban találja: a szolgáltatás korlátai nyilvános előzetes*](reference-service-limits.md)verzióban érhetők el.