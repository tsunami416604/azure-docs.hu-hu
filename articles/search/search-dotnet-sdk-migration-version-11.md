---
title: Frissítés a .NET SDK 11-es verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot az Azure Cognitive Search .NET SDK 11-es verziójára régebbi verzióról. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 03d40dcaeaefe01fecbc201cf28dc20c8634af9d
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926671"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Frissítés az Azure Cognitive Search .NET SDK 11-es verziójára

Ha a [.net SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)10,0-es vagy újabb verzióját használja, ez a cikk segítséget nyújt a 11-es verzióra való frissítéshez.

A 11-es verzió egy teljes mértékben újratervezett ügyféloldali kódtár, amelyet az Azure SDK Fejlesztői csapata szabadít fel (az előző verziókat az Azure Cognitive Search fejlesztői csapat hozta létre). A rendszer újratervezte a kódtárat a többi Azure-ügyfélszoftverrel való nagyobb konzisztencia érdekében, az [Azure. Core](https://docs.microsoft.com/dotnet/api/azure.core) és [aSystem.Text.Js](https://docs.microsoft.com/dotnet/api/system.text.json), valamint a gyakori feladatokra vonatkozó ismerős megközelítések megvalósításával.

Az új verzióban megjelenő legfontosabb különbségek a következők:

+ Egy csomag és egy könyvtár több
+ Új csomag neve: `Azure.Search.Documents` helyett `Microsoft.Azure.Search` .
+ Három ügyfél a kettő helyett: `SearchClient` , `SearchIndexClient` ,`SearchIndexerClient`
+ Különböző API-k és kisebb szerkezeti különbségek elnevezése az egyes feladatok egyszerűsítése érdekében

## <a name="package-and-library-consolidation"></a>Csomag-és könyvtár-konszolidáció

A 11-es verzió összevonja a több csomagot és tárat. Az áttelepítés után kevesebb könyvtárat fog kezelni.

+ [Azure.Search.Documents-csomag](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-referenciák az ügyféloldali kódtár számára](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Ügyfelek közötti különbségek

Ha lehetséges, az alábbi táblázat a két verzió közötti ügyféloldali kódtárakat képezi le.

| Műveletek hatóköre | Microsoft. Azure. Search &nbsp; (v10) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| A lekérdezésekhez és az indexek feltöltéséhez használt ügyfél. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Indexekhez, elemzőekhez, szinonimák megfeleltetéséhez használt ügyfél | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Az indexelő, az adatforrások és a szakértelmével által használt ügyfél | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**új**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient`mindkét verzióban létezik, de különböző dolgokat támogat. A 10-es verzióban `SearchIndexClient` Indexek és egyéb objektumok hozhatók létre. A 11-es verzióban a `SearchIndexClient` meglévő indexekkel működik. A kód frissítésekor a félreértések elkerülése érdekében figyelembe kell venni, hogy milyen sorrendben frissülnek az ügyfelek hivatkozásai. A [frissítéshez szükséges lépések](#UpgradeSteps) végrehajtásával csökkentheti a karakterlánc-helyettesítési problémákat.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Elnevezési és egyéb API-eltérések

Az ügyfél-eltérések mellett (a korábban említettek szerint) több más API is átnevezve lett, és bizonyos esetekben újratervezték. Az osztályok nevének különbségeit az alábbiakban összegzi. Ez a lista nem teljes, de az API-t a feladat szerint módosítja, ami hasznos lehet az adott kódrészletek változatának módosításához. Az API-frissítések részletezett listája a GitHubon a [változási naplóban](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) található `Azure.Search.Documents` .

### <a name="authentication-and-encryption"></a>Hitelesítés és titkosítás

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey`(az [előzetes verzió SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) -ban általánosan elérhető szolgáltatásként létezett) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indexek, elemzők, szinonimák térképek

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Mező](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Adattípus](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyze) (más néven `AnalyzerName` `LexicalAnalyzerName` ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (más néven `StandardTokenizerV2` `LuceneStandardTokenizerV2` ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (más néven `TokenizerName` `LexicalTokenizerName` ) |
| [SynonymMap. Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Nincsenek. Hivatkozások eltávolítása a következőre: `Format` . |

A mező-definíciók racionalizálva vannak: a [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), a [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), a [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) új API-k a mezők definícióinak létrehozásához.

### <a name="indexers-datasources-skillsets"></a>Indexelő, adatforrások, szakértelmével

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [Indexelő](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Ügyességi](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Készségkészlet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskillse) |
| [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Adatimportálás

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Lekérdezési definíciók és eredmények

| 10-es verzió | 11-es verzió – egyenértékű |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) vagy [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), attól függően, hogy az eredmény egyetlen dokumentum vagy több. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [SearchOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>A 11-es verzió

Az Azure Cognitive Search ügyféloldali kódtár minden verziója a REST API megfelelő verzióját célozza meg. A REST API a szolgáltatás alapját képezi, és egyedi SDK-k a REST API egy verzióját becsomagolják. .NET-fejlesztőként hasznos lehet áttekinteni [REST API dokumentációt](https://docs.microsoft.com/rest/api/searchservice/) , ha adott objektumokra vagy műveletekre több hátteret szeretne használni.

A 11-es verzió a [2020-06-30 keresési szolgáltatást](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)célozza meg. Mivel a 11-es verzió egy új, az alapoktól kiépített ügyféloldali kódtár is, a fejlesztési erőfeszítések többsége a 10-es verzióra koncentrál, és néhány REST API funkció támogatása továbbra is függőben van.

A 11-es verzió teljes mértékben támogatja a következő objektumokat és műveleteket:

+ Index létrehozása és kezelése
+ Szinonimák leképezésének létrehozása és kezelése
+ Minden lekérdezés típusa és szintaxisa (kivéve a Geo-térbeli szűrőket)
+ Indexelő objektumok és műveletek az Azure-adatforrások indexeléséhez, beleértve az adatforrásokat és a szakértelmével

### <a name="pending-features"></a>Függőben lévő funkciók

A következő 10-es verziójú funkciók még nem érhetők el a 11. verzióban. Ha ezeket a szolgáltatásokat használja, az áttelepítés után tartsa lenyomva a szolgáltatást, amíg azok nem támogatottak.

+ térinformatikai típusok
+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) (bár [ezt a megoldást](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/tests/Samples/FieldBuilder/FieldBuilder.cs)használhatja).
+ [Tudástár](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései

A következő lépések végrehajtásával kezdheti meg a kód áttelepítését a szükséges feladatok első készletének bejárásával, különösen az ügyfél-referenciák tekintetében.

1. Telepítse a [Azure.Search.Documents csomagot](https://www.nuget.org/packages/Azure.Search.Documents/) úgy, hogy a jobb gombbal a projekt hivatkozásaira kattint, és kiválasztja a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

1. Cserélje le a Microsoft. Azure. Search utasításait a következőre:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Cserélje le a [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) -t a [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential).

1. Ügyfél-referenciák frissítése az indexelő szolgáltatással kapcsolatos objektumokhoz. Ha indexelő, adatforrásokat vagy szakértelmével használ, módosítsa az ügyfél [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient)mutató hivatkozásait. Ez az ügyfél a 11-es verzióban új, és nincs előzménye.

1. A lekérdezésekhez és az adatimportáláshoz tartozó ügyfél-referenciák frissítése. A [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) példányait [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient)értékre kell módosítani. A félreértések elkerülése érdekében győződjön meg arról, hogy a következő lépéshez való továbblépés előtt minden példányt elkapjon.

1. Az ügyfél-referenciák frissítése index, indexelő, szinonimák leképezése és elemző objektumok számára. A [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) példányait [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchindexclient)értékre kell módosítani. 

1. A lehető legnagyobb mértékben frissítse az osztályokat, a metódusokat és a tulajdonságokat az új könyvtár API-jai használatához. Az [elnevezési különbségek](#naming-differences) szakasz elindítható, de a [módosítási naplót](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)is áttekintheti.

   Ha nem találja az egyenértékű API-kat, javasoljuk, hogy naplózza a problémát, [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) hogy javítsa a dokumentációt, vagy vizsgálja meg a problémát.

1. Hozza létre újra a megoldást. A felépítési hibák vagy figyelmeztetések kijavítása után további módosításokat végezhet az alkalmazásban, hogy kihasználhassa az [új funkciókat](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>A 11. verzióban feltört változások

A könyvtárak és API-k elsöprő változásai miatt a 11-ös verzióra való frissítés nem triviális, és olyan változást jelent, amely abban az értelemben, hogy a kód már nem lesz kompatibilis a 10-es és a korábbi verziókkal. A különbségek alapos áttekintéséhez tekintse meg a [változási naplót](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) `Azure.Search.Documents` .

A szolgáltatási verziók esetében a 10 – 11 értékre való áttérés a következő viselkedési változásokat mutatja be: 

+ A [BM25 ranking algoritmus](index-ranking-similarity.md) újabb technológiával helyettesíti az előző rangsorolási algoritmust. Az új szolgáltatások ezt az algoritmust automatikusan használják majd. Meglévő szolgáltatások esetén a paramétereket az új algoritmus használatára kell beállítani.

+ A null értékek [rendezett eredményei](search-query-odata-orderby.md) módosultak ebben a verzióban, és először Null érték jelenik meg, ha a rendezés `asc` és az utolsó, ha a rendezés `desc` . Ha kódot írt a null értékek rendezésének kezeléséhez, tekintse át és távolítsa el ezt a kódot, ha már nincs rá szükség.

## <a name="next-steps"></a>További lépések

+ [Azure.Search.Documents-csomag](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Példák a GitHubon](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API-referenciája](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)