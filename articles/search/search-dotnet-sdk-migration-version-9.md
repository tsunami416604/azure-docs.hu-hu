---
title: Az Azure Search .NET SDK 9 – Azure Search verzió frissítése
description: Kód áttelepítése az Azure Search .NET SDK 9-es verzió régebbi verzióit. Megtudhatja, Miben változott, és melyik programkód módosítása.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: f540bc304920073bcd823adcf6c9dd47cb2cf93b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159750"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-9"></a>Az Azure Search .NET SDK 9-es verzió frissítése

Ha a 7.0-preview jelű verziót vagy a régebbi verziót használja a [Azure Search .NET SDK](https://aka.ms/search-sdk), ez a cikk segít az alkalmazás használhatja a 9-es verzió frissítése.

> [!NOTE]
> Ha szeretne, amely egyelőre nem általánosan elérhető funkció kiértékelésében 8.0. dátumú előzetes sémaverzióra használatával, ez a cikk utasításait követve 8.0-preview jelű verziót frissíti a korábbi verziók is követheti.

Az SDK-t, beleértve a példákat általános bemutatóért lásd: [használata az Azure Search .NET-alkalmazásból](search-howto-dotnet-sdk.md).

9-es verzió, az Azure Search .NET SDK korábbi verzióinak számos módosításokat tartalmaz. Ezek közül néhányat a rendszer használhatatlanná tévő változásai, de csak követelje meg a kód viszonylag kis módosításait. Lásd: [frissítésére lépéseket](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatához.

> [!NOTE]
> 4.0 – előzetes verzió vagy annál régebbi verzióját használja, ha meg kell először frissítse a 5-ös verzióját, majd utána frissítse a 9-es verzió. Lásd: [frissítését az Azure Search .NET SDK 5-ös verzió](search-dotnet-sdk-migration-version-5.md) útmutatást.
>
> Az Azure Search-szolgáltatáspéldányhoz több REST API-verziók, többek között a legújabb buildszám támogatja. Továbbra is verzióját használja, amikor már nem a legújabb buildszám, de azt javasoljuk, hogy a legújabb verzió használatához kódok migrálása. A REST API használata esetén az api-version paraméter használatával minden kérésben meg kell adnia az API-verziót. A .NET SDK használatával, az SDK-t használ a verzióját határozza meg a megfelelő REST API-verzió. Ha egy régebbi SDK-t használja, továbbra is futtassa, hogy a kód módosítása nélkül, akkor is, ha a szolgáltatás frissítése egy újabb API-verzió támogatja.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>9-es verzió újdonságai
Az Azure Search .NET SDK 9 verzióját célozza meg, a legújabb általánosan elérhető verzióját az Azure Search REST API, kifejezetten a 2019-05-06. Ez lehetővé teszi az Azure Search .NET-alkalmazásból, többek között az alábbi új szolgáltatások használatához:

* [A kognitív keresés](cognitive-search-concept-intro.md) AI szolgáltatás az Azure Search szolgáltatásban használt szöveg kinyerése a képek, blobok és más strukturálatlan adatforrások – győződjön meg arról, hogy több kereshető az Azure Search-index a tartalom bővítését.
* Támogatja a [komplex típusok](search-howto-complex-data-types.md) lehetővé teszi, hogy szinte bármilyen beágyazott JSON-struktúra Azure Search-index a modellt.
* [Az automatikus kiegészítés](search-autocomplete-tutorial.md) egy alternatívát kínál a **javaslat** API a keresés –--beíráskor viselkedésének megvalósítása. Az automatikus kiegészítés "befejezése" szó vagy kifejezés, amely a felhasználó jelenleg éppen gépel.
* [Elemzési mód JsonLines](search-howto-index-json-blobs.md)részét képező Azure BLOB indexelést, egy keresési új dokumentumot hoz létre egy JSON-entitás, amely egy új sor választja el egymástól.

### <a name="new-preview-features-in-version-80-preview"></a>8.0. dátumú előzetes sémaverzióra az új előzetes verziójú funkciók
8.0. dátumú előzetes sémaverzióra, az Azure Search .NET SDK API verziója 2017-11-11-Preview célozza. Ebben a verzióban ugyanazokat funkcióival verzió 9, továbbá:

* [Ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) Szolgáltatásoldali titkosítás inaktív van egy új előzetes verziójú funkció. A beépített titkosítási inaktív a Microsoft felügyeli, mellett további, ahol Ön kizárólagos tulajdonosa, a kulcsok titkosítási réteget is alkalmazhat.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Először frissítse a NuGet referenciája `Microsoft.Azure.Search` vagy a NuGet Package Manager konzol segítségével vagy a jobb gombbal a projekt hivatkozásait a és a Visual Studióban válassza a "Kezelése NuGet Packages …".

Miután NuGet töltött le az új csomagok és azok függőségeit, a projekt újraépítéséhez. Attól függően, hogyan épül fel a kódot akkor előfordulhat, hogy építse újra sikeresen megtörtént. Ha igen, akkor készen!

Ha a build sikertelen, szüksége lesz a minden build hiba elhárításához. Lásd: [használhatatlanná tévő változásai a 9-es verzió](#ListOfChanges) a részletek minden lehetséges megoldásáról hozhat létre hiba.

Elavult módszerek és a Tulajdonságok kapcsolatban további build figyelmeztetések jelenhetnek meg. A figyelmeztetésekkel kapcsolatos helyett az elavult funkció tartalmazza. Például, ha az alkalmazás használja a `DataSourceType.DocumentDb` tulajdonságot használja, érdemes egy figyelmeztetés fog megjelenni arról, hogy a "elavult ehhez a taghoz. Inkább CosmosDb".

Miután már kijavított bármely fordítási hibákat vagy figyelmeztetéseket, alkalmazását az új funkciók előnyeinek kihasználása, ha módosításokat végezheti el. Az SDK-t az új funkciók részletes leírásuk [9-es verzió újdonságai](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>A 9-es verzió használhatatlanná tévő változásai

9-es verzió, amely lehet szükség a kód módosításait az alkalmazás újraépítését mellett számos kompatibilitástörő változásokat találhatók.

> [!NOTE]
> Azokat a változásokat az alábbi tehát nem tekinthető teljesnek. Néhány módosítást valószínűleg nem eredményez fordítási hibákat, de a rendszer technikailag használhatatlanná tévő, mivel azok felosztása, szerelvényeket, amelyek függnek az Azure Search .NET SDK-szerelvények korábbi verzióiban a bináris kompatibilitás. Az ilyen változások alább nem láthatók. Építse újra az alkalmazás bináris kompatibilitási problémák elkerülése érdekében 9-verzióra történő frissítése során.

### <a name="making-properties-immutable"></a>Így nem módosítható tulajdonságok

Több modell osztályok nyilvános tulajdonságainak immár nem módosítható. Ha ezeket az osztályokat tesztelési egyéni példányainak van szüksége, használhatja az új paraméteres konstruktorok:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>A mező módosításai

A `Field` most, hogy összetett mezők is jelenthet módosították.

A következő `bool` tulajdonságok immár nullázható:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Ennek oka, hogy ezek a Tulajdonságok most kell `null` összetett mezők esetén. Rendelkezik kódot, amely beolvassa ezeket a tulajdonságokat, hogy rendelkezik-e készítenek kezeléséhez `null`. Vegye figyelembe, hogy az összes többi tulajdonság `Field` végigkísérte, és továbbra is nullázható, és olyanokat is lehet `null` összetett mezőjét, konkrétan a következő esetén:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Az a paraméter nélküli konstruktor `Field` lett végrehajtva `internal`. Mostantól minden `Field` igényel explicit nevét és adattípusát konstrukció időpontjában.

### <a name="simplification-of-batch-and-results-types"></a>Batch-és eredmények egyszerűsítése

A 7.0-preview és a korábbi verziókban a különböző osztályok, amely magába foglalja a csoportok a dokumentumok is strukturált történő párhuzamos osztály hierarchiák:

  -  `DocumentSearchResult` és `DocumentSearchResult<T>` öröklődés forrása: `DocumentSearchResultBase`
  -  `DocumentSuggestResult` és `DocumentSuggestResult<T>` öröklődés forrása: `DocumentSuggestResultBase`
  -  `IndexAction` és `IndexAction<T>` öröklődés forrása: `IndexActionBase`
  -  `IndexBatch` és `IndexBatch<T>` öröklődés forrása: `IndexBatchBase`
  -  `SearchResult` és `SearchResult<T>` öröklődés forrása: `SearchResultBase`
  -  `SuggestResult` és `SuggestResult<T>` öröklődés forrása: `SuggestResultBase`

A származtatott típusok általános típusú paraméter nélkül volt "dinamikusan típusmegadású" forgatókönyvekben használni kívánt és használatát feltételezi a `Document` típusa.

8.0. dátumú előzetes sémaverzióra kezdve az alaposztályok és nem általános származtatott osztályainak összes el lettek távolítva. Dinamikusan gépelt forgatókönyvek esetén használható `IndexBatch<Document>`, `DocumentSearchResult<Document>`, és így tovább.
 
### <a name="removal-of-extensibleenum"></a>ExtensibleEnum eltávolítása

A `ExtensibleEnum` alaposztály el lett távolítva. Minden osztály, amely abból származó immár struktúrák, mint például `AnalyzerName`, `DataType`, és `DataSourceType` például. Saját `Create` módszereket is el lett távolítva. Csak távolítsa el a hívások `Create` mivel ezek a típusok konvertálható erre a karakterláncokat. Ha, amely fordítási hibákat eredményez, az átalakítás operátor döntő keresztül könnyen pontosítani lehet típusok explicit módon hívhat meg. Módosíthatja például ehhez hasonló kódok:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

módosítsa a következőre:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Nem kötelező a következő típusú értékek tárolt tulajdonságokat most már explicit módon típusú nullázható, ezek továbbra is választható.

### <a name="removal-of-facetresults-and-hithighlights"></a>FacetResults és HitHighlights eltávolítása

A `FacetResults` és `HitHighlights` osztályok el lett távolítva. Most már típusú értékkorlátozás eredményeket `IDictionary<string, IList<FacetResult>>` , és nyomja le kiemeli `IDictionary<string, IList<string>>`. Ez a módosítás bevezetett fordítási hibákat oldja meg gyorsan az, hogy hozzáadása `using` aliasok felső részén, hogy az eltávolított típusok használja. Példa:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>SynonymMap módosítása 

A `SynonymMap` konstruktor már nem rendelkezik egy `enum` paramétere `SynonymMapFormat`. Ez az enumerálás csak egyetlen értékkel rendelkezik, és ezért redundáns volt. Ha ez miatt kialakult hibákat, a egyszerűen a mutató hivatkozások eltávolítása a `SynonymMapFormat` paraméter.

### <a name="miscellaneous-model-class-changes"></a>Vegyes adatmodell osztály változásainak

A `AutocompleteMode` tulajdonsága `AutocompleteParameters` már nem nullázható. Ha a kódot, amely hozzárendeli ezt a tulajdonságot, hogy rendelkezik `null`, egyszerűen törölje azt, és a tulajdonság automatikusan lehet inicializálni az alapértelmezett értékre.

A paraméterek sorrendje a `IndexAction` konstruktor változott, most, hogy ez a konstruktor automatikusan létrehozott. A konstruktor helyett javasoljuk, hogy a gyári módszerekkel `IndexAction.Upload`, `IndexAction.Merge`, és így tovább.

### <a name="removed-preview-features"></a>Eltávolított előzetes verziójú funkciók

Ha frissít a 8.0. dátumú előzetes sémaverzióra 9-es verzió, vegye figyelembe, hogy a titkosítás, az ügyfél által felügyelt kulcsok el lett távolítva, mivel ez a funkció egyelőre előzetes verzióként érhető. Pontosabban a `EncryptionKey` tulajdonságainak `Index` és `SynonymMap` el lettek távolítva.

Az alkalmazás maga rögzített ezt a szolgáltatást, ha nem tudja frissíteni az Azure Search .NET SDK 9 verziójára. 8.0. dátumú előzetes sémaverzióra használatához továbbra is. Ugyanakkor Kérjük vegye figyelembe, hogy **éles üzemi alkalmazások pedig az SDK-k előzetes verzió használata nem ajánlott**. Előzetes verziójú funkciók csak tesztelési és változhat.

> [!NOTE]
> Ha titkosított létrehozott indexek vagy a térképek 8.0. dátumú előzetes sémaverzióra az SDK használatával, hogy továbbra is képesek lesznek szinonimát használhat, és módosítsa a definíciójukat hátrányosan a titkosítási állapot 9-es verzió az SDK használatával. 9-es verzió az SDK ne küldjön a `encryptionKey` tulajdonság a REST API-t, és eredményként a REST API-t nem módosítja az erőforrás titkosítási állapotát. 

### <a name="behavioral-change-in-data-retrieval"></a>Adatok beolvasása a viselkedéssel összefüggő változás

Ha használja a "dinamikusan gépelt" `Search`, `Suggest`, vagy `Get` típusú példányok visszaadó API-k `Document`, vegye figyelembe, hogy azok most deszerializálni üres JSON-tömbök `object[]` helyett `string[]`.

## <a name="conclusion"></a>Összegzés
Ha részletesebb tájékoztatást az Azure Search .NET SDK van szüksége, tekintse meg a [.NET útmutató](search-howto-dotnet-sdk.md).

Szívesen fogadjuk a visszajelzéseket az SDK-val. Ha problémákat tapasztal, nyugodtan segítségét kell kérnie velünk a kapcsolatot a [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha a hiba, a megkereséseit is fájl a [Azure .NET SDK GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címe és az "[az Azure Search]" előtagot.

Köszönjük, hogy az Azure Search használatával!
