---
title: Az Azure Functions kapcsolatok kezelése
description: Megtudhatja, hogyan statikus kapcsolat ügyfelek használatával az Azure Functions problémák elkerülése érdekében.
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
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655084"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Az Azure Functions kapcsolatok kezelése

Egy függvény alkalmazásban funkciók osztozik az erőforrásokon, és megosztott erőforrások közötti kapcsolatok &mdash; HTTP-kapcsolatok, a Helyadatbázis-kapcsolatot és a kapcsolatok, például a tárolás Azure-szolgáltatásokhoz való. Ha számos művelet fut. párhuzamosan is lehet elfogy a rendelkezésre álló kapcsolatok. Ez a cikk ismerteti, hogyan kerülheti el a további kapcsolatok ténylegesen van szükségük, mint a funkciók kód.

## <a name="connections-limit"></a>Kapcsolatok számának korlátozása

A rendelkezésre álló kapcsolatok száma korlátozva, részben, mert egy függvény alkalmazás fut a [Azure App Service védőfal](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). A védőfal ró a kód korlátozások egyike egy [a kapcsolatok száma, jelenleg 300 a maximális](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Ha eléri ezt a határt, a functions futtatókörnyezete létrehoz egy naplófájlt a következő üzenetet: `Host thresholds exceeded: Connections`.

A túllépő esélyét mikor növelje a [méretezési vezérlő hozzáadása függvény app példányok](functions-scale.md#how-the-consumption-plan-works). Minden egyes függvény app-példány is lehet meghívása funkciók sokszor egyszerre, és minden ezeket a funkciókat használ, amely növeli a 300 megnyitott száma kapcsolatok.

## <a name="use-static-clients"></a>Statikus ügyfelek használata

Rendelkezik a szükségesnél több kapcsolat elkerüléséhez használja fel újra minden függvény meghívása az új címkék létrehozása helyett ügyfél példányok. .NET-ügyfelekről, például a `HttpClient`, `DocumentClient`, és az Azure Storage ügyfelek kapcsolatok kezelése statikus ügyfél használatakor.

Az alábbiakban néhány irányelveket az Azure Functions alkalmazásban egy szolgáltatás-specifikus ügyfélfrissítés használata esetén kövesse:

- **NE** hozzon létre egy új ügyfél minden függvény hívása.
- **Tegye** hozzon létre statikus ügyfél, amely minden függvény meghívása a használatával.
- **Érdemes lehet** statikus ügyfelet hoz létre egy megosztott segítőosztály, ha a különböző funkciók használja ugyanazt a szolgáltatást.

## <a name="httpclient-code-example"></a>HttpClient Kódpélda

Íme egy példa a függvény kód létrehoz egy statikus `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

A .NET közös kérdése `HttpClient` "Kell I kell ártalmatlanítása az ügyfél?" Általában akkor eldobásakor megvalósító objektumok `IDisposable` befejezése használja őket. Egy statikus ügyfél nem eldobásakor, mivel nem történik, de amikor befejeződik a függvényt használja. Azt szeretné, hogy az alkalmazás időtartama élő statikus ügyfél.

## <a name="documentclient-code-example"></a>DocumentClient-példakód

`DocumentClient` a Cosmos DB példányhoz csatlakozik. A Cosmos DB dokumentáció azt javasolja, hogy Ön [egypéldányos Azure Cosmos DB-ügyfél használata az alkalmazás teljes](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). A következő példa bemutatja a függvényben történt, amely egy mintát.

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

További információ arról, hogy miért statikus ügyfelek számára javasoltak, lásd: [helytelen példánylétrehozás antipattern](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

További tudnivalók az Azure Functions teljesítmény, lásd: [a teljesítmény-és az Azure Functions megbízhatóság](functions-best-practices.md).