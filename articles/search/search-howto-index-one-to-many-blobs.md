---
title: Egy blob indexelése számos keresési indexelő dokumentumba az Azure Blob Indexer-ből a teljes szöveges kereséshez
description: Azure-Blobok bejárása a szöveges tartalomhoz a Azure Search blob indexelő használatával. Minden blob egy vagy több Azure Search indexelő dokumentumot eredményezhet.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 585d1e64ae124dce8cb0d4165ecbf0f503560405
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533686"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobok indexelése több keresési dokumentum létrehozásához
Alapértelmezés szerint a blob indexelő egyetlen keresési dokumentumként fogja kezelni a blob tartalmát. Bizonyos **parsingMode** -értékek olyan forgatókönyveket támogatnak, amelyekben egy adott blob több keresési dokumentumot is eredményezhet. A különböző típusú **parsingMode** , amelyek lehetővé teszik, hogy az indexelő több keresési dokumentumot is kinyerjen a blobból:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Egy-a-többhöz dokumentum kulcsa
A Azure Search indexben megjelenített minden dokumentumot egyedileg azonosít egy dokumentum kulcsa. 

Ha nincs megadva elemzési mód, és ha nincs explicit leképezés a kulcs mezőhöz az indexben, Azure Search automatikusan [leképezi](search-indexer-field-mappings.md) a `metadata_storage_path` tulajdonságot kulcsként. Ez a leképezés biztosítja, hogy az egyes Blobok külön keresési dokumentumként jelenjenek meg.

A fent felsorolt elemzési módok bármelyikének használatakor az egyik blob a "sok" keresési dokumentumra mutat, így a dokumentum kulcsa kizárólag a blob metaadatainak alapján használható. Ennek a korlátozásnak a leküzdéséhez Azure Search képes "egy-a-többhöz" dokumentum-kulcsot generálni a blobból kinyert egyes entitásokhoz. Ez a tulajdonság neve `AzureSearch_DocumentKey`, és hozzá lesz adva a blobból kinyert egyes entitásokhoz. Ennek a tulajdonságnak az értéke garantáltan egyedi a _Blobok közötti_ egyes entitások esetében, és az entitások külön keresési dokumentumokként jelennek meg.

Alapértelmezés szerint, ha nincs megadva explicit mező a Key index mezőhöz, a `AzureSearch_DocumentKey` a `base64Encode` mező-leképezési függvény használatával lesz leképezve.

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

Amikor létrehoz egy indexelő, és a **parsingMode** úgy állítja be, hogy `jsonLines` – anélkül, hogy megadta a Key mezőhöz tartozó explicit mező-hozzárendeléseket, a következő leképezést a rendszer implicit módon alkalmazza.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Ez a beállítás azt eredményezi, hogy a Azure Search index a következő adatokat tartalmazza (a Base64 kódolású azonosító lerövidíti a rövid időpontot)

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

Amikor `delimitedText` **parsingMode**hoz létre indexelő, természetesnek tűnhet, hogy egy mező-leképezési függvényt állítson be a Key mezőbe a következőképpen:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Ez a leképezés azonban _nem_ eredményez 4 olyan dokumentumot, amely megjelenik az indexben, mert a `recordid` mező nem egyedi a _Blobok között_. Ezért javasoljuk, hogy használja a `AzureSearch_DocumentKey` tulajdonságban alkalmazott implicit mező leképezését az "egy a többhöz" elemzési üzemmódok Key index mezőjére.

Ha explicit mező-hozzárendelést szeretne beállítani, győződjön meg arról, hogy a _sourceField_ különbözik az egyes entitások **minden blobban**.

> [!NOTE]
> A kinyert entitások egyediségének biztosításához `AzureSearch_DocumentKey` által használt megközelítés változhat, ezért nem szabad az alkalmazás igényeinek megfelelő értéket használni.

## <a name="next-steps"></a>Következő lépések

Ha még nem ismeri a blob-indexelés alapszintű szerkezetét és munkafolyamatát, először tekintse át az [Azure Blob Storage indexelését Azure Search](search-howto-index-json-blobs.md) első lépéseit. A különböző blob conten-típusok elemzési módjairól a következő cikkekben olvashat bővebben.

> [!div class="nextstepaction"]
> [CSV-Blobok indexelése](search-howto-index-csv-blobs.md) 
> [JSON-Blobok indexelése](search-howto-index-json-blobs.md)