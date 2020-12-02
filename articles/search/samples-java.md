---
title: Java-példák
titleSuffix: Azure Cognitive Search
description: A Java-hoz készült Azure .NET SDK-t használó Azure Cognitive Search bemutató Java-kód mintáinak megkeresése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 10dff18f7b9db7273fcd6ec92bcca5970bb83b08
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510369"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-kódok az Azure Cognitive Search

Ismerje meg az Azure-Cognitive Search funkcióit és funkcióit bemutató Java-kódok mintáit. Az elsődleges Tárházak a következők:

| Adattár | Leírás |
|------------|-------------|
| [Azure-SDK-for-Java/Tree/Master/SDK/keresés/Azure-Search-Documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Az Azure SDK csapata által előállított minták, amelyek az SDK-ban a Azure.Search.Documents-ügyfél kódtárat szállítanak. A különböző API-k meghívásához tekintse át az [egységre vonatkozó teszteket](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) is az ügyféloldali kódtár számára. |
| [Azure-Samples/Azure-Search-Java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Az útmutató cikkeihez kapcsolódó kód mintái. A **tárházban lévő minták még nem frissültek a Javához készült Azure SDK használatára**. Ezek a minták jelenleg REST API-kat hívhatnak a Java-kódban.|

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=java&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="java-sdk-samples"></a>Java SDK-minták

A Javához készült Azure SDK számos mintát és egy [első lépéseket tartalmazó oldalt](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) tartalmaz, amely a csomagok telepítését ismerteti. A lap a példák széles körét is felsorolja. Az alábbiakban felsorolunk néhány gyakori műveletet az Ön kényelme érdekében.

| Példák | Leírás |
|---------|-------------|
| [Keresési index létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Bemutatja, hogyan hozhat létre [keresési indexeket](search-what-is-an-index.md). |
| [Szinonimák létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Bemutatja, hogyan hozhatók létre [szinonima térképek](search-synonyms.md).  |
| [Indexer-létrehozás keresése](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Bemutatja, hogyan hozhat létre [Indexelő](search-indexer-overview.md). |
| [Keresési indexelő adatforrás létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Bemutatja, hogyan hozhat létre indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő-alapú indexeléséhez szükségesek. |
| [Készségkészlet létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Azt mutatja be, hogyan hozhatók létre indexelő [szakértelmével](cognitive-search-working-with-skillsets.md) , és melyek az indexelés során mesterséges intelligencia-alapú dúsítást végeznek. |
| [Dokumentumok betöltése](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Bemutatja, hogyan lehet feltölteni vagy egyesíteni a dokumentumokat egy indexbe egy [adatimportálási](search-what-is-data-import.md) műveletben. |
| [Lekérdezés szintaxisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Bemutatja, hogyan állíthat be egy [alapszintű lekérdezést](search-query-overview.md). |

## <a name="documentation-samples"></a>Dokumentáció – minták

Az alábbi példákban az [Azure Cognitive Search dokumentációjában](./index.yml)szerepel egy kapcsolódó cikk.

| Példák | Leírás | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet a javában](search-get-started-java.md). Ez a példa a REST API-kat hívja meg. |
| [Keresés – Java-indexelő – bemutató](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Egy Azure Cosmos DB indexelő használatát mutatja be javában. Ez a példa a REST API-kat hívja meg. |