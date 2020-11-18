---
title: JavaScript-minták
titleSuffix: Azure Cognitive Search
description: Keresse meg az Azure Cognitive Search bemutató JavaScript-kód mintáit, amelyek a JavaScripthez készült Azure .NET SDK-t használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686723"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search funkcióit és funkcióit bemutató JavaScript-kód mintáit. Az elsődleges Tárházak a következők:

| Adattár | Leírás |
|------------|-------------|
| [Azure-SDK-for-js/Tree/Master/SDK/keresés/keresés – dokumentumok](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Az Azure SDK csapata által előállított minták, amelyek az SDK-ban a Azure.Search.Documents-ügyfél kódtárat szállítanak. A különböző API-k meghívásához tekintse át az [egységre vonatkozó teszteket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) is az ügyféloldali kódtár számára. |
| [Azure-Samples/Azure-Search-JavaScript-Samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Az útmutató cikkeihez kapcsolódó kódrészletek, beleértve a gyors üzembe helyezést [: keresési index létrehozása a JavaScriptben](search-get-started-javascript.md).|

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=csharp&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="javascript-sdk-samples"></a>JavaScript SDK-minták

A Javához készült Azure SDK számos mintát és egy [első lépéseket tartalmazó oldalt](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) tartalmaz, amely a csomagok telepítésére, az ügyfél beállítására és a hibaelhárításra terjed ki. Az oldal az alábbi, az Ön kényelmét szolgáló példákat is ismerteti.

| Példák | Leírás |
|---------|-------------|
| [indexek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. Ez a minta egy szolgáltatási statisztikai mintát is tartalmaz. |
| [dataSourceConnections (indexek esetében)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [indexelők](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat, állíthat vissza és törölhet [Indexelő](search-indexer-overview.md).|
| [Készségkészlet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet olyan [szakértelmével](cognitive-search-working-with-skillsets.md) , amelyek indexelő, valamint az indexelés során mesterségesen alapuló dúsítást végeznek. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |
| [Lekérdezések](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | A lekérdezések csak olvashatók. Ez a minta lekérdezés a Microsoft által üzemeltetett nyilvános indexen hajtható végre.  |

## <a name="typescript-samples"></a>Írógéppel minták

Az SDK emellett olyan írógéppel-példákat is tartalmaz, amelyek az Ön kényelme érdekében itt láthatók.

| Példák | Leírás |
|---------|-------------|
| [indexek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. Ez a minta egy szolgáltatási statisztikai mintát is tartalmaz. |
| [dataSourceConnections (indexek esetében)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [indexelők](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat, állíthat vissza és törölhet [Indexelő](search-indexer-overview.md).|
| [Készségkészlet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet olyan [szakértelmével](cognitive-search-working-with-skillsets.md) , amelyek indexelő, valamint az indexelés során mesterségesen alapuló dúsítást végeznek. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |
| [Lekérdezések](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | A lekérdezés-végrehajtást a Microsoft által üzemeltetett írásvédett nyilvános indexen mutatja be.  |

## <a name="documentation-samples"></a>Dokumentáció – minták

Az alábbi példákban az [Azure Cognitive Search dokumentációjában](https://docs.microsoft.com/azure/search/)szerepel egy kapcsolódó cikk.

| Példák | Leírás | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet a JavaScriptben](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Önálló minták

| Példák | Leírás |
|---------|-------------|
| [Azure-Search – reagálás – sablon](https://github.com/dereklegenzoff/azure-search-react-template) | Sablon megválaszolása az Azure Cognitive Search (github.com) |