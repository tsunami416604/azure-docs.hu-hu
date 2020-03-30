---
title: Frissítés az Azure Search .NET SDK 9-es verziójára
titleSuffix: Azure Cognitive Search
description: A kódot az Azure Search .NET SDK 9-es verziójába telepítheti át a régebbi verziókról. Ismerje meg, hogy mi az új, és milyen kódmódosításokra van szükség.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793018"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Frissítés az Azure Search .NET SDK 9-es verziójára

Ha az [Azure Search .NET SDK](https://aka.ms/search-sdk)7.0-s vagy régebbi verzióját használja, ez a cikk segít az alkalmazás frissítésében a 9-es verzió használatára.

> [!NOTE]
> Ha a 8.0-s verziójú előzetes verziót szeretné használni az általánosan még nem elérhető funkciók kiértékeléséhez, a cikkben található utasításokat követve frissíthet a 8.0-előzetes verzióra a korábbi verziókról.

Az SDK általánosabb forgatókönyvét példákkal együtt az [Azure Search használata .NET alkalmazásból](search-howto-dotnet-sdk.md)című témakörben talál.

Az Azure Search .NET SDK 9-es verziója számos korábbi verzióhoz tartozó módosítást tartalmaz. Ezek közül néhány a változások at szakító, de csak viszonylag kisebb módosításokat igényel a kódot. Az új SDK-verzió használatához a [frissítés lépései](#UpgradeSteps) című témakörben talál útmutatást a kód módosításához.

> [!NOTE]
> Ha a 4.0-s vagy újabb verziót használja, először frissítsen az 5-ös verzióra, majd frissítsen a 9-es verzióra. További információt [az Azure Search .NET SDK 5-ös verziójára való frissítés](search-dotnet-sdk-migration-version-5.md) című témakörben talál.
>
> Az Azure Search szolgáltatáspéldány a REST API számos verzióját támogatja, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de azt javasoljuk, hogy telepítse át a kódot a legújabb verzió használatára. A REST API használatakor meg kell adnia az API-verziót minden kérelemben az api-version paraméteren keresztül. A .NET SDK használatakor a használt SDK-verzió határozza meg a REST API megfelelő verzióját. Ha egy régebbi SDK-t használ, továbbra is futtathatja a kódot módosítások nélkül, még akkor is, ha a szolgáltatás egy újabb API-verzió támogatásához lett frissítve.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>A 9-es verzió újdonságai
Az Azure Search .NET SDK 9-es verziója az Azure Search REST API legújabb, általánosan elérhető verzióját célozza meg, különösen a 2019-05-06-os verziót. Ez lehetővé teszi az Azure Search új funkcióinak használatát egy .NET alkalmazásból, beleértve a következőket:

* [AI-bővítés](cognitive-search-concept-intro.md) az a képesség, hogy kinyerje a szöveget a képek, blobok és más strukturálatlan adatforrások – gazdagítja a tartalmat, hogy az Azure Search indexben kereshetőbb.
* Az [összetett típusok](search-howto-complex-data-types.md) támogatása lehetővé teszi, hogy az Azure Search-indexszinte en-alapú JSON-struktúráját modellezze.
* [Az automatikus kiegészítés](search-autocomplete-tutorial.md) a **Javaslat** API alternatívája a beírási keresés viselkedésének megvalósításához. Az automatikus kiegészítés "befejezi" azt a szót vagy kifejezést, amelyet a felhasználó éppen beír.
* [JsonLines elemzési mód](search-howto-index-json-blobs.md), része az Azure Blob indexelés, létrehoz egy keresési dokumentumot egy JSON-entitás, amely egy újvonal választja el.

### <a name="new-preview-features-in-version-80-preview"></a>Új előzetes verziójú funkciók a 8.0-as verzióban
Az Azure Search .NET SDK 8.0-s verziójú verziója a 2017-11-11-preview API-verziót célozza meg. Ez a verzió tartalmazza az összes ugyanazokat a funkciókat a 9-es verzió, plusz:

* [Az ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) a szolgáltatásoldali inaktív titkosításhoz egy új előzetes funkció. A Microsoft által felügyelt beépített inaktív titkosításon kívül egy további titkosítási réteget is alkalmazhat, ahol ön a kulcsok egyedüli tulajdonosa.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse NuGet-referenciáját a NuGet Csomagkezelő konzol `Microsoft.Azure.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. Attól függően, hogy a kód épül, előfordulhat, hogy sikeresen újraépül. Ha igen, akkor készen áll az indulásra!

Ha a build sikertelen, minden egyes buildhibát ki kell javítania. Az egyes lehetséges buildelési hibák megoldásáról a [9-es verzió módosításai](#ListOfChanges) nak elhárítása című témakörben talál.

Az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további buildfigyelmeztetések jelenhetnek meg. A figyelmeztetések utasításokat tartalmaznak arra vonatkozóan, hogy mit használjanak az elavult szolgáltatás helyett. Ha például az alkalmazás `DataSourceType.DocumentDb` használja a tulajdonságot, akkor egy figyelmeztetést kell kapnia, amely azt mondja: "Ez a tag elavult. Használja inkább a CosmosDb-t".

Miután kijavította a buildelési hibákat vagy figyelmeztetéseket, módosíthatja az alkalmazást, hogy kihasználhassa az új funkciók előnyeit, ha szeretné. Az SDK új funkcióit a [9-es verzió újdonságai](#WhatsNew)részletezik.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>A változások megtörése a 9-es verzióban

A 9-es verzióban számos olyan törési módosítás van, amely az alkalmazás újraépítése mellett kódmódosításokat is igényelhet.

> [!NOTE]
> Az alábbi változások listája nem teljes körű. Egyes módosítások valószínűleg nem eredményeznek buildelési hibákat, de technikailag megszakadnak, mivel megszakítják a bináris kompatibilitást az Azure Search .NET SDK-szerelvények korábbi verzióitól függő szerelvényekkel. Az ilyen változások az alábbiakban nem szerepelnek. A bináris kompatibilitási problémák elkerülése érdekében a 9-es verzióra való frissítéskor építse újra az alkalmazást.

### <a name="immutable-properties"></a>Nem módosítható tulajdonságok

Több modellosztály nyilvános tulajdonságai mostantól nem módosíthatók. Ha ezekből az osztályokból egyéni példányokat kell létrehoznia tesztelésre, használhatja az új paraméterezett konstruktorokat:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Mező módosításai

Az `Field` osztály megváltozott most, hogy összetett mezőket is képviselhet.

A `bool` következő tulajdonságok mostantól semlegesíthetők:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Ennek az az oka, hogy ezeknek a tulajdonságoknak összetett mezők esetén kell lenniük. `null` Ha olyan kóddal rendelkezik, amely beolvassa `null`ezeket a tulajdonságokat, fel kell készülnie a kezelésére. Ne feledje, `Field` hogy az összes többi tulajdonsága mindig is volt `null` és a jövőben is semlegesíthető, és ezek közül néhány összetett mezők esetében is így lesz - különösen a következők:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

A paraméter nélküli `Field` konstruktor készült. `internal` Mostantól minden `Field` szükséges explicit név és adattípus idején az építés.

### <a name="simplified-batch-and-results-types"></a>Egyszerűsített köteg- és eredménytípusok

A 7.0-s és korábbi verzióban a dokumentumcsoportokat beágyazó különböző osztályok párhuzamos osztályhierarchiákba szerveződtek:

  -  `DocumentSearchResult`és `DocumentSearchResult<T>` örökölt`DocumentSearchResultBase`
  -  `DocumentSuggestResult`és `DocumentSuggestResult<T>` örökölt`DocumentSuggestResultBase`
  -  `IndexAction`és `IndexAction<T>` örökölt`IndexActionBase`
  -  `IndexBatch`és `IndexBatch<T>` örökölt`IndexBatchBase`
  -  `SearchResult`és `SearchResult<T>` örökölt`SearchResultBase`
  -  `SuggestResult`és `SuggestResult<T>` örökölt`SuggestResultBase`

Az általános típusparaméter nélküli származtatott típusokat "dinamikusan beírt" forgatókönyvekben `Document` és a típus feltételezett használatában kellett volna használni.

A 8.0-s verziójú előzetes verziótól kezdve az alaposztályok és a nem általános eredetű származtatott osztályok mind eltávolításra kerültek. Dinamikusan beírt esetekben használhatja `IndexBatch<Document>`a `DocumentSearchResult<Document>`, és így tovább.
 
### <a name="removed-extensibleenum"></a>Eltávolítva ExtensibleEnum

Az `ExtensibleEnum` alaposztály t lett eltávolítva. Az abból származó összes osztály most antól `AnalyzerName`structs, például , `DataType`, és `DataSourceType` például. Módszereiket `Create` is eltávolították. Egyszerűen eltávolíthatja a `Create` hívásokat, mivel ezek a típusok implicit módon átalakíthatók a karakterláncokból. Ha ez fordítási hibákat eredményez, explicit módon meghívhatja a konverziós operátort az öntésen keresztül a félreérthető típusokra. A kódot például a következőkhez hasonlóan módosíthatja:

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

Az ilyen típusok nem kötelező értékeit tartalmazó tulajdonságok mostantól kifejezetten nullázhatóként vannak beírva, így továbbra is választhatóak.

### <a name="removed-facetresults-and-hithighlights"></a>Eltávolított FacetResults és HitHighlights

A `FacetResults` `HitHighlights` és az osztályok el lettek távolítva. Facet eredmények most gépelt, `IDictionary<string, IList<FacetResult>>` `IDictionary<string, IList<string>>`és nyomja meg kiemeli a . A módosítás által bevezetett buildhibák megoldásának gyors `using` módja, ha az eltávolított típusokat használó fájlok tetejére aliasokat ad hozzá. Példa:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Váltás a SynonymMap-re 

A `SynonymMap` konstruktor már nem rendelkezik paraméterrel `enum` a hoz. `SynonymMapFormat` Ennek a felsorításnak csak egy értéke volt, ezért felesleges volt. Ha ennek eredményeként buildhibákat lát, egyszerűen távolítsa `SynonymMapFormat` el a paraméterre mutató hivatkozásokat.

### <a name="miscellaneous-model-class-changes"></a>Egyéb modellosztály-változások

A `AutocompleteMode` tulajdonság `AutocompleteParameters` már nem nullázható. Ha olyan kóddal rendelkezik, `null`amely hozzárendeli ezt a tulajdonságot a hoz, egyszerűen eltávolíthatja azt, és a tulajdonság automatikusan az alapértelmezett értékre lesz inicializálva.

A paraméterek sorrendje a `IndexAction` konstruktorhoz megváltozott, most, hogy ez a konstruktor automatikusan generálódik. A konstruktor használata helyett `IndexAction.Upload`a `IndexAction.Merge`gyári módszerek használatát javasoljuk, és így tovább.

### <a name="removed-preview-features"></a>Az előzetes verzió funkcióinak eltávolítása

Ha a 8.0-s verziójú előzetes verzióról a 9-es verzióra frissít, vegye figyelembe, hogy az ügyfél által felügyelt kulcsokkal történő titkosítás t, mivel ez a szolgáltatás még előzetes verzióban van. Pontosabban, `EncryptionKey` a `Index` tulajdonságait, és `SynonymMap` eltávolították.

Ha az alkalmazás erősen függ ettől a funkciótól, nem fog tudni frissíteni az Azure Search .NET SDK 9-es verziójára. Továbbra is használhatja a 8.0-s verziójú előzetes verziót. Kérjük azonban, vegye figyelembe, hogy **nem javasoljuk az előzetes SDK-k használatát az éles alkalmazásokban.** Az előnézeti funkciók csak kiértékelésre szolgálnak, és változhatnak.

> [!NOTE]
> Ha titkosított indexeket vagy szinonimát hozott létre az SDK 8.0-s verziójával, továbbra is használhatja őket, és módosíthatja a definícióikat az SDK 9-es verziójával anélkül, hogy hátrányosan befolyásolná a titkosítási állapotukat. Az SDK 9-es verziója `encryptionKey` nem küldi el a tulajdonságot a REST API-nak, és ennek következtében a REST API nem módosítja az erőforrás titkosítási állapotát. 

### <a name="behavioral-change-in-data-retrieval"></a>Az adatok visszakeresésének viselkedésbeli változása

`Search`Ha a "dinamikusan beírt" vagy `Suggest` `Get` API-kat használja, amelyek `Document`típuspéldányokat adnak vissza, vegye figyelembe, `object[]` hogy `string[]`azok most antól deszerializálják az üres JSON-tömböket a helyett.

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége az Azure Search .NET SDK használatával kapcsolatos részletekről, olvassa el a [.NET útmutatóját.](search-howto-dotnet-sdk.md)

Örömmel fogadjuk az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, nyugodtan kérjen tőlünk segítséget [stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, az [Azure .NET SDK GitHub tárházban](https://github.com/Azure/azure-sdk-for-net/issues)nyújthat be problémát. Győződjön meg arról, hogy előtag a probléma címe a "[Azure Search]".

Köszönjük, hogy az Azure Search-et használja!
