---
title: Térkép kognitív keresési dúsított Azure Search-indexek kimeneti mező beviteli mezők |} Microsoft Docs
description: Bontsa ki forrás adatmezők kiegészítése és az Azure Search-index kimeneti mezőinek van leképezve.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 67e4798070a73eebb8f61b0b260e3104e9ae6237
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790950"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Hogyan képezheti bővített mezők kereshető index

Ebből a cikkből megismerheti, hogyan kereshető index kimeneti mezők bővített beviteli mezők hozzárendelése. Ha már [egy skillset definiált](cognitive-search-defining-skillset.md), a kimeneti mezők bármely szakértelem, amely közvetlenül hozzájárul az search-index egy mező értékének meg kell jelölnie. Bővített dokumentumok tartalom áthelyezése az index mező hozzárendelések szükségesek.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata
Képezze le a mezőket, vegye fel `outputFieldMappings` az indexelő definíciójához alább látható módon:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzse a következőképpen épül:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Minden kimeneti mező leképezés, állítsa be a bővített (sourceFieldName) mező nevét, és a mező nevét a index (targetFieldName) hivatkozott.

Az elérési utat egy sourceFieldName elem egy vagy több elem jelenthet. A példában ```/document/content/sentiment``` jelöli egyetlen numerikus érték, amíg ```/document/content/organizations/*/description``` több szervezet leírások jelöli. Azokban az esetekben, ahol több eleme van, ezek vannak "simítva" elemek beállításait tartalmazó tömb. Több konkrétan az a ```/document/content/organizations/*/description``` példa, az adatokat a *leírások* mező néz leírások strukturálatlan tömbje azt lekérdezi indexelése után:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>További lépések
Miután leképezte a bővített mezők a kereshető mezők, beállíthatja az egyes a kereshető mezők a mező attribútumok [az index definícióját részeként](search-what-is-an-index.md).

Mező leképezési kapcsolatos további információkért lásd: [mezőben az Azure Search indexelők hozzárendelések](search-indexer-field-mappings.md).