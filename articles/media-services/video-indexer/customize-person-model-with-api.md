---
title: A Video Indexer API használatával egy személy modellje – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a Video Indexer API személy modell.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/10/2018
ms.author: anzaman
ms.openlocfilehash: 2b035243e577adbbefa866acbae6243a9e7e9a31
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285073"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>A Video Indexer API személy modell testreszabása

A video Indexer videótartalmak támogatja az arcfelismerés és hírességek felismerése. A hírességek felismerése funkció körülbelül 1 millió arcok IMDB Wikipedia és felső LinkedIn véleményvezérek például gyakran lekérdezett adatforrás alapján ismerteti. Észlelt, amely nem ismeri fel a híresség-felismerés szolgáltatás; azonban van hátra névtelen. Miután, videó feltöltése a Video Indexer, és vissza eredményt, lépjen vissza, és nevezze el az arcok, amely nem ismerhetők fel. Miután Ön címke egy ARC nevére, a fiókhoz tartozó személy modell hozzáadja az arcfelismerés és nevét. Video Indexer majd fogja felismerni a face a jövőbeli videók és a múltbeli videókat.

A Video Indexer API segítségével, amely az észlelt a videó szerkesztése ebben a témakörben leírtak szerint. Használhatja a Video Indexer webhelyen leírtak szerint [testreszabása személy modell a Video Indexer webhelyen](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Több személy modellek kezelése 

A video Indexer-fiókonként több személy modell támogatja. Ez a funkció jelenleg csak a Video Indexer API-kat.

Ha a fiók caters a különböző használati esetek, érdemes fiókonként több személy modellek létrehozásának eljárásait. Például ha a tartalom kapcsolatos például sportesemények, majd létrehozhat egy külön személy modellt használ az egyes porthoz (labdarúgó Kosárlabda, foci, stb.). 

Modell létrehozása után használhatja azáltal, hogy egy adott személy modell feltöltése/indexelő vagy videó újraindexelés modell Azonosítóját. Képzési videók esetében egy új arc frissíti az adott egyéni modell, amelyhez a videó társítva lett.

Minden fiók esetében az 50 személy modellek. Ha nem kell a több személy modellt támogató, nem rendel egy személy Modellazonosító a videó feltöltése/indexelő vagy újraindexelés. A Video Indexer ebben az esetben az alapértelmezett egyéni személy modellt használ a fiókjában.

## <a name="create-a-new-person-model"></a>Hozzon létre egy új személynek modell

Hozzon létre új személy modell található a megadott fiók. 

### <a name="request-url"></a>Kérés URL-címe

Ez a POST-kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|Fiókazonosító|sztring|Igen|A fiók globálisan egyedi azonosító|
|név|sztring|Igen|A személy modell neve|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz biztosít a nevét és a létrehozott modell azonosítója a személy modellezheti, amikor az imént létrehozott formátuma az alábbi példát követve.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Kell majd használnia a **azonosító** értékét a **personModelId** paraméter amikor [egy videó feltöltése az indexbe](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [videó újraindexelés](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Egy személy modell törlése

Egy egyéni személy modell törlése a megadott fiók. 

Miután a személy modell törlése sikeresen megtörtént, a törölt modellt használó aktuális videók indexe változatlan marad mindaddig, amíg Ön újraindexelése őket. Követően újraindexelés, az arcok a törölt modellben is nevű rendszer nem ismeri fel a Video Indexer által indexelt adott modellel; jelenlegi videók a azonban ezeket arcok fog továbbra is észlelhető. A jelenlegi videók, a törölt modellel indexelt most fogja használni a fiók alapértelmezett személy modell. Ha a törölt modell arcokat is neve a fiók alapértelmezett modellben, ezeket az arcok továbbra ismeri fel a videókban.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-PersonModel?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|Fiókazonosító|sztring|Igen|A fiók globálisan egyedi azonosító|
|id|sztring|Igen|A személy modellazonosító (jönnek létre, ha a személy modell jön létre)|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a személy modell törlése sikerült.

## <a name="get-all-person-models"></a>Minden személy modell beolvasása

Minden személy modell lépjen be a megadott fiók. 

### <a name="request-call"></a>Kérelem hívás

Ez az egy GET kéréssel.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-PersonModels?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|Fiókazonosító|sztring|Igen|A fiók globálisan egyedi azonosító|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz az összes, a személy modellek a fiókjában (beleértve az alapértelmezett személy modellt a megadott fiók), és az egyes nevek és azonosítók formátuma az alábbi példát követve listáját tartalmazza.

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

Kiválaszthatja, hogy melyik modellben használandó videó használatával a **azonosító** a személy modell értékét a **personModelId** paraméter amikor [egy videó feltöltése az indexbe](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) vagy [videó újraindexelés](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>A face frissítése

Ez a parancs lehetővé teszi, hogy frissítse a egy oldallal a videó a nevével, a videó azonosítója és, a face ID használatával. Ezután frissíti a személy modell, amely a videó feltöltése/indexelő vagy újraindexelés társítva. Ha nincs személy modell hozzá volt rendelve, a fiók alapértelmezett személy modell frissíti. 

Ez történik, ha észleli a található egyéb aktuális megosztó személy ugyanannak a modellnek ugyanazon face előfordulását. Felismerés, a többi aktuális videóit a face érvénybe lépéséhez, mivel ez egy batch-folyamat hosszabb időt is igénybe vehet.

Frissítheti a egy oldallal, amely a Video Indexer a hírességek, más néven ismeri fel. Az új nevet, amelyet átadhat elsőbbséget élveznek a beépített hírességek felismerése.

### <a name="request-call"></a>Kérelem hívás

Ez a POST-kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-face?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|Fiókazonosító|sztring|Igen|A fiók globálisan egyedi azonosító|
|videoId|sztring|Igen|A frissíteni kívánt arc jelenik meg a videó azonosítója. Ez jön létre, amikor a videó feltöltése és indexelve.|
|faceId|egész szám|Igen|A face frissített azonosítója. A faceId kérhet le a videók indexe|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|név|sztring|Igen|Új nevet a face való frissítéséhez.|

Egyedinek kell lennie személy modellek esetén, ha engedélyezi a két különböző arc ugyanazt az embert a modell azonos **neve** paraméter értéke, Video Indexer megtekinti az arcok, ugyanazt az embert, és össze legyen vonva őket, ha Ön a videó újraindexelése. 

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a face frissítése sikeresen megtörtént.

## <a name="next-steps"></a>További lépések

[A Video Indexer webhelyen személy modell testreszabása](customize-person-model-with-website.md)
