---
title: Nyelvi modell testreszabása a Video Indexer API-val
titlesuffix: Azure Media Services
description: Ismerje meg, hogyan szabhatja testre a nyelvi modellt a Video Indexer API-val.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127984"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Nyelvi modell testreszabása a Video Indexer API-val

A Video Indexer lehetővé teszi, hogy egyéni nyelvi modelleket hozzon létre a beszédfelismerés testreszabásához adaptációs szöveg feltöltésével, nevezetesen olyan tartományból származó szöveget, amelynek szókincséhez alkalmazkodni szeretne. A modell betanítása után a program felismeri az adaptációs szövegben megjelenő új szavakat.

Az egyéni nyelvi modellek részletes áttekintését és gyakorlati tanácsait a [Nyelvi modell testreszabása videoindexelővel](customize-language-model-overview.md)című témakörben találja.

A Video Indexer API-k segítségével egyéni nyelvi modelleket hozhat létre és szerkeszthet a fiókjában, a jelen témakörben leírtak szerint. A webhelyet a Language modell testreszabása című részben leírtak szerint is [használhatja a Video Indexer webhely használatával.](customize-language-model-with-api.md)

## <a name="create-a-language-model"></a>Nyelvi modell létrehozása

Hozzon [létre egy nyelvi modell](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) API-t egy új egyéni nyelvi modell a megadott fiókban. Ebben a hívásban feltöltheti a nyelvi modell fájljait. Azt is megteheti, hogy itt hozza létre a nyelvi modellt, és később a nyelvi modell frissítésével fájlokat tölthet fel a modellhez.

> [!NOTE]
> Továbbra is be kell tanítania a modellt az engedélyezett fájlokkal ahhoz, hogy a modell megismerje a fájlok tartalmát. A nyelv képzésére vonatkozó utasítások a következő részben találhatók.

A Nyelvi modellhez hozzáadni kívánt fájlok feltöltéséhez a fenti paraméterek en kívül fájlokat kell feltöltenie a törzsbe a FormData használatával. A feladat kétféleképpen végezhető el:

* Kulcs lesz a fájl neve és értéke lesz a txt fájlt.
* Kulcs lesz a fájl neve és értéke lesz egy URL-t txt fájlt.

### <a name="response"></a>Válasz

A válasz metaadatokat biztosít az újonnan létrehozott nyelvi modellről, valamint metaadatokat a modell egyes fájljairól a példa JSON-kimenet formátumát követve:

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

A [betanítása egy nyelvi modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API betanítja az egyéni nyelvi modell a megadott fiókban a tartalmát a fájlokat, amelyek feltöltötték, és engedélyezve van a nyelvi modellben.

> [!NOTE]
> Először létre kell hoznia a Nyelvi modellt, és fel kell töltenie a fájljait. Fájlokat tölthet fel a Nyelvi modell létrehozásakor vagy a Nyelvi modell frissítésével.

### <a name="response"></a>Válasz

A válasz metaadatokat biztosít az újonnan betanított nyelvi modellről, valamint metaadatokat a modell egyes fájljairól a példa JSON-kimenet formátumát követve:

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

A `id` visszaadott egy egyedi azonosító, amely a `languageModelId` nyelvi modellek megkülönböztetésére szolgál, míg a [videó indexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) `linguisticModelId` és [újraindexeléséhez](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (más néven a Video Indexer upload/reindex API-kban is ismert) használható.

## <a name="delete-a-language-model"></a>Nyelvi modell törlése

A [nyelvi modell törlése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API törli az egyéni nyelvi modellt a megadott fiókból. A törölt nyelvi modellt használó videók ugyanazt az indexet megőrzik, amíg újra indexeli a videót. Ha újraindexeli a videót, új nyelvi modellt rendelhet a videóhoz. Ellenkező esetben a Video Indexer az alapértelmezett modellt fogja használni a videó újraindexeléséhez.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom, ha a nyelvi modell sikeresen törlődik.

## <a name="update-a-language-model"></a>Nyelvi modell frissítése

A [nyelvi modell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) API frissítése frissíti az egyéni nyelvi személy modell a megadott fiókban.

> [!NOTE]
> Már létre kell hoznia a nyelvi modellt. Ezzel a hívással engedélyezheti vagy letilthatja a modell összes fájlját, frissítheti a Nyelvi modell nevét, és feltöltheti a nyelvi modellhez hozzáadandó fájlokat.

A Nyelvi modellhez hozzáadni kívánt fájlok feltöltéséhez a fenti paraméterek en kívül fájlokat kell feltöltenie a törzsbe a FormData használatával. A feladat kétféleképpen végezhető el:

* Kulcs lesz a fájl neve és értéke lesz a txt fájlt.
* Kulcs lesz a fájl neve és értéke lesz egy URL-t txt fájlt.

### <a name="response"></a>Válasz

A válasz metaadatokat biztosít az újonnan betanított nyelvi modellről, valamint metaadatokat a modell egyes fájljairól a példa JSON-kimenet formátumát követve:

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

Használja `id` a válaszban visszaadott fájlokat a fájl tartalmának letöltéséhez.

## <a name="update-a-file-from-a-language-model"></a>Fájl frissítése nyelvi modellből

A [fájl frissítése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) lehetővé teszi `enable` egy fájl nevének és állapotának frissítését egy egyéni nyelvi modellben a megadott fiókban.

### <a name="response"></a>Válasz

A válasz metaadatokat ad a fájlhoz, amelyet az alábbi JSON-kimenet formátuma alapján frissített.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Használja `id` a válaszban visszaadott fájlt a fájl tartalmának letöltéséhez.

## <a name="get-a-specific-language-model"></a>Egy adott nyelvi modell beszereznie

A [get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API a megadott nyelvi modellre vonatkozó információkat ad vissza a megadott fiókban, például a nyelvet és a nyelvi modellben található fájlokat.

### <a name="response"></a>Válasz

A válasz metaadatokat biztosít a megadott nyelvi modellről, valamint metaadatokat a modell egyes fájljairól a példa JSON kimenetének formátumát követve:

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

Használja `id` a válaszban visszaadott fájlt a fájl tartalmának letöltéséhez.

## <a name="get-all-the-language-models"></a>Szerezd meg az összes nyelvi modellt

Az összes API [bekéselése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) a megadott fiókban lévő összes egyéni nyelvi modellt visszaadja.

### <a name="response"></a>Válasz

A válasz a fiókjában található összes nyelvi modell, valamint a példa JSON-kimenetformátumát követő en-alapú metaadataik és fájljainak listáját tartalmazza:

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

## <a name="delete-a-file-from-a-language-model"></a>Fájl törlése nyelvi modellből

A [delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) API törli a megadott fájlt a megadott nyelvi modellből a megadott fiókban.

### <a name="response"></a>Válasz

Nincs visszaadott tartalom, ha a fájlt sikeresen törli a nyelvi modellből.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Fájl metaadatainak beszereznie nyelvi modellből

A fájl API [metaadatainak bekésezése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) a megadott fájl tartalmát és metaadatait adja vissza a fiókjában kiválasztott nyelvi modellből.

### <a name="response"></a>Válasz

A válasz a fájl tartalmát és metaadatait JSON formátumban tartalmazza, hasonlóan ehhez a példához:

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
> A példafájl tartalma a "hello" és a world szavak két külön sorban.

## <a name="download-a-file-from-a-language-model"></a>Fájl letöltése nyelvi modellből

A [fájl letöltése](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API letölti a megadott fájl tartalmát tartalmazó szövegfájlt a megadott fiók megadott nyelvi modelljéből. Ennek a szövegfájlnak meg kell egyeznie az eredetileg feltöltött szövegfájl tartalmával.

### <a name="response"></a>Válasz

A válasz egy szöveges fájl letöltése lesz, amelynek tartalma a fájl Tartalma JSON formátumban történik.

## <a name="next-steps"></a>További lépések

[Nyelvi modell testreszabása a webhely használatával](customize-language-model-with-website.md)
