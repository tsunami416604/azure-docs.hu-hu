---
title: Python-példák
titleSuffix: Azure Cognitive Search
description: Keresse meg az Azure Cognitive Search bemutató Python-kódrészleteket, amelyek a Pythonhoz vagy a REST-hez készült Azure .NET SDK-t használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6e0f3d318cc462b03151d5a4935ae318df46e2c5
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510556"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search funkcióit és funkcióit bemutató Python-kód mintáit. Az elsődleges Tárházak a következők:

| Adattár | Leírás |
|------------|-------------|
| [Azure-SDK-for-Python/Tree/Master/SDK/keresés/Azure-Search-Documents/Samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Az Azure SDK csapata által előállított minták, amelyek az SDK-ban a Azure.Search.Documents-ügyfél kódtárat szállítanak. A különböző API-k meghívásához tekintse át az [egységre vonatkozó teszteket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) is az ügyféloldali kódtár számára. |
| [Azure-Samples/Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples) | Az útmutatókat kísérő kódrészletek, beleértve a gyors üzembe helyezést [: keresési index létrehozása a Pythonban](search-get-started-python.md).|

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=python&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="python-sdk-samples"></a>Python SDK-minták

A Pythonhoz készült Azure SDK számos mintát és egy [első lépések oldalt](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) tartalmaz, amely tartalmazza az előfeltételeket és a csomagok telepítését. A lap az alábbi példákra mutató hivatkozásokat is tartalmaz, amelyek itt láthatók az Ön kényelme érdekében.

| Példák | Leírás |
|---------|-------------|
| [Hitelesítés](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Bemutatja, hogyan konfigurálhat egy ügyfelet, és hogyan végezhet hitelesítést a szolgáltatásban. | 
| [Index Create-Read-Update-DELETE művelet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. |
| [Indexelő létrehozása – olvasás-frissítés – törlési műveletek](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Bemutatja, hogyan lehet [Indexelő](search-indexer-overview.md)létrehozni, frissíteni, beolvasni, listázni, visszaállítani és törölni. |
| [Indexelő adatforrások keresése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [Szinonimák](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |
| [Dokumentumok betöltése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Bemutatja, hogyan lehet feltölteni vagy egyesíteni a dokumentumokat egy indexbe egy [adatimportálási](search-what-is-data-import.md) műveletben. |
| [Egyszerű lekérdezés](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Bemutatja, hogyan állíthat be egy [alapszintű lekérdezést](search-query-overview.md). |
| [Lekérdezés szűrése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Egy [szűrő kifejezés](search-filters.md)beállítását mutatja be. |
| [Dimenzió lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Bemutatja az [aspektusokkal](search-filters-facets.md)való munkát. |

## <a name="documentation-samples"></a>Dokumentáció – minták

Az alábbi példákban az [Azure Cognitive Search dokumentációjában](./index.yml)szerepel egy kapcsolódó cikk.

| Példák | Leírás | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Forráskód a gyors üzembe helyezéshez [: keresési index létrehozása a Pythonban](search-get-started-python.md).  |
| [oktatóanyag – AI-gazdagodás](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [Oktatóanyag: a Python és a AI használatával kereshető tartalmak hozhatók elő az Azure-blobokból](cognitive-search-tutorial-blob-python.md).  |
| [AzureML – egyéni – szakértelem](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Forráskód [például: hozzon létre egy egyéni képességet a Python használatával](cognitive-search-custom-skill-python.md).  |