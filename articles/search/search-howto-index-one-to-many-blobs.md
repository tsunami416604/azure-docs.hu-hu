---
title: Több dokumentumot tartalmazó Blobok indexelése
titleSuffix: Azure Cognitive Search
description: Azure-Blobok bejárása a szöveges tartalomhoz az Azure Cognitive Search blob indexelő használatával, ahol minden blob egy vagy több keresési indexelési dokumentumot eredményezhet.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112262"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobok indexelése több keresési dokumentum létrehozásához
Alapértelmezés szerint a blob indexelő egyetlen keresési dokumentumként fogja kezelni a blob tartalmát. Bizonyos **parsingMode** -értékek olyan forgatókönyveket támogatnak, amelyekben egy adott blob több keresési dokumentumot is eredményezhet. A különböző típusú **parsingMode** , amelyek lehetővé teszik, hogy az indexelő több keresési dokumentumot is kinyerjen a blobból:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Egy-a-többhöz dokumentum kulcsa
Az Azure Cognitive Search indexben megjelenített minden dokumentumot egyedileg azonosít egy dokumentum kulcsa. 

Ha nincs megadva elemzési mód, és ha nincs explicit leképezés az Azure-beli kulcs mezőjére, akkor a (Cognitive Search) automatikusan [leképezi](search-indexer-field-mappings.md) a `metadata_storage_path` tulajdonságot kulcsként. Ez a leképezés biztosítja, hogy az egyes Blobok külön keresési dokumentumként jelenjenek meg.

A fent felsorolt elemzési módok bármelyikének használatakor az egyik blob a "sok" keresési dokumentumra mutat, így a dokumentum kulcsa kizárólag a blob metaadatainak alapján használható. Ennek a korlátozásnak a leküzdéséhez az Azure Cognitive Search képes a blobokból kinyert egyes entitások "egy a többhöz" dokumentum-kulcsának generálására. Ez a tulajdonság neve `AzureSearch_DocumentKey` , és hozzá lesz adva a blobból kinyert egyes entitásokhoz. Ennek a tulajdonságnak az értéke garantáltan egyedi a _Blobok közötti_ egyes entitások esetében, és az entitások külön keresési dokumentumokként jelennek meg.

Alapértelmezés szerint, ha nincs megadva explicit mező a Key index mezőhöz, a a `AzureSearch_DocumentKey` leképezése a `base64Encode` mező leképezése függvény használatával történik.

## <a name="example"></a>Példa
Tegyük fel, hogy az index definíciója a következő mezőkkel rendelkezik:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

És a blob-tároló Blobokkal rendelkezik a következő szerkezettel:

_Blob1. JSON_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. JSON_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Amikor létrehoz egy indexelő, és a **parsingMode** úgy `jsonLines` állítja be, hogy a kulcs mezőhöz tartozó explicit mező-hozzárendelések megadása nélkül történjen, a következő leképezést a rendszer implicit módon alkalmazza.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ez a beállítás azt eredményezi, hogy az Azure Cognitive Search index a következő adatokat tartalmazza (a Base64 kódolású azonosító lerövidíti a rövid időpontot)

| id | hőmérséklet | pressure | időbélyeg |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Egyéni mezők leképezése az index kulcsa mezőhöz

Ha ugyanazt az index-definíciót szeretné, mint az előző példában, tegyük fel, hogy a blob-tároló blobokat tartalmaz a következő szerkezettel:

_Blob1. JSON_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. JSON_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Ha a `delimitedText` **parsingMode**-vel hoz létre indexelő, természetesnek tűnhet, hogy egy mező-leképezési függvényt állítson be a kulcs mezőhöz a következőképpen:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Ez a leképezés azonban _nem_ eredményez 4 olyan dokumentumot, amely megjelenik az indexben, mert a `recordid` mező nem egyedi a _Blobok között_. Ezért javasoljuk, hogy a `AzureSearch_DocumentKey` tulajdonságból az "egy a többhöz" elemzési üzemmódhoz tartozó Key index mezőre alkalmazott implicit mező-leképezést használja.

Ha explicit mező-hozzárendelést szeretne beállítani, győződjön meg arról, hogy a _sourceField_ különbözik az egyes entitások **minden blobban**.

> [!NOTE]
> A kinyert `AzureSearch_DocumentKey` entitások egyediségének biztosításához használt megközelítés változhat, ezért nem szabad az alkalmazás igényeinek megfelelően használni.

## <a name="next-steps"></a>További lépések

Ha még nem ismeri a blob-indexelés alapszintű szerkezetét és munkafolyamatát, először tekintse át az Azure [blob Storage indexelését az azure Cognitive Search](search-howto-index-json-blobs.md) . A különböző blob-tartalomtípusok elemzési módjaival kapcsolatos további információkért tekintse át a következő cikkeket.

> [!div class="nextstepaction"]
> [A CSV-Blobok](search-howto-index-csv-blobs.md)
> indexelése[JSON-Blobok indexelése](search-howto-index-json-blobs.md)
