---
title: A tartalom az Azure Media Services kulcs házirendjei |} A Microsoft Docs
description: Ez a cikk annak magyarázatát, Mik azok a Tartalomszabályzat kulcsot, és hogyan használják az Azure Media Services biztosítja.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 9a5ef8df9b1ca87430fb5e8d1da94f1899c4a856
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985862"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek dinamikusan titkosított juttathat el: a Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.

Az Azure Media Services v3 tartalom kulcs házirendjei lehetővé teszik annak meghatározását a tartalomkulcsot a rendszer hogyan továbbítja a Media Services kulcs kézbesítési összetevő-n keresztül az ügyfelek közötti. További információkért lásd: [Content protection áttekintése](content-protection-overview.md).

## <a name="contentkeypolicies-definition"></a>ContentKeyPolicies definíciója

Az alábbi táblázat a ContentKeyPolicy tulajdonságait jeleníti meg, és lehetővé teszi a definíciójukat.

|Name (Név)|Típus|Leírás|
|---|---|---|
|id|sztring|Az erőforrás teljes erőforrás-azonosítója.|
|név|sztring|Az erőforrás neve.|
|Properties.created |sztring|A szabályzat létrehozásának dátuma|
|properties.description |sztring|A házirend leírását.|
|properties.lastModified    |sztring|A házirend utolsó módosítás dátuma|
|Properties.Options |ContentKeyPolicyOption]|A kulcs házirend-beállításokban.|
|properties.policyId    |sztring|Az örökölt szabályzat azonosítója.|
|type   |sztring|Az erőforrás típusát.|

A teljes meghatározása: [Tartalomszabályzat kulcs](https://docs.microsoft.com/rest/api/media/contentkeypolicies).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

A Media Services a következő OData lekérdezési beállítások ContentKeyPolicies támogatja: 

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
|Properties.created |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|properties.description |Eq, ne, a ge, le, gt, lt||
|properties.lastModified    |Eq, ne, a ge, le, gt, lt|Növekvő vagy csökkenő sorrendben|
|Properties.Options |||
|properties.policyId    |Eq, ne||
|type   |||

### <a name="pagination"></a>Tördelés

Tördelés a négy engedélyezve van a rendezési sorrend mindegyike támogatott. Az oldalméret jelenleg 10.

> [!TIP]
> A gyűjtemény enumerálása, és nem függ egy adott oldal méretét a következő hivatkozás mindig használjon.

A lekérdezési válasz számos elemet tartalmaz, ha a szolgáltatás visszaadja egy "\@odata.nextLink" tulajdonságát a következő lapra az eredmények lekérése. Ez használható a lapozza végig a teljes eredményhalmaz. Az oldal méretét nem lehet konfigurálni. 

Ha StreamingPolicy létrehozott vagy a gyűjtemény átlapozva közben, a módosítások megjelennek a kapott találatok közül (ha ezek a módosítások a gyűjteményt, amely még nincs letöltve részén.) 

Az alábbi C#-példa bemutatja a fiókban lévő összes ContentKeyPolicies számba.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

További példák: [tartalom kulcs házirendjei – lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

## <a name="next-steps"></a>További lépések

[AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)

[DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
