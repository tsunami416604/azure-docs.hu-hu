---
title: Frissítés az Azure Search .NET SDK 3-as verziójára
titleSuffix: Azure Cognitive Search
description: A kódot az Azure Search .NET SDK 3-as verziójába telepítheti át a régebbi verziókból. További információ az újdonságokról és a kódmódosításokról.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792986"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Frissítés az Azure Search .NET SDK 3-as verziójára

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Ha az [Azure Search .NET SDK](https://aka.ms/search-sdk)2.0-s vagy régebbi verzióját használja, ez a cikk segít az alkalmazás 3-as verzióhasználatára való frissítésében.

Az SDK általánosabb forgatókönyvét példákkal együtt az [Azure Search használata .NET alkalmazásból](search-howto-dotnet-sdk.md)című témakörben talál.

Az Azure Search .NET SDK 3-as verziója tartalmaz néhány módosítást a korábbi verziókhoz. Ezek többnyire kisebb, így a kód módosítása csak minimális erőfeszítést igényel. Az új SDK-verzió használatához a [frissítés lépései](#UpgradeSteps) című témakörben talál útmutatást a kód módosításához.

> [!NOTE]
> Ha az 1.0.2-es vagy régebbi verziót használja, először frissítsen az 1.1-es verzióra, majd frissítsen a 3-as verzióra. További információt [az Azure Search .NET SDK 1.1-es verziójára való frissítés](search-dotnet-sdk-migration-version-1.md) című témakörben talál.
>
> Az Azure Search szolgáltatáspéldány a REST API számos verzióját támogatja, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de azt javasoljuk, hogy telepítse át a kódot a legújabb verzió használatára. A REST API használatakor meg kell adnia az API-verziót minden kérelemben az api-version paraméteren keresztül. A .NET SDK használatakor a használt SDK-verzió határozza meg a REST API megfelelő verzióját. Ha egy régebbi SDK-t használ, továbbra is futtathatja a kódot módosítások nélkül, még akkor is, ha a szolgáltatás egy újabb API-verzió támogatásához lett frissítve.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>A 3-as verzió újdonságai
Az Azure Search .NET SDK 3-as verziója az Azure Search REST API legújabb, általánosan elérhető verzióját célozza meg, különösen a 2016-09-01-es verziót. Ez lehetővé teszi az Azure Search számos új funkciójának használatát egy .NET alkalmazásból, többek között a következőket:

* [Egyéni elemzők](https://aka.ms/customanalyzers)
* [Az Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) és [az Azure Table Storage](search-howto-indexing-azure-tables.md) indexelőjnek támogatása
* Indexelő [testreszabása mezőleképezéseken](search-indexer-field-mappings.md) keresztül
* ETags támogatás lehetővé teszi az indexdefiníciók, indexelők és adatforrások biztonságos egyidejű frissítését
* Támogatja az épület index mező definíciók deklaratív an `FieldBuilder` díszítő modell osztály és az új osztály használatával.
* A .NET Core és a .NET Portable Profile 111 támogatása

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse NuGet-referenciáját a NuGet Csomagkezelő konzol `Microsoft.Azure.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. Attól függően, hogy a kód épül, előfordulhat, hogy sikeresen újraépül. Ha igen, akkor készen áll az indulásra!

Ha a build sikertelen, a következőhez hasonló buildhibát kell látnia:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A következő lépés a buildhiba kijavítása. A [3-as verzió módosításai című témakörben](#ListOfChanges) részletesen tudni szeretné, hogy mi okozza a hibát, és hogyan javíthatja ki.

Az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további buildfigyelmeztetések jelenhetnek meg. A figyelmeztetések utasításokat tartalmaznak arra vonatkozóan, hogy mit használjanak az elavult szolgáltatás helyett. Ha például az alkalmazás `IndexingParameters.Base64EncodeKeys` használja a tulajdonságot, figyelmeztetést kell kapnia, amely szerint a`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Miután kijavította a buildelési hibákat, módosíthatja az alkalmazást, hogy kihasználhassa az új funkciók előnyeit, ha szeretné. Az SDK új funkcióit a [3-as verzió újdonságai](#WhatsNew)részletezik.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>A változások megtörése a 3-as verzióban
Van egy kis számú törés változások 3-as verzió, amely szükség lehet a kód módosítása mellett az alkalmazás újjáépítése.

### <a name="indexesgetclient-return-type"></a>Indexek.GetClient visszatérési típusa
A `Indexes.GetClient` metódus nak új visszatérési típusa van. Korábban visszatért `SearchIndexClient`, de ez `ISearchIndexClient` a 2.0-s verziójú előzetes verzióban megváltozott, és ez a változás a 3-as verzióra változik. Ez azokat az ügyfeleket `GetClient` támogatja, akik az egységtesztek `ISearchIndexClient`módszerét szeretnék kigúnyolni a modell implementációjának visszaküldésével.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

A buildelési hibák kijavításához módosíthatja:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType és mások már nem implicit módon átválthatók karakterláncokra
Az Azure Search .NET SDK számos típusa `ExtensibleEnum`származik. Korábban ezek a típusok mind `string`implicit módon átválthatók a típusra . Azonban egy hibát fedeztek `Object.Equals` fel a végrehajtás ezen osztályok, és a hiba javítása szükséges letiltása implicit átalakítás. Az explicit `string` átalakítás továbbra is engedélyezett.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

A buildelési hibák kijavításához módosíthatja:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Elavult tagok eltávolítása

A 2.0-s verziójú előzetes verzióban elavultként megjelölt, majd a 3-as verzióban eltávolított metódusokkal vagy tulajdonságokkal kapcsolatos buildhibák jelenhetnek meg. Ha ilyen hibákkal találkozik, a következőképpen oldhatja meg őket:

- Ha ezt a konstruktotort használta: `ScoringParameter(string name, string value)`, használja inkább ezt:`ScoringParameter(string name, IEnumerable<string> values)`
- Ha a `ScoringParameter.Value` tulajdonságot használta, `ScoringParameter.Values` használja `ToString` inkább a tulajdonságot vagy a metódust.
- Ha a `SearchRequestOptions.RequestId` tulajdonságot használta, `ClientRequestId` használja inkább a tulajdonságot.

### <a name="removed-preview-features"></a>Az előzetes verzió funkcióinak eltávolítása

Ha a 2.0-s verziójú előzetes verzióról 3-as verzióra frissít, vegye figyelembe, hogy a JSON és a CSV-elemzési támogatást a Blob Indexerek eltávolították, mivel ezek a funkciók még előzetes verzióban vannak. Pontosabban, az `IndexingParametersExtensions` osztály következő módszereit eltávolították:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás szigorú függőséget gyakorol ezektől a funkcióktól, nem fog tudni frissíteni az Azure Search .NET SDK 3-as verziójára. Továbbra is használhatja a 2.0-s verziójú előzetes verziót. Kérjük azonban, vegye figyelembe, hogy **nem javasoljuk az előzetes SDK-k használatát az éles alkalmazásokban.** Az előnézeti funkciók csak kiértékelésre szolgálnak, és változhatnak.

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége az Azure Search .NET SDK használatával kapcsolatos részletekről, olvassa el a [.NET útmutatóját.](search-howto-dotnet-sdk.md)

Örömmel fogadjuk az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, nyugodtan kérjen tőlünk segítséget [stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, az [Azure .NET SDK GitHub tárházban](https://github.com/Azure/azure-sdk-for-net/issues)nyújthat be problémát. Győződjön meg arról, hogy előtag a probléma címe a "[Azure Search]".

Köszönjük, hogy az Azure Search-et használja!
