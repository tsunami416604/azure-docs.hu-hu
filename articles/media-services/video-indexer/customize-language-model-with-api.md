---
title: A Video Indexer API-k használatával testre szabhatja a nyelvi modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a nyelvi modell a Video Indexer API-kkal.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 5f77857c82846fe9c3d2ad4f5f82572d18401691
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003601"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>A Video Indexer API-kkal nyelvi modell testreszabása

A video Indexer segítségével hozhat létre egyéni nyelvi modelljeit, testre szabható beszédfelismerés betanítás szövegben, nevezetesen a tartomány, amelynek Szójegyzék a motor mértékének megfelelően szeretné feltölteni. Ha a modell betanításához a betanítás szövegben szereplő szavakat ismerhető fel. 

A részletes áttekintése és ajánlott eljárások az egyéni nyelvi modelljeit: [Video Indexer nyelvi modell testreszabása](customize-language-model-overview.md).

Használhatja a Video Indexer API-kat hozhat létre és szerkeszthet egyéni nyelvi modelljeit a fiókban, ebben a témakörben leírtak szerint. Használhatja a webhelyen leírtak szerint [testreszabása nyelvi modell a Video Indexer webhelyen](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

A következő parancs létrehoz egy új egyéni nyelvi modell a megadott fiók. A nyelvi modell, a hívás a fájlokat tölthet fel. Azt is megteheti Itt a nyelvi modell létrehozásához és a modell fájlok feltöltése a később a nyelvi modell frissítésével.

> [!NOTE]
> Az engedélyezett fájlok a modellhez, a fájlok tartalmát a további továbbra is kell betanítja a modellt. Utasításokat a képzés nyelv a következő szakaszban találhatók.

### <a name="request-url"></a>Kérés URL-címe

Ez a POST-kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosító|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|modelName|sztring|Igen|A nyelvi modell neve|
|language|sztring|Igen|A nyelvi modell nyelve. <br/>A **nyelvi** paramétert meg kell adni a nyelvi "nyelvi címke-régió" BCP-47 formátumát (például: "en-US"). Támogatott nyelvek (en-US) angol, német (de-DE), spanyol (es – SP), arab (ar-működtek az Adatbázisok), francia (fr-FR), Hindi (üdv mindenkinek! – Üdvözöljük), olasz (it-IT), japán (ja-JP), portugál (pt-BR), orosz (ru-RU) és kínai (zh-CN).  |

### <a name="request-body"></a>A kérés törzse

A nyelvi modell hozzáadandó fájlok feltöltése, fel kell tölteni az űrlap-adatok a szükséges paraméterek a fenti értékek biztosítása mellett a szervezet fájlokat. Ehhez két módja van: 

1. A kulcs lesz a fájl neve és értéke lesz a txt-fájlba
2. A kulcs lesz a fájl neve és értéke lesz a txt-fájl URL-címe

### <a name="response"></a>Válasz

A válasz mellett az egyes a következő példa JSON-kimenet formátumát a modell fájlok metaadatait az újonnan létrehozott nyelvi modell metaadatainak nyújt.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Nyelvi modell betanítása

A következő parancs betanítja egy egyéni nyelvi modell található a megadott fiók által feltöltött és engedélyezve van a nyelvi modell a fájlok tartalmát. 

> [!NOTE]
> Először hozzon létre a nyelvi modell, és töltse fel a fájlokat. Fájlokat tölthet fel vagy a nyelvi modell létrehozása során, vagy a nyelvi modell frissítésével. 

### <a name="request-url"></a>Kérés URL-címe

Ez a PUT kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz mellett az egyes a következő példa JSON-kimenet formátumát a modell fájlok metaadatait az újonnan betanított nyelvi modell metaadatainak nyújt.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Kell majd használnia a **azonosító** értékét a nyelvi modell, a a **linguisticModelId** paraméter amikor [egy videó feltöltése az indexbe](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) és a  **languageModelId** paraméter amikor [videó újraindexelés](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

A következő parancsot egy egyéni nyelvi modell törlése a megadott fiók. Bármely videó használta a szolgáltatást a törölt nyelvi modell fogja megőrizni a ugyanazt az indexet, mindaddig, amíg újra indexeli a videót. Ha újraindexelni a videót, egy új nyelvi modellbe rendelhet a videót. Ellenkező esetben a Video Indexer használja az alapértelmezett modell újraindexelni a videót.

### <a name="request-url"></a>Kérés URL-címe

Ez a törlési kérelem.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a nyelvi modell törlése sikerült.

## <a name="update-a-language-model"></a>Nyelvi modell frissítése

A következő parancs frissíti a megadott fiók egy egyéni nyelvi személy modell.

> [!NOTE]
> Kell már létrehozta a nyelvi modell. Ez a hívás segítségével engedélyezése vagy letiltása a modellben található összes fájlt, a nyelvi modell nevének frissítéséhez és a nyelvi modell hozzáadandó fájlok feltöltése.

### <a name="request-url"></a>Kérés URL-címe

Ez a PUT kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|modelName|sztring|Nem|Új nevét, amely segítségével biztosíthat a modell|
|engedélyezése|logikai|Nem|Válassza ki, hogy vannak-e ebben a modellben található összes fájlt (true) engedélyezve vagy letiltva (hamis)|

### <a name="request-body"></a>A kérés törzse

A nyelvi modell hozzáadandó fájlok feltöltése, fel kell tölteni az űrlap-adatok a szükséges paraméterek a fenti értékek biztosítása mellett a szervezet fájlokat. Ehhez két módja van: 

1. A kulcs lesz a fájl neve és értéke lesz a txt-fájlba
2. A kulcs lesz a fájl neve és értéke lesz a txt-fájl URL-címe

### <a name="response"></a>Válasz

A válasz mellett az egyes a következő példa JSON-kimenet formátumát a modell fájlok metaadatait az újonnan betanított nyelvi modell metaadatainak nyújt.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Használhatja a **azonosító** fájlok visszaadott Itt töltheti le a fájl tartalmát.

## <a name="update-a-file-from-a-language-model"></a>Egy fájl a nyelvi modell frissítése

A következő parancsot lehetővé teszi, hogy frissítse a nevét, és **engedélyezése** állapot egy fájl a megadott fiók az egyéni nyelvi modell.

### <a name="request-url"></a>Kérés URL-címe

Ez a PUT kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|sztring|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|fileName|sztring|Nem|Frissítse a fájl nevét, a neve|
|engedélyezése|logikai|Nem|Ez a fájl-e (igaz) engedélyezve van, vagy le van tiltva (hamis), a nyelvi modell||

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz a fájlt az alábbi példa JSON-kimenet formátuma a következő frissített metaadatokat nyújt.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Használhatja a **azonosító** fájl visszaadott Itt töltheti le a fájl tartalmát.

## <a name="get-a-specific-language-model"></a>Egy adott nyelvi modell beolvasása

A következő parancsot a megadott nyelvi modell például nyelvi és a nyelvi modell a fájlokat a megadott fiók az információkat adja vissza. 

### <a name="request-url"></a>Kérés URL-címe

Ez az egy GET kéréssel.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16).

### <a name="request-parameters-and-request-body"></a>A kérés paraméterei és a kérelem törzse

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz a megadott nyelvi modell, valamint egyes, a modell fájlok az alábbi példa JSON-kimenet formátuma a következő metaadatokat metaadatok nyújt.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Használhatja a **azonosító** fájl visszaadott Itt töltheti le a fájl tartalmát.

## <a name="get-all-the-language-models"></a>Az összes nyelvi modellek beolvasása

A következő parancsot adja vissza minden, az egyéni nyelvi modellek listáját a megadott fiók.

### <a name="request-url"></a>Kérés URL-címe

Ez az egy GET kérelmet.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz a nyelvi modell, a fiókban lévő összes és az egyes metaadatok és az alábbi példa JSON-kimenet formátuma a következő fájlok listáját tartalmazza.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Fájl törlése a nyelvi modell

A következő parancsot a megadott fájl törlése a megadott nyelvi modell található a megadott fiók. 

### <a name="request-url"></a>Kérés URL-címe

Ez a törlési kérelem.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|sztring|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom van, amikor a fájl sikeresen törlődött a nyelvi modell alapján.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>A fájl metaadatainak beolvasása a nyelvi modell

Ez adja vissza a metaadatait és tartalmát a megadott fájlban található a kiválasztott nyelvi modell a fiókját.

### <a name="request-url"></a>Kérés URL-címe

Ez az egy GET kéréssel.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|sztring|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz tartalmát és a fájl JSON formátumú, ehhez hasonló metaadatait tartalmazza:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Ez a példa a fájl tartalma a "hello" szót, és a világ"két külön sorban.

## <a name="download-a-file-from-a-language-model"></a>Töltse le a fájlt a nyelvi modell

A következő parancs letölti a tartalmát a megadott nyelvi modell található a megadott fiók a megadott fájlt tartalmazó szövegfájl. A szövegfájl meg kell egyeznie az eredetileg feltöltött szöveges fájl tartalmát.

### <a name="request-url"></a>Kérés URL-címe
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Az alábbi, a kérelmet a Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|sztring|Igen|A fiók globálisan egyedi azonosító|
|modelId|sztring|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|sztring|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|hozzáférési tokent|sztring|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse 

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz lesz a JSON-formátumban a fájl tartalmát egy szöveges fájl letöltését. 

## <a name="next-steps"></a>További lépések

[Webhely segítségével nyelvi modell testreszabása](customize-language-model-with-website.md)
