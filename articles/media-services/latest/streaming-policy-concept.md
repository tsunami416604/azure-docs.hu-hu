---
title: Házirendek az Azure Media Services Streaming |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési szabályzatok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/22/2018
ms.author: juliako
ms.openlocfilehash: d74ce913a2189dd1062b30f9def919cbbabe7b64
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742524"
---
# <a name="streaming-policies"></a>Streamelési szabályok

Az Azure Media Services v3, adatfolyam-szabályzatok lehetővé teszik adatfolyam-továbbítási protokollok és titkosítási beállításokat a StreamingLocators meghatározásához. Adja meg a létrehozott Streamelési szabályzat nevét, vagy használja az előre definiált adatfolyam-házirendek egyikét. Jelenleg elérhető előre definiált adatfolyam-szabályzatok a következők: 'Predefined_DownloadOnly', "Predefined_ClearStreamingOnly", "Predefined_DownloadAndClearStreaming", "Predefined_ClearKey", "Predefined_MultiDrmCencStreaming" és "Predefined_MultiDrmStreaming".

> [!IMPORTANT]
> Egyéni használatakor [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú házirendeket tervezzen a Media Services-fiók, és újra alkalmazza őket a Streamelési Lokátorok, amikor az ugyanazon titkosítási lehetőségeket és a protokollok van szükség. A Media szolgáltatás fiókja rendelkezik Streamelési Hozzáférésiszabályzat-bejegyzések száma tartozó kvótát. Meg kell nem lehet új szabályzatot hoz létre Streamelési az egyes Streamelési lokátor.

## <a name="streamingpolicy-definition"></a>StreamingPolicy definíciója

Az alábbi táblázat a StreamingPolicy tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Leírás|
|---|---|
|id|Az erőforrás teljes erőforrás-azonosítója.|
|név|Az erőforrás neve.|
|properties.commonEncryptionCbcs|CommonEncryptionCbcs konfigurációja|
|properties.commonEncryptionCenc|CommonEncryptionCenc konfigurációja|
|Properties.created |Adatfolyam-házirend létrehozásának idejét|
|properties.defaultContentKeyPolicyName |Aktuális Streamelési házirend által használt alapértelmezett ContentKey|
|properties.envelopeEncryption  |EnvelopeEncryption konfigurációja|
|properties.noEncryption|NoEncryption konfigurációi|
|type|Az erőforrás típusát.|

A teljes meghatározása: [szabályzatokat. adatfolyam](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

A Media Services adatfolyam-szabályzatok a következő OData lekérdezési beállítások támogatja: 

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

Az alábbi táblázat bemutatja, hogyan lehet alkalmazni ezeket a beállításokat a StreamingPolicy tulajdonságai: 

|Name (Név)|Szűrés|Rendelés|
|---|---|---|
|id|||
|név|Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. 

Ha StreamingPolicy létrehozott vagy a gyűjtemény átlapozva közben, a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részén.) 

Az alábbi C#-példa bemutatja a fiókban lévő összes StreamingPolicies számba.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [Streamelési házirendek – lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)

## <a name="next-steps"></a>További lépések

[Fájl streamelése](stream-files-dotnet-quickstart.md)
