---
title: A Video Indexer API-k használatával testre szabhatja a nyelvi modell – Azure
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a nyelvi modell a Video Indexer API-kkal.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4ef5354a94ae707df8dd1f2767efe04dfbacd7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799590"
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

[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|modelName|string|Igen|A nyelvi modell neve|
|language|string|Igen|A nyelvi modell nyelve. <br/>A **nyelvi** paramétert meg kell adni a nyelvi "nyelvi címke-régió" BCP-47 formátumát (például: "en-US"). Támogatott nyelvek (en-US) angol, német (de-DE), spanyol (es – SP), arab (ar-működtek az Adatbázisok), francia (fr-FR), Hindi (üdv mindenkinek! – Üdvözöljük), olasz (it-IT), japán (ja-JP), portugál (pt-BR), orosz (ru-RU) és kínai (zh-CN).  |

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|modelName|string|Nem|Új nevét, amely segítségével biztosíthat a modell|
|Engedélyezése|logikai|Nem|Válassza ki, hogy vannak-e ebben a modellben található összes fájlt (true) engedélyezve vagy letiltva (hamis)|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountId|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|string|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|
|fileName|string|Nem|Frissítse a fájl nevét, a neve|
|Engedélyezése|logikai|Nem|Ez a fájl-e (igaz) engedélyezve van, vagy le van tiltva (hamis), a nyelvi modell|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>A kérés paraméterei és a kérelem törzse

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell azonosítót (jönnek létre, ha a nyelvi modell létrehozása)|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>A kérés paraméterei

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|string|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|string|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

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
 
[Tekintse meg a szükséges paramétereket, és a Video Indexer fejlesztői portál használatával kipróbálásához](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>A kérés paraméterei 

|**Name (Név)**|**Típus**|**Kötelező**|**Leírás**|
|---|---|---|---|
|location|string|Igen|Az Azure-régió, amelyhez a hívást kell átirányítani. További információkért lásd: [Azure-régiók és a Video Indexer](regions.md).|
|accountID|string|Igen|A fiók globálisan egyedi azonosító|
|modelId|string|Igen|A nyelvi modell, amely tartalmazza a fájl (jönnek létre, ha a nyelvi modell létrehozása) azonosítója|
|fileId|string|Igen|A fájl, amely frissítés alatt áll (jönnek létre, ha a fájl nem feltöltött a létrehozáskor vagy a nyelvi modell frissítése) azonosítója|
|accessToken|string|Igen|Hozzáférési jogkivonat (hatókör kell [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) hitelesíti a rendszer a hívást. Hozzáférési jogkivonatok 1 órán belül lejár.|

### <a name="request-body"></a>A kérés törzse 

További az e híváshoz szükséges a kérelem törzsében.

### <a name="response"></a>Válasz

A válasz lesz a JSON-formátumban a fájl tartalmát egy szöveges fájl letöltését. 

## <a name="next-steps"></a>További lépések

[Webhely segítségével nyelvi modell testreszabása](customize-language-model-with-website.md)
