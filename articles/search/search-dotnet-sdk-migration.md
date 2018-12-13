---
title: Az Azure Search .NET SDK 3 – Azure Search verzió frissítése
description: Kód áttelepítése az Azure Search .NET SDK 3-as verziójú régebbi verzióit. Megtudhatja, Miben változott, és melyik programkód módosítása.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4acf609ca1f81e69babfa1a319b43e20e84a8395
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317253"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Az Azure Search .NET SDK 3-as verziójú frissítését
Ha 2.0 – előzetes verzió vagy a régebbi verziót használja a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít az alkalmazás használhatja a 3-as verziójú frissítését.

Az SDK-t, beleértve a példákat általános bemutatóért lásd: [használata az Azure Search .NET-alkalmazásból](search-howto-dotnet-sdk.md).

Az Azure Search .NET SDK 3-as verziója néhány módosítást korábbi verzióit tartalmazza. Ezek a leginkább kis, így a kód módosítása csak minimális erőfeszítéssel elvégzéséhez szükséges. Lásd: [frissítésére lépéseket](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatához.

> [!NOTE]
> Verzió 1.0.2-preview használata vagy a régebbi, frissítsen a első és majd frissítsen a 3-as verziójú 1.1-es verzió. Lásd: [az Azure Search .NET SDK 1.1-es verziójának frissítése](search-dotnet-sdk-migration-version-1.md) útmutatást.
>
> Az Azure Search-szolgáltatáspéldányhoz több REST API-verziók, többek között a legújabb buildszám támogatja. Továbbra is verzióját használja, amikor már nem a legújabb buildszám, de azt javasoljuk, hogy a legújabb verzió használatához kódok migrálása. A REST API használata esetén az api-version paraméter használatával minden kérésben meg kell adnia az API-verziót. A .NET SDK használatával, az SDK-t használ a verzióját határozza meg a megfelelő REST API-verzió. Ha egy régebbi SDK-t használja, továbbra is futtassa, hogy a kód módosítása nélkül, akkor is, ha a szolgáltatás frissítése egy újabb API-verzió támogatja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>3. verzió újdonságai
Az Azure Search .NET SDK 3-as verziója a legújabb célozza meg benne az Azure Search REST API, kifejezetten 2016-09-01 általánosan elérhető verzióját. Ez lehetővé teszi számos új funkciót, az Azure Search .NET-alkalmazásból, többek között a következőket használja:

* [Egyéni elemzők](https://aka.ms/customanalyzers)
* [Az Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) és [Azure Table Storage](search-howto-indexing-azure-tables.md) indexelő támogatása
* Az indexelő testreszabási keresztül [mezőleképezéseivel](search-indexer-field-mappings.md)
* Ahhoz, hogy biztonságos egyidejű frissítése index definíciók, indexelők és adatforrások támogatja az ETag
* Index Meződefiníciók deklaratív kialakítása a modellosztály dekorálása és az új `FieldBuilder` osztály.
* .NET Core- és hordozható .NET-profil 111 támogatása

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Először frissítse a NuGet referenciája `Microsoft.Azure.Search` vagy a NuGet Package Manager konzol segítségével vagy a jobb gombbal a projekt hivatkozásait a és a Visual Studióban válassza a "Kezelése NuGet Packages …".

Miután NuGet töltött le az új csomagok és azok függőségeit, a projekt újraépítéséhez. Attól függően, hogyan épül fel a kódot akkor előfordulhat, hogy építse újra sikeresen megtörtént. Ha igen, akkor készen!

A build sikertelen lesz, ha egy build hiba történt a következőhöz hasonlóan kell megjelennie:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A következő lépés, hogy javítsa a build-hibát. Lásd: [használhatatlanná tévő változásai a 3-as verziójú](#ListOfChanges) mi okozza a hibát, és annak megoldásáról.

Elavult módszerek és a Tulajdonságok kapcsolatban további build figyelmeztetések jelenhetnek meg. A figyelmeztetésekkel kapcsolatos helyett az elavult funkció tartalmazza. Például, ha az alkalmazás használja a `IndexingParameters.Base64EncodeKeys` tulajdonságot használja, érdemes egy figyelmeztetés fog megjelenni arról, hogy a `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Felépítési hibák már rögzített, ha módosításokat végezheti el az alkalmazást, hogy új funkciók előnyeinek kihasználása, ha szeretné. Az SDK-t az új funkciók részletes leírásuk [What's new in 3-as verziójú](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>A 3-as verziójú használhatatlanná tévő változásai
Hiba a használhatatlanná tévő változásai a 3-as verziója, amely lehet szükség a kód kis számú mellett az alkalmazás újraépítését változik.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient visszatérési típusa
A `Indexes.GetClient` metódus egy új visszatérési típussal rendelkezik. Korábban a visszaadott `SearchIndexClient`, azonban ez megváltozott a `ISearchIndexClient` 2.0 – előzetes verzió, és hogy a módosítás végrehajtott módosítások megjelennek a 3-as verziójú. Ez az szeretné utánzása támogatásához a `GetClient` az egységteszteket utánzatként funkcionáló megvalósítását visszaadó metódus `ISearchIndexClient`.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Módosíthatja a build hibák elhárításához:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>Amelyek AnalyzerName, adattípus és mások már nem konvertálható erre a karakterláncok
Nincsenek az Azure Search .NET SDK, amely származik a számos különböző `ExtensibleEnum`. Korábban ezek a típusok is az összes implicit módon válthatók be `string`. Egy hiba felfedezett azonban a `Object.Equals` ezeket az osztályokat, és ez implicit konverzió letiltása szükséges hiba kijavítása megvalósítása. Az explicit átalakításhoz `string` továbbra is engedélyezett.

#### <a name="example"></a>Példa
Ha a kód a következőhöz hasonló:

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

Módosíthatja a build hibák elhárításához:

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

### <a name="removed-obsolete-members"></a>Elavult a tagok eltávolítása

Látni kapcsolódó fordítási hibákat módszerek és a tulajdonságok lettek megjelölve elavultként verziójában, 2.0 – előzetes verzió, és ezt követően eltávolítva a 3-as verziójú. Ha ilyen hibákat észlel, a következő azok megoldását:

- Ha ez a konstruktor használja: `ScoringParameter(string name, string value)`, használja helyette a erre: `ScoringParameter(string name, IEnumerable<string> values)`
- Ha használta a `ScoringParameter.Value` tulajdonsága a `ScoringParameter.Values` tulajdonság vagy a `ToString` metódus helyett.
- Ha használta a `SearchRequestOptions.RequestId` tulajdonsága a `ClientRequestId` tulajdonság helyett.

### <a name="removed-preview-features"></a>Eltávolított előzetes verziójú funkciók

Ha a 3-as verzió frissítése a 2.0 – előzetes verzió, vegye figyelembe, hogy JSON-t és a CSV-elemzési Blob-indexelők támogatása el lett távolítva, mivel ezek a szolgáltatások egyelőre előzetes verzióként. Pontosabban, a következő módszerek egyikét a `IndexingParametersExtensions` osztály el lett távolítva:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás egy rögzített függőségi ezekről a funkciókról, nem tudja frissíteni az Azure Search .NET SDK 3. verzió. Továbbra is 2.0 – előzetes verzió használatára. Ugyanakkor Kérjük vegye figyelembe, hogy **éles üzemi alkalmazások pedig az SDK-k előzetes verzió használata nem ajánlott**. Előzetes verziójú funkciók csak tesztelési és változhat.

## <a name="conclusion"></a>Összegzés
Ha részletesebb tájékoztatást az Azure Search .NET SDK van szüksége, tekintse meg a [.NET útmutató](search-howto-dotnet-sdk.md).

Szívesen fogadjuk a visszajelzéseket az SDK-val. Ha problémákat tapasztal, nyugodtan megszüntetését a Súgó a [Azure Search MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a megkereséseit is fájl a [Azure .NET SDK GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címe és az "[az Azure Search]" előtagot.

Köszönjük, hogy az Azure Search használatával!
