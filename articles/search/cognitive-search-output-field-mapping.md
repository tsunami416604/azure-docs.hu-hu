---
title: Bemenet hozzárendelése a kimeneti mezőkhöz
titleSuffix: Azure Cognitive Search
description: Kinyerheti és gazdagíthatja a forrásadatok mezőit, és leképezheti a kimeneti mezőket egy Azure Cognitive Search indexben.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280969"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-gazdagított mezők leképezése kereshető indexre

Ebből a cikkből megtudhatja, hogyan jelenítheti meg a bővíthető beviteli mezőket egy kereshető index kimeneti mezőihez. Miután [meghatározta a készségkészlet](cognitive-search-defining-skillset.md), le kell képeznie minden olyan képesség kimeneti mezőjét, amely közvetlenül járul hozzá az értékekhez a keresési index egy adott mezőjében. 

A bővített dokumentumokból származó tartalomnak az indexbe való áthelyezéséhez kimeneti mezők hozzárendelése szükséges.  A dúsított dokumentum valójában az információk fája, és bár az index összetett típusai is támogatottak, időnként előfordulhat, hogy a dúsított fában lévő adatokat egy egyszerű típusra kívánja átalakítani (például karakterláncok tömbje). A kimeneti mezők leképezése lehetővé teszi az adatalakzatok átalakításának elvégzését az információk összeolvasztásával.

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

Minden egyes kimeneti mező leképezéséhez állítsa be a dúsított dokumentum fájában (sourceFieldName) található adatokat, valamint a mező nevét az indexben hivatkozott módon (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Információk összeolvasztása összetett típusokból 

Egy sourceFieldName elérési útja egy vagy több elemet is jelenthet. A fenti példában a ```/document/content/sentiment``` egyetlen numerikus értéket jelöl, míg ```/document/content/organizations/*/description``` több szervezeti leírást is képvisel. 

Azokban az esetekben, ahol több elem is van, azok egy tömbbe kerülnek, amely tartalmazza az egyes elemeket. 

Pontosabban, a ```/document/content/organizations/*/description``` például a *leírások* mezőben lévő információ a leírások lapos tömbjét fogja kinézni, mielőtt bejelentkezne az indexelésbe:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Ez fontos elv, ezért egy másik példát is biztosítunk. Képzelje el, hogy az összetett típusok tömbje a dúsítási fa része. Tegyük fel, hogy van egy customEntities nevű tag, amely az alább leírtak szerint összetett típusokat tartalmaz.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Tegyük fel, hogy az indexnek van egy "diseases" nevű mezője (EDM. String), ahol az entitások nevét tárolni szeretné. 

Ezt egyszerűen megteheti a "\*" szimbólum használatával, a következőképpen:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Ez a művelet egyszerűen "lelapul" a customEntities elemek mindegyikének egyetlen tömbje a következőhöz hasonlóan:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Következő lépések
Miután leképezte a dúsított mezőket a kereshető mezőkre, az [index definíciójának részeként](search-what-is-an-index.md)megadhatja az egyes kereshető mezőkhöz tartozó mezőtulajdonságokat.

További információ a mezők hozzárendeléséről: [mező-hozzárendelések az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatásban.
