---
title: AI-gazdagított beviteli mezők leképezése kimeneti mezőkre
titleSuffix: Azure Cognitive Search
description: Kinyerheti és gazdagíthatja a forrásadatok mezőit, és leképezheti a kimeneti mezőket egy Azure Cognitive Search indexben.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 77f378f4e0c4a8e4827523e244f7b18c2a9ba336
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792106"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-gazdagított mezők leképezése kereshető indexre

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a bővíthető beviteli mezőket egy kereshető index kimeneti mezőihez. Miután [meghatározta a készségkészlet](cognitive-search-defining-skillset.md), le kell képeznie minden olyan képesség kimeneti mezőjét, amely közvetlenül járul hozzá az értékekhez a keresési index egy adott mezőjében. A bővített dokumentumokból az indexbe való áthelyezéshez mező-hozzárendelés szükséges.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata
A mezők leképezéséhez adja hozzá `outputFieldMappings` az indexelő definícióhoz az alább látható módon:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzse a következőképpen van strukturálva:

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
Minden egyes kimeneti mező leképezéséhez állítsa be a dúsított mező (sourceFieldName) nevét és a mező nevét az indexben (targetFieldName) hivatkozott módon.

Egy sourceFieldName elérési útja egy vagy több elemet is jelenthet. A fenti példában a ```/document/content/sentiment``` egyetlen numerikus értéket jelöl, míg ```/document/content/organizations/*/description``` több szervezeti leírást is képvisel. Azokban az esetekben, ahol több elem is van, azok egy tömbbe kerülnek, amely tartalmazza az egyes elemeket. Pontosabban, a ```/document/content/organizations/*/description``` például a *leírások* mezőben lévő információ a leírások lapos tömbjét fogja kinézni, mielőtt bejelentkezne az indexelésbe:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Következő lépések
Miután leképezte a dúsított mezőket a kereshető mezőkre, az [index definíciójának részeként](search-what-is-an-index.md)megadhatja az egyes kereshető mezőkhöz tartozó mezőtulajdonságokat.

További információ a mezők hozzárendeléséről: [mező-hozzárendelések az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatásban.
