---
title: Bemenet hozzárendelése kimeneti mezőkhöz
titleSuffix: Azure Cognitive Search
description: Kinyerheti és gazdagíthatja a forrásadatmezőket, és leképezheti az Azure Cognitive Search index kimeneti mezőihez.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280969"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>A i-dúsított mezők leképezése kereshető indexhez

Ebből a cikkből megtudhatja, hogyan képezheti le a bővített bemeneti mezőket a kereshető index kimeneti mezőihez. Miután [definiált egy skillsetet,](cognitive-search-defining-skillset.md)le kell képeznie minden olyan szakértelem kimeneti mezőit, amely közvetlenül hozzájárul egy adott mezőhöz a keresési indexben. 

Kimeneti mezőleképezések szükségesek a bővített dokumentumok tartalmának az indexbe való áthelyezéséhez.  A dúsított dokumentum valójában az információk fája, és bár az indexben támogatja az összetett típusokat, néha érdemes a dúsított fából származó információkat egyszerűbb típussá (például karakterláncok tömbjé) átalakítani. A kimeneti mezőleképezések lehetővé teszik adatalakzat-átalakítások elvégzését az adatok összeolvasztásával.

## <a name="use-outputfieldmappings"></a>OutputFieldMappings használata
Mezők leképezéséhez adja hozzá `outputFieldMappings` az indexelő definícióját az alábbi módon:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

A kérelem törzse a következőképpen épül fel:

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

Minden kimeneti mező leképezéséhez állítsa be az adatok helyét a bővített dokumentumfában (sourceFieldName), valamint a mező nevét az indexben hivatkozottmódon (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Adatok összeolvasztása összetett típusokból 

A sourceFieldName elérési útja egy elemet vagy több elemet jelölhet. A fenti példában egyetlen numerikus ```/document/content/sentiment``` értéket, míg ```/document/content/organizations/*/description``` több szervezeti leírást jelöl. 

Azokban az esetekben, ahol több elem van, azok "összeolvasztott" egy tömb, amely tartalmazza az egyes elemeket. 

Konkrétabban, a ```/document/content/organizations/*/description``` példában a *leírások* mezőben lévő adatok a leírások sík tömbjének tűnnek, mielőtt indexelnék őket:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Ez egy fontos elv, ezért egy másik példát is mutatunk. Képzelje el, hogy a dúsító fa részeként összetett típusok ból álló tömbvan. Tegyük fel, hogy van egy customEntities nevű tag, amely az alábbiakban ismertetetthez hasonló összetett típusok tömbjével rendelkezik.

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

Tegyük fel, hogy az index rendelkezik egy Collection (Edm.String) típusú "betegségek" nevű mezővel, ahol az entitások egyes neveit szeretné tárolni. 

Ez könnyen elvégezhető a\*" " szimbólum használatával, az alábbiak szerint:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Ez a művelet egyszerűen "összeolvasztja" az egyéni entitások elemeinek nevét egyetlen karakterlánctömbbe, mint ez:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>További lépések
Miután a bővített mezőket kereshető mezőkhöz rendelte, az indexdefiníció részeként beállíthatja az egyes kereshető mezők [mezőattribútumait.](search-what-is-an-index.md)

A mezőleképezésről további információt az [Azure Cognitive Search indexelői mezőleképezései című témakörben talál.](search-indexer-field-mappings.md)
