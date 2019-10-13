---
title: Media Services entitások szűrése, rendezése és lapozása – Azure | Microsoft Docs
description: Ez a cikk a Azure Media Services entitások szűrését, rendezését és lapozását ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298945"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services entitások szűrése, rendezése és lapozása

Ez a témakör a OData-lekérdezési lehetőségeket és a tördelési támogatást ismerteti Azure Media Services v3 entitások listázásakor.

## <a name="considerations"></a>Megfontolandó szempontok

* A DateTime típusú entitások tulajdonságai mindig UTC formátumban jelennek meg.
* A lekérdezési karakterláncban található üres területnek URL-kódolással kell rendelkeznie a kérelem elküldése előtt.

## <a name="comparison-operators"></a>Összehasonlító operátorok

A következő operátorok segítségével hasonlíthatja össze a mezőket egy állandó értékkel:

Esélyegyenlőségi operátorok:

- `eq`: annak tesztelése, hogy egy mező **egyenlő-** e egy konstans értékkel
- `ne`: annak tesztelése, hogy egy mező **nem egyenlő-** e állandó értékkel

Tartomány operátorai:

- `gt`: annak tesztelése, hogy egy mező nagyobb-e, **mint** egy konstans érték
- `lt`: annak tesztelése, hogy egy mező kisebb-e, **mint** egy konstans érték
- `ge`: annak tesztelése, hogy egy mező **nagyobb-e vagy egyenlő** -e állandó értékkel
- `le`: annak tesztelése, hogy egy mező értéke **kisebb vagy egyenlő** -e állandó értékkel

## <a name="filter"></a>Szűrő

**$Filter** – szűrő használatával adja meg a OData szűrő paramétert, hogy csak azokat az objektumokat találja meg, amelyekre kíváncsi.

A következő REST-példa szűrők egy eszköz alternateId:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

A következő C# példa az eszköz létrehozási dátumát szűri:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Rendezési sorrend

**$OrderBy** – ezzel a művelettel rendezheti a visszaadott objektumokat a megadott paraméter alapján. Példa:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Az eredmények növekvő vagy csökkenő sorrendbe rendezéséhez fűzze hozzá a `asc` vagy a `desc` értéket a mező nevéhez, szóközzel elválasztva. Például: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token kihagyása

**$skiptoken** – ha egy lekérdezés válasza sok elemet tartalmaz, a szolgáltatás visszaadja az eredmények következő oldalának beolvasásához használt kihagyó token (`@odata.nextLink`) értéket. Ez a teljes eredményhalmaz használatával végezhető el.

A Media Services V3 esetében nem konfigurálható az oldalméret. Az oldalméret az entitás típusától függően változik, kérjük, olvassa el a részleteket követő egyes szakaszokat.

Ha entitásokat hoz létre vagy töröl a gyűjteményen belüli lapozás során, a módosítások megjelennek a visszaadott eredményekben (ha ezek a módosítások a gyűjtemény azon részén találhatók, amely nem lett letöltve). 

> [!TIP]
> Mindig a `nextLink` értéket kell használnia a gyűjtemény számbavételéhez, és nem függ egy adott oldalméret méretétől.
>
> A `nextLink` csak akkor jelenik meg, ha az entitások egynél több lapja van.

Vegye figyelembe a következő példát, ahol a $skiptoken használatban van. Ügyeljen rá, hogy a *amstestaccount* cserélje le a fiók nevére, és állítsa be az *API-Version* értéket a legújabb verzióra.

Ha a következőhöz hasonló eszközök listáját kéri:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

A következőhöz hasonló választ kaphat:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Ezután a következő lapot kell kérnie a Get kérelem elküldésével:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Az alábbi C# példa bemutatja, hogyan lehet enumerálni a fiókban lévő összes streaming-lokátoron.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>A logikai operátorok használata a lekérdezési beállítások egyesítéséhez

A Media Services v3 támogatja a következőt: "vagy", "és" logikai operátorok. 

A következő REST-példa ellenőrzi a feladatok állapotát:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

A következőhöz C# hasonló lekérdezést kell létrehoznia: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Entitások szűrése és rendezési lehetőségei

A következő táblázat bemutatja, hogyan alkalmazhatók a szűrési és a rendezési beállítások a különböző entitásokra:

|Entitás neve|Tulajdonság neve|Szűrő|Rendelés|
|---|---|---|---|
|[Adategységek](https://docs.microsoft.com/rest/api/media/assets/)|név|`eq`, `gt`, `lt`, `ge`, `le`|`asc` és `desc`|
||Properties. alternateId |`eq`||
||Properties. assetId |`eq`||
||tulajdonságok. létrehozva| `eq`, `gt`, `lt`| `asc` és `desc`|
|[Tartalmi kulcs házirendjei](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. Leírás    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Properties. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||Properties. policyId|`eq`, `ne`||
|[Feladatok](https://docs.microsoft.com/rest/api/media/jobs)| név  | `eq`            | `asc` és `desc`|
||tulajdonságok. állapot        | `eq`, `ne`        |                         |
||tulajdonságok. létrehozva      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
||Properties. lastModified | `gt`, `ge`, `lt`, `le` | `asc` és `desc`| 
|[Folyamatos átviteli lokátorok](https://docs.microsoft.com/rest/api/media/streaminglocators)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. végső Befejezés    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Folyamatos átviteli házirendek](https://docs.microsoft.com/rest/api/media/streamingpolicies)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||tulajdonságok. létrehozva    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Átalakítja](https://docs.microsoft.com/rest/api/media/transforms)| név | `eq`            | `asc` és `desc`|
|| tulajdonságok. létrehozva      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
|| Properties. lastModified | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|

## <a name="next-steps"></a>Következő lépések

* [Eszközök listázása](https://docs.microsoft.com/rest/api/media/assets/list)
* [A tartalmi kulcs házirendjeinek listázása](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Feladatok listázása](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Folyamatos átviteli szabályzatok listázása](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Streaming-lokátorok listázása](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Kvóták és korlátozások](limits-quotas-constraints.md)
