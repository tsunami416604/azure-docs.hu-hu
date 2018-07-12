---
title: Az Azure Functions kapcsolatok kezelése
description: Megtanulhatja, hogyan teljesítményproblémákat okozhat az Azure Functions ügyfelek statikus kapcsolat használatával.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 6c0af8f6f7e1d4aea8880a7af311aaa21f474f7e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969004"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Az Azure Functions kapcsolatok kezelése

A függvényalkalmazás függvénye erőforrások megosztása, és a megosztott erőforrások közé tartoznak a kapcsolatok &mdash; HTTP kapcsolat, adatbázis-kapcsolatok és az Azure-szolgáltatások például a tárolási kapcsolatok. Számos függvényt egy időben futnak esetén lehetséges, hogy elfogy a rendelkezésre álló kapcsolatok. Ez a cikk bemutatja, hogyan lehet, kerülje a további kapcsolatok ténylegesen szükségük van, mint a functions-kód.

## <a name="connections-limit"></a>Kapcsolatok számának korlátozása

A rendelkezésre álló kapcsolatok száma korlátozva, részben mivel a függvényalkalmazás a [Azure App Service tesztkörnyezetben](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). A védőfal ró a kód korlátozások egyike egy [cap-kapcsolatok esetén jelenleg 300 száma](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Ha eléri a korlátot, a functions futtatókörnyezete létrehoz egy naplófájlt a következő üzenettel: `Host thresholds exceeded: Connections`.

Szoftver-és meghaladja a korlátot növelni, mikor a [méretezési vezérlő hozzáad függvény alkalmazáspéldány](functions-scale.md#how-the-consumption-plan-works). Minden függvény alkalmazáspéldány is lehet meghívása funkciók sokszor egyszerre, és ezen funkciók mindegyikét a 300 korlát beleszámítanak kapcsolatokat használja.

## <a name="use-static-clients"></a>Statikus ügyfelek használata

Rendelkezés a szükségesnél több kapcsolatot elkerüléséhez ügyfélpéldányok helyett az egyes függvény meghívási újakat hozna létre újból. .NET-ügyfelek, például a `HttpClient`, `DocumentClient`, és az Azure Storage-ügyfelek kapcsolatok kezelése egyetlen, statikus ügyfél használatakor.

Az alábbiakban néhány irányelv szükséges a következő szolgáltatásspecifikus-ügyfél használatával egy Azure Functions-alkalmazásban:

- **NE** hozzon létre egy új ügyfél minden függvény meghívási együtt.
- **Tegye** hozzon létre egy statikus ügyfél, amely minden függvény meghívási által használható.
- **Érdemes lehet** statikus ügyfél létrehozása a megosztott segítőosztály, ha a különböző függvényeket használja ugyanazt a szolgáltatást.

## <a name="httpclient-code-example"></a>HttpClient példakód

Íme egy példa, amely létrehoz egy statikus függvénykód `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

A .NET kapcsolatos gyakori kérdések `HttpClient` "Kell I kell értékesítésére fejlesztek?" Általánosságban véve tud megszabadulni megvalósító objektumok `IDisposable` befejezése használja őket. A statikus ügyfél nem dobható el, mert nem kész, de amikor befejeződik a függvényt használja. Azt szeretné, hogy a statikus ügyfél élő az alkalmazás időtartamára.

## <a name="documentclient-code-example"></a>DocumentClient-példakód

`DocumentClient` a Cosmos DB-példányhoz csatlakozik. A Cosmos DB-dokumentációt azt javasolja, hogy Ön [egyedülálló Azure Cosmos DB-ügyfél használata az alkalmazás teljes élettartama](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). Az alábbi példa bemutatja egy függvény adatelemzésre, amely egy mintát.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client; 

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="next-steps"></a>További lépések

További információ arról, hogy miért statikus ügyfelek számára javasoltak, lásd: [nem megfelelő példányosítás kizárási minta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További Azure Functions teljesítményének tippek: [teljesítményének és megbízhatóságának az Azure Functions optimalizálása](functions-best-practices.md).