---
title: .NET-minták
titleSuffix: Azure Cognitive Search
description: Itt megtalálhatja az Azure Cognitive Search bemutató C#-kód mintáit, amelyek a .NET-ügyfél kódtárait használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: ab6408621616a4be62631391456f73e90fced752
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498999"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET (C#) kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search funkcióit és funkcióit bemutató C#-kódrészleteket. Az elsődleges Tárházak a következők:

| Adattár | Description |
|------------|-------------|
| [Azure-SDK-for-net/SDK/Search/Azure.Search.Documents/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Az Azure SDK csapata által előállított minták, amelyek az SDK-ban a Azure.Search.Documents-ügyfél kódtárat szállítanak. A különböző API-k meghívásához tekintse át az [egységre vonatkozó teszteket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) is az ügyféloldali kódtár számára. |
| [Azure-Samples/Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | A dokumentációban szereplő cikkeket kísérő példák, beleértve [a .net-ügyfél függvénytárának használatát](search-howto-dotnet-sdk.md).|
| [Azure-Samples/Search-DotNet-első lépések](https://github.com/Azure-Samples/search-dotnet-getting-started) | A dokumentációban szereplő gyors útmutatókat és oktatóanyagokat tartalmazó minták.|

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=csharp&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="net-sdk-samples"></a>.NET SDK-minták

A .NET-hez készült Azure SDK számos mintát tartalmaz, valamint egy, az egyes tartalmakat leíró [mintát](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) . Az alábbi lista az Ön kényelmét szolgálja.

| Példák | Description |
|---------|-------------|
| ["Helló világ", szinkron módon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Bemutatja, hogyan hozhat létre ügyfelet, hitelesítheti és kezelheti a hibákat szinkron metódusok használatával.|
| ["Helló világ", aszinkron módon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Bemutatja, hogyan hozhat létre ügyfelet, hitelesítheti és kezelheti a hibákat aszinkron metódusok használatával.  |
| [Szolgáltatási szintű műveletek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Bemutatja, hogyan hozhat létre indexeket, indexelő fájlokat, adatforrásokat, szakértelmével és szinonimákat. Ez a minta azt is bemutatja, hogyan kérheti le a szolgáltatás statisztikáit, és hogyan kérdezheti le az indexeket.  |
| [Indexelési műveletek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Bemutatja, hogyan hajtható végre művelet a meglévő indexben, ebben az esetben az indexben tárolt dokumentumok száma.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | A nem támogatott adattípusok használatának technikáját mutatja be.  |
| [Dokumentumok indexelése (leküldéses modell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | A "leküldéses" modell indexelése, amely JSON-adattartalom küldését teszi lehetővé egy szolgáltatás indexéhez.   |
| [Titkosítási kulcs mintája](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Azt mutatja be, hogy az ügyfél által felügyelt titkosítási kulcs segítségével további védelmi réteget adhat hozzá a bizalmas tartalomhoz.  |

## <a name="documentation-samples"></a>Dokumentáció – minták

Az alábbi példákban az [Azure Cognitive Search dokumentációjában](./index.yml)szerepel egy kapcsolódó cikk.

| Példák | Description |
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Forráskód a [.net Ügyféloldali kódtár használatához](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | A lekérdezési bővítéshez szinonimákat tartalmazó listát kell használni, amely az indexen kívüli, egyeztethető kifejezéseket biztosít. Ez a példa a következő [példában szerepel: szinonimák hozzáadása a C#-ban](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Az indexelő szolgáltatáshoz kapcsolódó kódrészletek mögötti forráskód különböző cikkekben. Ebből a példából megtudhatja, hogyan konfigurálhat olyan indexelő, amely egy ütemtervet, egy mező-hozzárendelést és egy paramétert tartalmaz.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Forráskód az [ügyfél által felügyelt kulcsok adattitkosításhoz való konfigurálásához](search-security-manage-encryption-keys.md) |
| [Az első alkalmazás létrehozása C-ben #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [Oktatóanyag: az első keresési alkalmazás létrehozása](tutorial-csharp-create-first-app.md). Habár a legtöbb minta a konzolos alkalmazások, ez az MVC-példa egy weboldalt használ a Sample Hotels-index előfizetéséhez, amely az alapszintű keresés, a tördelés, az automatikus kiegészítés és a javasolt lekérdezések, dimenziók és szűrők megjelenítésére szolgál. |
| [több adatforrások](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [Oktatóanyaghoz tartozó forráskód: több adatforrásból származó index](tutorial-multiple-data-sources.md). |
|  [optimalizálás – adatindexelés](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [Oktatóanyag: az indexelés optimalizálása a leküldéses API-val](tutorial-optimize-indexing-push-api.md).  |
| [oktatóanyag – AI-gazdagodás](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [Oktatóanyag: AI által generált kereshető tartalom az Azure-blobokból a .net SDK használatával](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Önálló minták és megoldások

| Példák | Description |
|---------|-------------|
| [Azure-Search – Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Forráskód a megnyert megoldásokban felépíthető egyéni képességekhez.  |
| [Tudásbányászati megoldásgyorsító](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | A sablonokkal, a támogatási fájlokkal és az analitikai jelentésekkel a teljes körű tudás-bányászati megoldás prototípusát segíti.  |
| [Covid-19 keresési alkalmazás tárháza](https://github.com/liamca/covid19search) | Forráskód-tárház a Cognitive Search-alapú [Covid-19 keresési alkalmazáshoz](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | További információ a [JFK megoldásról](https://www.microsoft.com/ai/ai-lab-jfk-files). |