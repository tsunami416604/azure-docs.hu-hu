---
title: Az Azure Blob indexelő által több keresési indexelési dokumentumot tartalmazó Blobok indexelése teljes szöveges kereséshez – Azure Search
description: Azure-Blobok bejárása a szöveges tartalomhoz a Azure Search blob indexelő használatával. Az egyes Blobok egy vagy több Azure Search indexelő dokumentumot is tartalmazhatnak.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: 2c2a17d006f65854a89b9fac1818fcec420c07dc
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182301"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Több keresési dokumentumot előállító Blobok indexelése
Alapértelmezés szerint a blob indexelő egyetlen keresési dokumentumként fogja kezelni a blob tartalmát. Bizonyos **parsingMode** -értékek olyan forgatókönyveket támogatnak, amelyekben egy adott blob több keresési dokumentumot is eredményezhet. A különböző típusú **parsingMode** , amelyek lehetővé teszik, hogy az indexelő több keresési dokumentumot is kinyerjen a blobból:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Egy-a-többhöz dokumentum kulcsa
A Azure Search indexben megjelenített minden dokumentumot egyedileg azonosít egy dokumentum kulcsa. 

Ha nincs megadva elemzési mód, és ha nincs explicit leképezés a kulcs mezőhöz az indexben, Azure Search automatikusan leképezi [](search-indexer-field-mappings.md) a `metadata_storage_path` tulajdonságot kulcsként. Ez a leképezés biztosítja, hogy az egyes Blobok külön keresési dokumentumként jelenjenek meg.

A fent felsorolt elemzési módok bármelyikének használatakor az egyik blob a "sok" keresési dokumentumra mutat, így a dokumentum kulcsa kizárólag a blob metaadatainak alapján használható. Ennek a korlátozásnak a leküzdéséhez Azure Search képes "egy-a-többhöz" dokumentum-kulcsot generálni a blobból kinyert egyes entitásokhoz. Ez a tulajdonság neve `AzureSearch_DocumentKey` , és hozzá lesz adva a blobból kinyert egyes entitásokhoz. Ennek a tulajdonságnak az értéke garantáltan egyedi a _Blobok közötti_ egyes entitások esetében, és az entitások külön keresési dokumentumokként jelennek meg.

Alapértelmezés szerint, ha nincs megadva explicit mező a Key index mezőhöz, a a `AzureSearch_DocumentKey` leképezése a `base64Encode` mező leképezése függvény használatával történik.

## <a name="example"></a>Példa
Tegyük fel, hogy az index definíciója a következő mezőkkel rendelkezik:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

És a blob-tároló Blobokkal rendelkezik a következő szerkezettel:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Amikor létrehoz egy indexelő, és a **parsingMode** úgy `jsonLines` állítja be, hogy a kulcs mezőhöz tartozó explicit mező-hozzárendelések megadása nélkül történjen, a következő leképezést a rendszer implicit módon alkalmazza.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ez a beállítás azt eredményezi, hogy a Azure Search index a következő adatokat tartalmazza (a Base64 kódolású azonosító lerövidíti a rövid időpontot)

| id | hőmérséklet | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Egyéni mezők leképezése az index kulcsa mezőhöz

Ha ugyanazt az index-definíciót szeretné, mint az előző példában, tegyük fel, hogy a blob-tároló blobokat tartalmaz a következő szerkezettel:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

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

## <a name="see-also"></a>Lásd még

+ [Indexelő a Azure Search](search-indexer-overview.md)
+ [Az Azure Blob Storage indexelése Azure Search](search-howto-index-json-blobs.md)
+ [CSV-Blobok indexelése Azure Search blob indexelő](search-howto-index-csv-blobs.md)
+ [JSON-Blobok indexelése Azure Search blob indexelő](search-howto-index-json-blobs.md)

## <a name="NextSteps"></a>Következő lépések
* Ha többet szeretne megtudni a Azure Searchről, tekintse meg a [keresési szolgáltatás lapot](https://azure.microsoft.com/services/search/).