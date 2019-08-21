---
title: A kognitív keresések gazdagított beviteli mezőinek megjelenítése a kimeneti mezőkhöz – Azure Search
description: Kinyerheti és gazdagíthatja a forrásadatok mezőit, és leképezheti a Azure Search indexben lévő kimeneti mezőket.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1d1ef7be83601a1ccf09ac52f87d0fcc953c605c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639191"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>A dúsított mezők leképezése kereshető indexre

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a bővíthető beviteli mezőket egy kereshető index kimeneti mezőihez. Miután meghatározta [a készségkészlet](cognitive-search-defining-skillset.md), le kell képeznie minden olyan képesség kimeneti mezőjét, amely közvetlenül járul hozzá az értékekhez a keresési index egy adott mezőjében. A bővített dokumentumokból az indexbe való áthelyezéshez mező-hozzárendelés szükséges.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata
A mezők leképezéséhez adja `outputFieldMappings` hozzá az indexelő definícióját az alábbi ábrán látható módon:

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

Egy sourceFieldName elérési útja egy vagy több elemet is jelenthet. A fenti ```/document/content/sentiment``` példában egyetlen numerikus érték szerepel, míg ```/document/content/organizations/*/description``` a több szervezet leírását jelöli. Azokban az esetekben, ahol több elem is van, azok egy tömbbe kerülnek, amely tartalmazza az egyes elemeket. A fenti ```/document/content/organizations/*/description``` példában a leírások mezőben szereplő információ a leírások egy lapos tömbje lesz, az indexelés előtt:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>További lépések
Miután leképezte a dúsított mezőket a kereshető mezőkre, az [index definíciójának részeként](search-what-is-an-index.md)megadhatja az egyes kereshető mezőkhöz tartozó mezőtulajdonságokat.

További információ a mezők leképezéséről: [Azure Search indexelő mezőinek leképezése](search-indexer-field-mappings.md).
