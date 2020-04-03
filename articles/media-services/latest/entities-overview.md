---
title: Media Services-entitások szűrése, rendezése és lapozása
titleSuffix: Azure Media Services
description: Ismerje meg az Azure Media Services v3-as entitásainak szűrését, rendelését és lapozását.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7e4f1141a9d4bd58451782e8412063a22565556d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584534"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Media Services-entitások szűrése, rendezése és lapozása

Ez a témakör az Azure Media Services v3-as entitások listázásakor elérhető OData-lekérdezési beállításokat és tördelési támogatást ismerteti.

## <a name="considerations"></a>Megfontolandó szempontok

* A `Datetime` típushoz tartozó entitások tulajdonságai mindig UTC formátumban vannak.
* A lekérdezési karakterlánc ban lévő szóközt a kérelem elküldése előtt URL-kódolással kell elküldeni.

## <a name="comparison-operators"></a>Összehasonlító operátorok

A következő operátorok segítségével hasonlíthatja össze a mezőt egy állandó értékkel:

Esélyegyenlőségi szereplők:

- `eq`: Ellenőrizze, hogy egy mező *egyenlő-e* állandó értékkel.
- `ne`: Ellenőrizze, hogy egy mező *nem egyenlő-e* állandó értékkel.

Tartomány operátorok:

- `gt`: Ellenőrizze, hogy egy mező nagyobb-e állandó *értéknél.*
- `lt`: Ellenőrizze, hogy egy mező kisebb-e állandó *értéknél.*
- `ge`: Ellenőrizze, hogy egy mező nagyobb vagy egyenlő-e állandó *értékkel.*
- `le`: Ellenőrizze, hogy egy mező *kisebb vagy egyenlő-e állandó értékkel.*

## <a name="filter"></a>Szűrés

Az `$filter` OData-szűrő paraméter megadására csak az Önt érdeklő objektumokat keresheti meg.

A következő REST példa `alternateId` egy eszköz értékét szűri ki:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

A következő C# példa szűri az eszköz létrehozásának dátumát:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Rendelés

A `$orderby` visszaadott objektumokat a megadott paraméter szerint rendezheti. Példa:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Ha az eredményeket növekvő vagy csökkenő sorrendbe `asc` `desc` szeretné rendezni, fűzz hozzá vagy a mezőnévhez, szóközvel elválasztva. Például: `$orderby properties/created desc`.

## <a name="skip-token"></a>Token kihagyása

Ha egy lekérdezési válasz sok elemet `$skiptoken` `@odata.nextLink`tartalmaz, a szolgáltatás egy ( ) értéket ad vissza, amelyet az eredmények következő oldalának lejáratához használ. Használja a teljes eredményhalmaz oldallapját.

A Media Services 3-as v3-as ében nem konfigurálható az oldalméret. Az oldalméret az entitás típusától függően változik. A részletekért olvassa el az alábbi szakaszokat.

Ha az entitások a gyűjteményen keresztül történő lapozás közben jönnek létre vagy törlődnek, a módosítások megjelennek a visszaadott eredményekben (ha ezek a módosítások a gyűjtemény azon része, amelyet még nem töltöttek le).

> [!TIP]
> Mindig `nextLink` a gyűjtemény számbavételére használja, és ne függjen egy adott oldalmérettől.
>
> Az `nextLink` érték csak akkor jelenik meg, ha egynél több entitásoldal van.

Vegye figyelembe a `$skiptoken` következő példát a használt helyszínre. Győződjön meg róla, hogy lecserélte *az amstestaccount-ot* a fiók nevére, és állítsa be az *api-verzió* értékét a legújabb verzióra.

Ha az ehhez hasonló eszközöket kér:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Kapsz vissza a választ hasonló ez:

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

Ezután a következő oldalt a következő bekéréses kérelem elküldésével kérheti:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

A következő C# példa bemutatja, hogyan kell számba venni a fiók összes streamelési lokátorán keresztül.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Logikai operátorok használata lekérdezési beállítások kombinálása

A Media Services v3 támogatja a **OR** **és az AND** logikai operátorokat. 

A következő REST-példa ellenőrzi a feladat állapotát:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Ugyanazt a lekérdezést a C# ciklusban a következőkhez hasonlóan hozhatja létre: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Entitások szűrési és rendezési lehetőségei

Az alábbi táblázat bemutatja, hogyan alkalmazhatja a szűrési és rendezési beállításokat a különböző entitásokra:

|Entitás neve|Tulajdonság neve|Szűrés|Rendelés|
|---|---|---|---|
|[Objektumok](https://docs.microsoft.com/rest/api/media/assets/)|név|`eq`, `gt`, `lt`, `ge`, `le`|`asc` és `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` és `desc`|
|[Tartalomkulcs-házirendek](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||properties.description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastMódosítva|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||properties.policyId|`eq`, `ne`||
|[Feladatok](https://docs.microsoft.com/rest/api/media/jobs)| név  | `eq`            | `asc` és `desc`|
||tulajdonságok.állapot        | `eq`, `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
||properties.lastMódosítva | `gt`, `ge`, `lt`, `le` | `asc` és `desc`| 
|[Streamelési lokátorok](https://docs.microsoft.com/rest/api/media/streaminglocators)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` és `desc`|
||properties.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Streamelési házirendek](https://docs.microsoft.com/rest/api/media/streamingpolicies)|név|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
||properties.created    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` és `desc`|
|[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms)| név | `eq`            | `asc` és `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|
|| properties.lastMódosítva | `gt`, `ge`, `lt`, `le`| `asc` és `desc`|

## <a name="next-steps"></a>További lépések

* [Eszközök listázása](https://docs.microsoft.com/rest/api/media/assets/list)
* [Tartalomkulcs-házirendek listázása](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Feladatok listázása](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Streamelési házirendek listázása](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Streamelési lokátorok listázása](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Kvóták és korlátozások](limits-quotas-constraints.md)
