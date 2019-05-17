---
title: Azure Video Indexer segítségével márkái modell testreszabása
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan lehet Azure Video Indexer segítségével márkái modelljét testreszabhatja.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8d0806bc0262cd45a49e4f97ea629683ac239aa8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799642"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>A Video Indexer API márkái modell testreszabása

A video Indexer márka észlelését a beszéd- és vizuális szöveges indexelést és a video- és audiotartalmak újraindexelés során támogatja. A márka duplikálásészlelési szolgáltatását a termékek, szolgáltatások, hírforrásaiból azonosítja, és a vállalatok a Bing márkái adatbázis által javasolt. Például ha egy videó vagy hang tartalom említett, a Microsoft vagy egy videóban visual szöveg megjelenik, Video Indexer észleli ezt, a tartalom egy adott márkához. Márka egyéni modell lehetővé teszi, hogy bizonyos márkái kizárása és márkákat, amelyeket a modell, amely nem feltétlenül Bing márkái adatbázis részét kell tartalmaznia.

Részletes ismertetőt talál [áttekintése](customize-brands-model-overview.md).

A Video Indexer API-k segítségével létrehozását, használatát és egyéni márkái modellek észlelhető a videó szerkesztése ebben a témakörben leírtak szerint. Használhatja a Video Indexer webhelyen leírtak szerint [testreszabása márkái modell a Video Indexer webhelyen](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Hozzon létre egy adott Márkához

Ez létrehoz egy új egyéni márka, és hozzáadja azt a megadott fiók egyéni márkái modelljére.

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

Mellett ezeket a paramétereket meg kell adni a kérelem törzsében JSON-objektum, amely információt nyújt a formátuma a következő példa a következő új márka.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Beállítás **engedélyezve** igaz helyezi a márka, a *Belefoglalás* lista a Video Indexer észleléséhez. Beállítás **engedélyezve** false értékre a márka helyezi a *kizárása* listájában, így a Video Indexer nem észleli.

A **referenceUrl** érték lehet egy a Wikipédia-oldalának hivatkozása, például a márka referencia webhelyeket.

A **címkék** értéke a márka címkék listája. Ez megjelenik-e a márka *kategória* mezőt a Video Indexer webhelyen. Például a márka "Azure" címkével ellátott is, vagy "Felhő" kategóriába.

### <a name="response"></a>Válasz

A válasz a márka, amely az újonnan létrehozott az alábbi példa formátuma a következő információkat biztosít.

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

## <a name="delete-a-brand"></a>Egy adott Márkához törlése

Egy adott márkához eltávolítja a megadott fiók egyéni márkái modellből. A fiók szerepel a **accountId** paraméter. Miután sikeresen nevű, a márka már nem található a *Belefoglalás* vagy *kizárása* védjegyzéssel listák.

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|azonosító|egész szám|Igen|A márkaazonosító (jönnek létre, ha a márka lett létrehozva)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a márka törlése sikerült.

## <a name="get-a-specific-brand"></a>Egy adott márkájú beolvasása

Ez lehetővé teszi, keresse meg az egyéni márkái modell a megadott fiók használatával a márkaazonosító fórummárka részleteit.

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|azonosító|egész szám|Igen|A Márkaazonosító (jönnek létre, ha a márka lett létrehozva)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz a márka (használatával Márkaazonosító) keres tájékoztatást nyújt az alábbi példa formátuma a következő.

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
> **engedélyezve** beállítása **igaz** jelzi, hogy a márka szerepel a *Belefoglalás* lista a Video Indexer észleléséhez és **engedélyezve** folyamatban van a False (hamis), amely jelzi a márka szerepel a *kizárása* listájában, így a Video Indexer nem észleli.

## <a name="update-a-specific-brand"></a>Egy adott márkájú frissítése

Ez lehetővé teszi, keresse meg a megadott fiók azonosítójával a márka egyéni márkái modellben egy adott márkához részletei

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|azonosító|egész szám|Igen|A Márkaazonosító (jönnek létre, ha a márka lett létrehozva)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

Ezeket a paramétereket, valamint meg kell adnia a kérelem törzsében JSON-objektum által biztosított frissített adatokat a márka, amely az alábbi példa formátuma a következő frissíteni szeretné a.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> Ebben a példában a márka, a kérés törzsében példa a létrehozott a **hozzon létre egy adott Márkához** szakasz frissítése folyamatban van itt egy új címkét, és új leírása. A **engedélyezve** értéke is megváltozott hamis értékre helyezni, a *kizárása* listája.

### <a name="response"></a>Válasz

A válasz a márka, az alábbi példa formátuma a következő frissített a frissített információkat biztosít.

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

## <a name="get-all-of-the-brands"></a>Mindezt a márka

Ez ad vissza, a márka mindegyikét az egyéni márkái modell, függetlenül attól, hogy a márka szinkronban kell lennie a megadott fiók a *Belefoglalás* vagy *kizárása* márkájú-listában.

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz a márka, a fiókban lévő összes és azok adatai az alábbi példa formátuma a következő mindegyike listáját tartalmazza.

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
> A márka nevű *példa* szerepel a *Belefoglalás* a Video Indexer észleléséhez és a márka nevű lista *példa2* szerepel a *kizárása* listája , így a Video Indexer nem észleli.

## <a name="get-brands-model-settings"></a>Márkái modell beállításainak lekérése

Ez a megadott fiókot a márka modelljének beállításait adja vissza. A modellbeállítások a márka jelölik, a Bing márkái adatbázisból észlelés engedélyezve van-e. A Bing márkái nincs engedélyezve, ha a Video Indexer csak észleli márka egyéni márkái modellből a megadott fiók.

### <a name="request-url"></a>Lekérdezés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válaszból kiderül, hogy a Bing márkái engedélyezve lettek-e az alábbi példa formátuma a következő.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** alatt állítsa true jelöli, hogy a Bing márkái engedélyezve vannak. Ha *useBuiltin* van false (hamis), a Bing márkái le vannak tiltva. A **állapot** értéket figyelmen kívül hagyható, már elavult.

## <a name="update-brands-model-settings"></a>Márkái modell beállításainak frissítése

Ez frissíti a megadott fiók márkái modell beállításait. A modellbeállítások a márka jelölik, a Bing márkái adatbázisból észlelés engedélyezve van-e. A Bing márkái nincs engedélyezve, ha a Video Indexer csak észleli márka egyéni márkái modellből a megadott fiók.

### <a name="request-url"></a>Kérelem URL-címe:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|hely|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>Kérelem törzse

Mellett ezeket a paramétereket meg kell adni a kérelem törzsében JSON-objektum, amely információt nyújt a formátuma a következő példa a következő új márka.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** alatt állítsa true jelöli, hogy a Bing márkái engedélyezve vannak. Ha *useBuiltin* van false (hamis), a Bing márkái le vannak tiltva.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a márka modell beállítás frissítése sikerült.

## <a name="next-steps"></a>További lépések

[Webhely segítségével márkái modell testreszabása](customize-brands-model-with-website.md)
