---
title: Az Azure Search .NET SDK 3 verzió frissítése |} Microsoft Docs
description: Az Azure Search .NET SDK 3 verzió frissítése
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: 161d22e0ff4ec4ab28107919a80ecc48cd027967
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Az Azure Search .NET SDK 3 verzió frissítése
Ha szeretne megtudni a 2.0-preview vagy a régebbi verzióját a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít frissíteni az alkalmazást a 3-as verzió használatára.

Az SDK, beleértve a példákat általános útmutatást lásd: [használata az Azure Search .NET-alkalmazás](search-howto-dotnet-sdk.md).

Az Azure Search .NET SDK 3-as verziója korábbi verzióihoz képest végrehajtott egyes módosításokat tartalmazza. Ezek a legtöbbször kisebb, így a kód módosítása elvégzéséhez szükséges csak csatlakozhatnak. Lásd: [frissítésének lépései](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatára.

> [!NOTE]
> Verzió 1.0.2-preview használata vagy régebbi, első, majd frissítsen a 3-as verziójú 1.1-es verziójával kell frissítenie. Lásd: [az Azure Search .NET SDK 1.1-es verziójának frissítése](search-dotnet-sdk-migration-version-1.md) utasításokat.
>
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja. A REST API használata esetén minden kérelemben keresztül az api-version paramétert meg kell adnia az API-verzió. A .NET SDK használatával, az SDK-t használ verziója határozza meg a megfelelő REST API-verzióra. Ha egy régebbi SDK használ, továbbra is futtassa ezt a kódot módosítások nélküli, még akkor is, ha a szolgáltatás frissítése újabb API-verzió támogatja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>3. verzió újdonságai
Az Azure Search .NET SDK 3-as verziója célja a legújabb az Azure Search REST API, kifejezetten 2016-09-01 általánosan elérhető verziójának. Ez lehetővé teszi számos új szolgáltatást nyújt az Azure Search .NET-alkalmazás, többek között a következőket használja:

* [Egyéni elemzők](https://aka.ms/customanalyzers)
* [Az Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) és [Azure Table Storage](search-howto-indexing-azure-tables.md) indexelő támogatása
* Az indexelő testreszabási keresztül [hozzárendelések mezőben](search-indexer-field-mappings.md)
* Ahhoz, hogy biztonságos egyidejű frissítésére index definíciók, az indexelők és az adatforrások támogatja az ETag-EK
* Index mező definíciók deklarációval létrehozása a modell osztály dekoráció és az új támogatása `FieldBuilder` osztály.
* A .NET Core és a hordozható .NET-profil 111 támogatása

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítésének lépései
Először frissítse a NuGet referencia `Microsoft.Azure.Search` vagy a NuGet-Csomagkezelő konzol használatával vagy az csomagot jobb gombbal a projekt hivatkozásait, majd válassza a "Kezelése NuGet csomagjainak..." a Visual Studióban.

Miután NuGet töltött le az új csomagok és a függőségek, a projekt újraépítéséhez. Attól függően, hogy a kód hogyan épül akkor előfordulhat, hogy újraépítése sikeresen. Ha igen, most készen áll!

Ha a build sikertelen, a következőhöz hasonló egy összeállítási hibát kell megjelennie:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

A következő lépés a build hiba kijavításához. Lásd: [módosítások Megtörje a 3-as verziójú](#ListOfChanges) mi okozza a hibát, és miként lehet elhárítani.

Elavult metódusokra vagy tulajdonságokra kapcsolatban további build figyelmeztetések jelenhetnek meg. A figyelmeztetésekkel kapcsolatos használata helyett az elavult funkció tartalmazza. Például, ha az alkalmazás által a `IndexingParameters.Base64EncodeKeys` tulajdonság, kell megjelenik egy figyelmeztetés, amely szerint `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Build hibák megszüntetése után módosíthatja a új funkcióinak kihasználásához, ha az alkalmazáshoz. Az SDK-t az új funkciói részletes leírást talál [What's new in 3-as verziójú](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>3-as verziójú megtörje változásai
Van néhány módosítások megtörje a 3-as verzió, amelyre szüksége lehet a kód mellett az alkalmazás újraépítése módosítja.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient visszatérési típusa
A `Indexes.GetClient` módszer új visszatérési értékkel rendelkezik. Korábban visszaadott `SearchIndexClient`, de ez értékre változott az `ISearchIndexClient` 2.0-előzetes verzióját, és hogy módosítás hordoz magában, ha a 3-as verziójú. Ez az szeretné mock támogatásához a `GetClient` egység tesztek utánzatait végrehajtásának visszaadó metódus `ISearchIndexClient`.

#### <a name="example"></a>Példa
Ha a kód így néz ki:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Módosíthatja a build hibák elhárításához:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>A rendszer AnalyzerName, adattípus és mások már nem konvertálható erre a karakterláncok
Az az Azure Search .NET SDK, amelyek a számos különböző `ExtensibleEnum`. Korábban ezek a típusok minden volt konvertálható erre írja be a `string`. Azonban az észlelt hiba a `Object.Equals` ezeket az osztályokat, és ez implicit konverzió letiltása szükséges hiba kijavítása implementációja. Az explicit átalakításhoz `string` rendszer nem tagadja meg.

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

### <a name="removed-obsolete-members"></a>Eltávolított elavult tagok

Láthatja, kapcsolódó fordítási hibákat módszerek vagy a verziójával elavultként lettek megjelenítve, 2.0-kép és a 3-as verziójú később eltávolított tulajdonságokhoz. Ha ilyen hibákba ütközik, ez megoldásával:

- Ha ez a konstruktor: `ScoringParameter(string name, string value)`, használja helyette a erre: `ScoringParameter(string name, IEnumerable<string> values)`
- Ha használta a `ScoringParameter.Value` tulajdonság, használja a `ScoringParameter.Values` tulajdonság vagy a `ToString` metódus helyett.
- Ha használta a `SearchRequestOptions.RequestId` tulajdonság, használja a `ClientRequestId` tulajdonság helyette.

### <a name="removed-preview-features"></a>Az eltávolított előzetes verziójú funkciók

Ha a 3-as verziójú 2.0-Preview verziót frissíti, vegye figyelembe, hogy JSON és a fürt megosztott kötetei szolgáltatás támogatja a Blob indexelők elemzése el lett távolítva, mert ezek a funkciók még még csak előzetes verziójúak. Pontosabban, a következő módszerek egyikét a `IndexingParametersExtensions` osztály eltávolításra került:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás erős függőség az ezen szolgáltatásoktól, csak akkor térnek át az Azure Search .NET SDK 3-as verziója. Továbbra is a 2.0-előzetes verzióját használja. Azonban adja a következőket kell figyelembe venni, hogy **megtekintés SDK-k éles környezetben nem javasoljuk**. Előzetes verziójú funkciók csak tesztelési és módosíthatja.

## <a name="conclusion"></a>Összegzés
Ha további információk az Azure Search .NET SDK van szüksége, tekintse meg a [.NET – útmutató](search-howto-dotnet-sdk.md).

Az SDK-val szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megkérdezi a Súgó a [Azure Search MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a problémát fájl is a [Azure .NET SDK GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címének és a "[az Azure Search]" előtag.

Köszönjük, hogy az Azure Search használatával!
