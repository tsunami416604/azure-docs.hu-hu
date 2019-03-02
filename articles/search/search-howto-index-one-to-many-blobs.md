---
title: Index blobokat tartalmazó több keresési index dokumentumokat az Azure Blob-indexelő az Azure Search – a teljes szöveges keresés
description: Feltérképezi az Azure-blobok az Azure Search Blob indexelőjével szöveges tartalom. Minden egyes blob egy vagy több Azure Search-index dokumentumot tartalmazhat.
ms.date: 02/12/2019
author: arv100kri
manager: briansmi
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seofeb2018
ms.openlocfilehash: e95eff015340659b642dff800a03f615e22c1577
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220570"
---
# <a name="indexing-blobs-producing-multiple-search-documents"></a>Dokumentumok keresése több előállító-blobok indexelése
Alapértelmezés szerint egy blob indexelőjével egyetlen keresési dokumentumként kezeli a blob tartalmát. Bizonyos **parsingMode** értékeket támogatja a forgatókönyvek, ahol az egyes blob több keresési dokumentumok eredményezhet. A különböző típusú **parsingMode** , amelyek engedélyezik a rendszer-, csomagolja ki indexer, amely több, mint egy keresési dokumentum-ból:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

> [!IMPORTANT]
> `jsonLines` elemzési mód nyilvános előzetes verzióban érhető el, és nem éles környezetekben használható. További információkért lásd: [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

## <a name="one-to-many-document-key"></a>-A-többhöz dokumentum kulcs
Minden egyes dokumentum, amely megjelenik-e az Azure Search-index egyedileg azonosít egy dokumentum kulcsot. 

Ha nincs elemzési mód van megadva, és ha nincs explicit leképezést az index Azure Search kulcsmező automatikusan [térképek](search-indexer-field-mappings.md) a `metadata_storage_path` kulcsként tulajdonság. Ez a leképezés biztosítja, hogy megjelenik-e minden egyes blob egyedi search-dokumentumként.

Használja a fent felsorolt elemzési mód bármelyikében, amikor egy blob "több" keresési dokumentumokhoz, ami kizárólag alapuló blob metaadatai nem alkalmasak a dokumentum kulcs rendeli hozzá. Ez a korlátozás áthidalható, Azure Search szolgáltatás képes a kulcs létrehozása minden egyes entitás blob kinyert "egy-a-többhöz" dokumentum. Ez a tulajdonság neve `AzureSearch_DocumentKey` és minden kinyert a blob egyedi entitás van adva. Ez a tulajdonság értékének garantáltan minden egyes entitásnak egyedi _blobok között_ és az entitások külön keresési-dokumentumok formájában jelennek meg.

Alapesetben, amikor a kulcsindex mező nincs explicit mezőmegfeleltetései meg van adva a `AzureSearch_DocumentKey` van leképezve, használja a `base64Encode` mezőleképezés függvény.

## <a name="example"></a>Példa
Tegyük fel, hogy az index definícióját a következő mezőket:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

És a blob-tároló blobok az alábbi struktúra használatával:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Mikor hozzon létre egy indexelőt, és állítsa a **parsingMode** való `jsonLines` – bármely kulcsmező, a következő hozzárendelést leképezéseket a rendszer implicit módon alkalmazza explicit mező megadása nélkül
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

A telepítő az Azure Search-index a következő információkat (base64-kódolású azonosító kivonatosan lerövidítettük) tartalmazó fog eredményezni.

| id | hőmérséklet | pressure | időbélyeg |
|----|-------------|----------|-----------|
| aHR0... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Leképezés index kulcsmező. egyéni mező

Feltéve, hogy az azonos index definícióját az előző példával, mondja ki a blobtároló blobok az alábbi struktúrával rendelkezik:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Az indexelő létrehozásakor `delimitedText` **parsingMode**, előfordulhat, hogy természetes kulcsmező mezőleképezés függvényt az alábbiak szerint állíthatja miatt:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Azonban ez a leképezés fog _nem_ eredményez 4 dokumentum jelenik meg az index, mert a `recordid` mező, nem egyedi _blobok között_. Ezért javasoljuk, győződjön meg arról, hogy a alkalmazni az implicit mezőleképezés felhasználása a `AzureSearch_DocumentKey` tulajdonságot "egy-a-többhöz" elemzési módhoz kulcsindex mezőjére.

Ha szeretné beállítani egy explicit mezőleképezés, ellenőrizze, hogy a _sourceField_ nem azonos azzal, ha minden egyes **közötti összes BLOB**.

> [!NOTE]
> A által használt megközelítés `AzureSearch_DocumentKey` annak biztosítására, hogy egyedisége kinyert entitásonként változhat, és ezért nem támaszkodhat a hozzá tartozó érték az alkalmazás igényeinek megfelelően.

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Search szolgáltatásban](search-indexer-overview.md)
+ [Az Azure Search szolgáltatással az Azure Blob Storage indexelése](search-howto-index-json-blobs.md)
+ [CSV-blobok indexelése az Azure Search blob indexelőjével](search-howto-index-csv-blobs.md)
+ [JSON-blobok indexelése az Azure Search blob indexelőjével](search-howto-index-csv-blobs.md)

## <a name="NextSteps"></a>Következő lépések
* Azure Search kapcsolatos további információkért tekintse meg a [keresési szolgáltatás oldalát](https://azure.microsoft.com/services/search/).