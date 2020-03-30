---
title: Személymodell testreszabása a Video Indexer API-val
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a személymodellt a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127893"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Személymodell testreszabása a Video Indexer API-val

A Video Indexer támogatja a videotartalmak arcfelismerését és híresség-felismerését. A híresség elismerés vonás borít körülbelül egy millió szembenéz azon alapszik általában igényelt adatforrás mint IMDB, Wikipedia, és tető Összekapcsolt influencers. A rendszer olyan arcokat észlel, amelyeket a hírességek felismerése nem ismer fel, de névtelenül hagy. Miután feltöltötted a videódat a Video Indexerbe, és visszaszerezed az eredményeket, visszamehetsz, és elnevezheted a nem felismert arcokat. Miután megcímkézett egy arcot egy névvel, az arc és a név hozzáadódik a fiók személymodelljéhez. A Video Indexer ezután felismeri ezt az arcot a jövőbeli és a korábbi videókban.

A Video Indexer API-val szerkesztheti a videóban észlelt arcokat, a jelen témakörben leírtak szerint. A Video Indexer webhelyet is használhatja a [Személy testreszabása modellben leírtak szerint a Video Indexer webhely használatával.](customize-person-model-with-api.md)

## <a name="managing-multiple-person-models"></a>Több személy modell kezelése

A Video Indexer fiókonként több személymodellt támogat. Ez a funkció jelenleg csak a Video Indexer API-kon keresztül érhető el.

Ha a fiók különböző használati eseteket lát el, fiókonként több személymodellt is létrehozhat. Ha például a tartalom sporthoz kapcsolódik, akkor minden sportághoz (labdarúgás, kosárlabda, foci és így tovább) külön személymodellt hozhat létre.

A modell létrehozása után használhatja azt egy adott személy modell modellazonosítójának megadásával egy videó feltöltésekor/indexelésekor vagy újraindexelésekor. A videó új arcának betanítása frissíti azt az egyéni modellt, amelyhez a videó társítva volt.

Minden fióklegfeljebb 50 személy modell. Ha nincs szükséged a többszemélyes modell támogatására, ne rendelj személymodell-azonosítót a videódhoz feltöltés/indexelés vagy újraindexelés közben. Ebben az esetben a Video Indexer az alapértelmezett egyéni személy modellt használja a fiókjában.

## <a name="create-a-new-person-model"></a>Új személymodell létrehozása

Ha új személymodellt szeretne létrehozni a megadott fiókban, használja a [személymodell létrehozása](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) API-t.

A válasz az alábbi példa formátumát követve létrehozott Személy modell nevét és generált modellazonosítóját tartalmazza.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Ezután a **personModelId** paraméter **azonosítóértékét** használja, amikor videót tölt fel a videó [indexelésére](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [újraindexelésére.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)

## <a name="delete-a-person-model"></a>Személymodell törlése

Egyéni személy modell törlése a megadott fiókból, használja a [személy törlése modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) API-t.

A Személy modell sikeres törlése után a törölt modellt használó aktuális videók indexe mindaddig változatlan marad, amíg újra nem indexeli őket. Az újraindexeléskor a törölt modellben elnevezett arcokat a Video Indexer nem ismeri fel az aktuális videókban, amelyeket az adott modell használatával indexeltek, de az arcok továbbra is észlelni fogják őket. A törölt modell használatával indexelt aktuális videók mostantól a fiók alapértelmezett Személy modelljét fogják használni. Ha a törölt modell ből származó arcok is szerepelnek a fiók alapértelmezett modelljében, akkor ezeket az arcokat továbbra is felismeri a rendszer a videókban.

Nincs visszaadott tartalom, ha a Személy modell sikeresen törlődik.

## <a name="get-all-person-models"></a>Az összes Személy modell beszereznie

A megadott fiókban az összes person modell lehívásához használja a személymodell API [beszerzése.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

A válasz tartalmazza a fiókjában lévő összes Személy modell (beleértve az alapértelmezett személy modellt a megadott fiókban), valamint az egyes nevük és azonosítóik listáját az alábbi példa formátumát követve.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

A videó `id` [indexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [újraindexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)használt személy modell `personModelId` értékével választhatja ki, hogy melyik modellt szeretné használni.

## <a name="update-a-face"></a>Arc frissítése

Ez a parancs lehetővé teszi, hogy frissítse az arc a videó egy nevet az azonosítót a videó és az azonosító az arc. Ez a művelet ezután frissíti azt a személymodellt, amelyhez a videó társítva volt a feltöltés/indexelés vagy újraindexelés során. Ha nincs személymodell hozzárendelése, frissíti a fiók alapértelmezett személy modelljét.

A rendszer ezután felismeri az azonos arc előfordulásait a többi aktuális videóban, amelyek ugyanazt a Személy modellt használják. Az arc felismerése a többi aktuális videóban időbe telhet, amíg érvénybe lép, mivel ez egy kötegelt folyamat.

Frissítheti azt az arcot, amelyet a Video Indexer új néven ismer fel hírességként. Az új nevet, hogy adsz elsőbbséget élveznek a beépített híresség elismerést.

Az arc frissítéséhez használja a [frissítés egy videoarc](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API.To update the face, use the update a video face API.

A nevek egyediek a Személy modellekhez, így ha két `name` különböző arcot adsz ugyanabban a Személy modellben ugyanazt a paraméterértéket, a Video Indexer ugyanannak a személynek tekinti meg az arcokat, és a videó újraindexelése után konvergálja őket.

## <a name="next-steps"></a>További lépések

[Személymodell testreszabása a Video Indexer webhely használatával](customize-person-model-with-website.md)
