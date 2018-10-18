---
title: A streamelési Lokátorok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési Lokátorok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 1757ca84e7390f1ecd2d6d1e90a085372d3e4c57
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381185"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy az ügyfelek egy URL-cím is használható kódolt videó vagy hang lejátszásához, szeretne létrehozni egy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) és a streamelési URL-címeket. További információkért lásd: [fájl Stream](stream-files-dotnet-quickstart.md).

## <a name="streaminglocator-definition"></a>StreamingLocator definíciója

Az alábbi táblázat a StreamingLocator tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|id |sztring|Az erőforrás teljes erőforrás-azonosítója.|
|név   |sztring|Az erőforrás neve.|
|properties.alternativeMediaId  |sztring|A Streamelési Lokátorok alternatív Media azonosítója.|
|properties.assetName   |sztring|Objektum neve|
|properties.contentKeys |StreamingLocatorContentKey]|A Tartalomkulcsok használják a Streamelési lokátor.|
|Properties.created |sztring|A Streamelési lokátor létrehozásának idejét.|
|properties.defaultContentKeyPolicyName |sztring|Az alapértelmezett a Streamelési lokátor által használt ContentKeyPolicy neve.|
|properties.endTime |sztring|A befejezési ideje a Streamelési lokátor.|
|properties.startTime   |sztring|A Streamelési lokátor kezdési idejét.|
|properties.streamingLocatorId  |sztring|A StreamingLocatorId, a Streamelési Lokátort.|
|properties.streamingPolicyName |sztring|A Streamelési lokátor által használt adatfolyam-házirend nevét. Adja meg a létrehozott Streamelési szabályzat nevét, vagy használja az előre definiált adatfolyam-házirendek egyikét. Vannak elérhető előre definiált adatfolyam-szabályzatok: 'Predefined_DownloadOnly', "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" és "Predefined_ MultiDrmStreaming "|
|type   |sztring|Az erőforrás típusát.|

A teljes meghatározása: [Streamelési Lokátorok](https://docs.microsoft.com/rest/api/media/streaminglocators).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

A Media Services a következő OData lekérdezési beállítások Streamelési Lokátorok támogatja: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Operátor leírása:

* EQ = egyenlő
* Ú = nem egyenlő
* A GE = nagyobb vagy egyenlő
* Le = kisebb vagy egyenlő
* Gt = nagyobb, mint
* Lt = kisebb, mint

### <a name="filteringordering"></a>Szűrés és rendezés

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat a StreamingLocator tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id |||
|név|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. 

Ha StreamingLocators létrehozott vagy a gyűjtemény átlapozva közben, a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részén.) 

Az alábbi C#-példa bemutatja a fiókban lévő összes StreamingLocators számba.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [Streamelési Lokátorok - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/streaminglocators_list)

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
