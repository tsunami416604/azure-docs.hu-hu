---
title: Márkák modell testreszabása a Video Indexer API-val
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a Brands modellt a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127997"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Márkák modell testreszabása a Video Indexer API-val

A Video Indexer támogatja a beszéd- és vizuális szövegből származó márkaészlelést a video- és hangtartalmak indexelése és újraindexelése során. A márkaészlelési funkció azonosítja a Bing márkaadatbázisa által javasolt termékek, szolgáltatások és vállalatok említését. Ha például a Microsoftot megemlítik a video- vagy hangtartalomban, vagy ha vizuális szövegben jelenik meg egy videóban, a Video Indexer márkaként észleli azt a tartalomban. Az egyéni Márkák modell lehetővé teszi, hogy bizonyos márkákat kizárjon az észlelésből, és olyan márkákat vegyen fel, amelyek nem szerepelnek a modellben, és amelyek nem szerepelnek a Bing márkaadatbázisában.

A részletes áttekintést az Áttekintés című [témakörben találja.](customize-brands-model-overview.md)

A Videóindexelő API-k segítségével egyéni Márkák modelleket hozhat létre, használhat és szerkeszthet egy videóban, ahogy az ebben a témakörben is szerepel. A Video Indexer webhelyet is használhatja a [Márkák testreszabása modellben leírtak szerint a Video Indexer webhely használatával.](customize-brands-model-with-api.md)

## <a name="create-a-brand"></a>Márka létrehozása

A [márka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) létrehozása API létrehoz egy új egyéni márka, és hozzáadja azt az egyéni Márkák modell a megadott fiókhoz.

> [!NOTE]
> Ha `enabled` (a törzsben) igazra állítja, a márka szerepel a Video Indexer felderítéséhez listába. *Include* Ha `enabled` hamisra állítja a *márkát* a Kizárás listában, így a Video Indexer nem észleli azt.

Néhány más paraméterek, amelyek et a szervezetben beállíthatja:

* Az `referenceUrl` érték lehet bármilyen referencia weboldalak a márka, mint például egy linket a Wikipedia oldalon.
* Az `tags` érték a márka címkéinek listája. Ez a címke megjelenik a márka *Kategória* mezőjében a Video Indexer webhelyén. Például az "Azure" márka címkézhető vagy "Felhő" kategóriába sorolható.

### <a name="response"></a>Válasz

A válasz az alábbi példa formátumát követve létrehozott márkáról nyújt információt.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Márka törlése

A márka API [törlése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) eltávolítja a márkát a megadott fiók egyéni Brands modelljéből. A fiók meg van `accountId` adva a paraméterben. A sikeres elnevezés után a márka a továbbiakban nem szerepel a *Márkák belefoglalása* vagy *kizárása* listában.

### <a name="response"></a>Válasz

A márka sikeres törlésekor nincs visszaküldött tartalom.

## <a name="get-a-specific-brand"></a>Szerezzen be egy adott márkát

A [márka api beszerezése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) lehetővé teszi, hogy a márka azonosítójának használatával keresse meg egy márka adatait a megadott fiók egyéni Brands modelljében.

### <a name="response"></a>Válasz

A válasz információt nyújt a keresett márkáról (márkaazonosító használatával) az alábbi példa formátumát követve.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled`ha úgy `true` van beállítva, hogy a márka szerepel a Video `enabled` Indexer *alkalmazási* listájában, és hamis, ami azt jelzi, hogy a márka szerepel a *Kizárás* listában, így a Video Indexer nem észleli azt.

## <a name="update-a-specific-brand"></a>Adott márka frissítése

A [frissítés egy márka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) API lehetővé teszi, hogy keressen a részleteket a márka az egyéni Márkák modell a megadott fiók a márka azonosítóját.

### <a name="response"></a>Válasz

A válasz az alábbi példa formátumát követve frissített információkat tartalmaz a márkáról.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Szerezd meg az összes márka

A get all brands API visszaadja az összes márkát az egyéni Márkák modellben a megadott fiókhoz, függetlenül attól, hogy a márka a *Márkák belefoglalása* vagy *kizárása* listában szerepel.The [get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) API returns all the brands in the custom Brands model for the specified account regardless whether the brand is meant to be in the Include or Exclude brands list.

### <a name="response"></a>Válasz

A válasz tartalmazza a fiókjában található összes márka listáját és azok adatait az alábbi példa formátumát követve.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A *Példa* nevű márka szerepel a Video Indexer észleléséhez szükséges *Belefoglalás* listában, és a *Example2* nevű márka szerepel a *Kizárás* listában, így a Video Indexer nem észleli azt.

## <a name="get-brands-model-settings"></a>A Márkák modellbeállításainak beszereznie

A [get brands settings](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) API a Brands modell beállításait adja vissza a megadott fiókban. A Márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing márkák adatbázisából történő észlelés. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók egyéni Márkák modelljéből észleli a márkákat.

### <a name="response"></a>Válasz

A válasz azt mutatja, hogy a Bing márkák engedélyezve vannak-e az alábbi példa formátumában.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`igaz értékre van állítva, ami azt jelenti, hogy a Bing márkák engedélyezve vannak. Ha `useBuiltin` hamis, a Bing-márkák le vannak tiltva. Az `state` érték figyelmen kívül hagyható, mert elavult.

## <a name="update-brands-model-settings"></a>Márkák modellbeállításainak frissítése

A [frissítés i.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) A Márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing márkák adatbázisából történő észlelés. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók egyéni Márkák modelljéből észleli a márkákat.

A `useBuiltIn` jelző értéke igaz azt jelenti, hogy a Bing márkák engedélyezve vannak. Ha `useBuiltin` hamis, a Bing-márkák le vannak tiltva.

### <a name="response"></a>Válasz

Nincs visszaküldött tartalom, ha a Brands modell beállítás sikeresen frissül.

## <a name="next-steps"></a>További lépések

[Márkák modell testreszabása a webhely használatával](customize-brands-model-with-website.md)
