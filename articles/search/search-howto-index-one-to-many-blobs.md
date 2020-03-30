---
title: Több dokumentumot tartalmazó tárgymutató-blobok
titleSuffix: Azure Cognitive Search
description: Feltérképezheti az Azure-blobok szöveges tartalom az Azure Congitive Search Blob indexelő, ahol minden blob eredményezhet egy vagy több keresési index dokumentumokat.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112262"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobok indexelése több keresési dokumentum létrehozásához
Alapértelmezés szerint egy blob indexelő fogja kezelni a blob tartalmát, mint egy keresési dokumentum. Bizonyos **elemzési módértékek** olyan forgatókönyveket támogatnak, amelyekben egy adott blob több keresési dokumentumot eredményezhet. A különböző típusú **elemzésmód,** amely lehetővé teszi az indexelő több keresési dokumentumot kinyerni egy blob a következők:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Egy-a-többhöz dokumentumkulcs
Minden dokumentumot, amely megjelenik egy Azure Cognitive Search index egyedileg azonosítja egy dokumentum kulcs. 

Ha nincs megadva elemzési mód, és ha nincs explicit leképezése a kulcsmező `metadata_storage_path` az indexben az Azure Cognitive Search automatikusan [leképezi](search-indexer-field-mappings.md) a tulajdonságot a kulcs. Ez a leképezés biztosítja, hogy minden blob külön keresési dokumentumként jelenjen meg.

A fent felsorolt elemzési módok bármelyikének használatakor egy blob "több" keresési dokumentumra van leképezve, így a dokumentumkulcs kizárólag a blob metaadatai alapján nem megfelelő. Ez a korlátozás leküzdése érdekében az Azure Cognitive Search képes egy "egy-a-többhöz" dokumentumkulcs létrehozására minden egyes entitás egy blobból kinyert. Ez a `AzureSearch_DocumentKey` tulajdonság neve, és hozzáadja a blobból kinyert minden egyes entitáshoz. A tulajdonság értéke garantáltan egyedi minden egyes entitás a _blobok között,_ és az entitások külön keresési dokumentumokként jelennek meg.

Alapértelmezés szerint, ha nincs megadva explicit mezőleképezés `AzureSearch_DocumentKey` a kulcsindex mezőhöz, `base64Encode` a program a mezőleképezési függvény használatával hozzá rendeli hozzá.

## <a name="example"></a>Példa
Tegyük fel, hogy indexdefiníciója a következő mezőkkel rendelkezik:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

A blobtároló blobok a következő struktúrával rendelkeznek:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Paca.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Amikor létrehoz egy indexelőt, és a `jsonLines` **parsingMode-ot** - a kulcsmező explicit mezőleképezésének megadása nélkül állítja be, a következő leképezés implicit módon kerül alkalmazásra
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ez a beállítás az Azure Cognitive Search indexét eredményezi, amely a következő információkat tartalmazza (a rövidség kedvéért lerövidül a base64 kódolású azonosító)

| id | hőmérséklet | pressure | időbélyeg |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy között | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Egyéni mezőleképezés az indexkulcs mezőhöz

Feltételezve, hogy ugyanazt az indexdefiníciót, mint az előző példa, tegyük fel, hogy a blob tároló blobok a következő struktúrával:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Paca.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Amikor `delimitedText` **egy indexelőt a parsingMode**segítségével hoz létre, természetesnek érezheti, hogy mezőleképezési függvényt állít be a kulcsmezőhöz az alábbiak szerint:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Ez a leképezés azonban _nem_ eredményezi, hogy `recordid` 4 dokumentum jelenik meg az indexben, mert a mező nem egyedi _a blobok között._ Ezért azt javasoljuk, hogy használja a tulajdonságról a `AzureSearch_DocumentKey` kulcs indexmezőjére alkalmazott implicit mezőleképezést az "egy-a-többhöz" elemzési módokhoz.

Ha explicit mezőleképezést szeretne beállítani, győződjön meg arról, hogy a _sourceField_ minden egyes entitásesetében különbözik **az összes blobban.**

> [!NOTE]
> A kinyert `AzureSearch_DocumentKey` entitásonkénti egyediség biztosításával használt megközelítés változhat, ezért ne hagyatkozzon az alkalmazás igényeinek megfelelő értékre.

## <a name="next-steps"></a>További lépések

Ha még nem ismeri a blob indexelésének alapvető szerkezetét és munkafolyamatát, először tekintse át az [Azure Blob Storage indexelése az Azure Cognitive Search alkalmazással](search-howto-index-json-blobs.md) című folyamatot. A különböző blobtartalom-típusok elemzési módjairól az alábbi cikkekben talál további információt.

> [!div class="nextstepaction"]
> [CsV-blobok indexelése](search-howto-index-csv-blobs.md)
> [JSON-blobokat indexelése](search-howto-index-json-blobs.md)
