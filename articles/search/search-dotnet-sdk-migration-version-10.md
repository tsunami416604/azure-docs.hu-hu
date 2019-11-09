---
title: Frissítés az Azure Cognitive Search .NET SDK 10-es verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot az Azure Cognitive Search .NET SDK 10-es verziójára a régebbi verzióról. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847526"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Frissítés az Azure Cognitive Search .NET SDK 10-es verziójára

Ha a [Azure Search .net SDK](https://aka.ms/search-sdk)9,0-es vagy régebbi verzióját használja, ez a cikk segítséget nyújt az alkalmazás a 10-es verzió használatára való frissítéséhez.

Azure Search a 10-es verzióban az Azure Cognitive Search lett átnevezve, de a névterek és a csomagok nevei változatlanok. Az SDK korábbi verziói (9,0-es és korábbi verziók) továbbra is a korábbi nevet használják. További információ az SDK használatáról, beleértve a példákat: az [Azure Cognitive Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A 10-es verzió számos funkciót és hibajavítást tartalmaz, és a REST API `2019-05-06`verziójának legújabb kiadásával azonos működési szintre helyezi. Olyan esetekben, amikor a változás megszakítja a meglévő kódot, végigvezeti a [probléma megoldásához szükséges lépéseken](#UpgradeSteps).

> [!NOTE]
> Ha a 8,0-es vagy régebbi verziót használja, először frissítsen a 9-es verzióra, majd frissítsen a 10-es verzióra. Útmutatásért lásd: [a Azure Search .net SDK 9-es verziójára való frissítés](search-dotnet-sdk-migration-version-9.md) .
>
> A keresési szolgáltatás példánya számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>A 10-es verzió újdonságai
Az Azure Cognitive Search .NET SDK 10-es verziója a REST API (`2019-05-06`) legújabb általánosan elérhető verzióját célozza meg a következő frissítésekkel:

* Két új szaktudás bevezetése – a [feltételes szaktudás](cognitive-search-skill-conditional.md) és a [szöveg fordítási](cognitive-search-skill-text-translation.md)képességei.
* A rendszer átalakította a [formálói képességek](cognitive-search-skill-shaper.md) bemeneteit, hogy az összevont környezetek összevonását is elférjen. További információt ebben a példában a [JSON-definícióban](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)talál.
* Két új mező- [hozzárendelési függvény](search-indexer-field-mappings.md)hozzáadása:
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Az [Indexelő végrehajtási állapotában](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) megjelenő hibák és figyelmeztetések bizonyos esetekben további részleteket is tartalmazhatnak, amelyek segítenek a hibakeresésben. a `IndexerExecutionResult` frissült, hogy tükrözze ezt a viselkedést.
* A [készségkészlet](cognitive-search-defining-skillset.md) belül definiált egyéni képességek egy `name` tulajdonság megadásával is azonosíthatók.
* `ServiceLimits` az [összetett típusok](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) és a `IndexerExecutionInfo` korlátait jeleníti meg, amelyek a kapcsolódó indexelő korlátokat/kvótákat mutatják be.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései

1. Frissítse a `Microsoft.Azure.Search` NuGet-hivatkozását a NuGet csomagkezelő konzol használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

2. Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. 

3. Ha a Build sikertelen, ki kell javítania az egyes Build-hibákat. A lehetséges Build-hibák megoldásával kapcsolatos részletekért tekintse meg a 10. verzióban megjelenő [változások feltörését ismertető részt](#ListOfChanges) .

4. A felépítési hibák vagy figyelmeztetések kijavítása után módosíthatja az alkalmazást, hogy igénybe vehesse az új funkciókat. Az SDK új funkciói részletesen ismertetik a [10-es verzió újdonságait](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>A 10. verzióban feltört változások

Több, a 10-es verzióban felmerülő változás miatt a kód módosítására is szükség lehet az alkalmazás újraépítése mellett.

> [!NOTE]
> Az alábbi módosítások listája nem teljes. Bizonyos változások valószínűleg nem eredményeznek fordítási hibákat, de technikailag megszakadnak, mivel a bináris kompatibilitást az Azure Cognitive Search .NET SDK-szerelvények korábbi verzióitól függő szerelvényekkel bontják le. Az ebben a kategóriában található jelentős változások a javaslatok mellett is szerepelnek. A bináris kompatibilitási problémák elkerülése érdekében hozza létre újra az alkalmazást a 10-es verzióra való frissítéskor.

### <a name="custom-web-api-skill-definition"></a>Egyéni webes API-szaktudás definíciója

Az [egyéni webes API-képesség](cognitive-search-custom-skill-web-api.md) definíciója helytelenül lett megadva a 9. és a régebbi verzióban. 

A `WebApiSkill` megadott modell olyan objektum-tulajdonságként `HttpHeaders`, amely szótárt _tartalmaz_ . Ha olyan készségkészlet hoz létre `WebApiSkill`, amely ilyen módon lett létrehozva, akkor kivételt eredményezne, mert a REST API a kérést helytelenül alakította ki. Ezt a problémát kijavítottuk, ha `HttpHeaders` **a `WebApiSkill` modell legfelső szintű szótár tulajdonságát** , amely a REST API érvényes kérelmének minősül.

Ha például korábban megkísérelt létrehozni egy `WebApiSkill` a következőképpen:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

módosítsa a következőre, hogy elkerülje az érvényesítési hibát a REST API:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>A formáló képesség lehetővé teszi a beágyazott környezetek összevonását

A formáló képesség mostantól lehetővé teszi a bemeneti konszolidációt a beágyazott környezetekben. Ennek a módosításnak az engedélyezéséhez módosítottunk `InputFieldMappingEntry`, hogy csak egy `Source` tulajdonság megadásával, vagy a `SourceContext` és `Inputs` tulajdonságok megadásával legyen létrehozva.

Valószínűleg nem lesz szükség a kód módosítására; vegye figyelembe azonban, hogy a két kombinációnak csak egy része engedélyezett. Ez a következőket jelenti:

- Olyan `InputFieldMappingEntry` létrehozása, amelyben csak `Source` inicializálása érvényes.
- Olyan `InputFieldMappingEntry` létrehozása, amelyben csak a `SourceContext` és a `Inputs` inicializálása érvényes.
- A három tulajdonságot tartalmazó összes többi kombináció érvénytelen.

Ha úgy dönt, hogy elkezdi használni ezt az új képességet, győződjön meg arról, hogy az összes ügyfelet először a 10-es verzióra frissíti, mielőtt a módosítást elvégzi. Ellenkező esetben előfordulhat, hogy az ügyfél (az SDK egy régebbi verziójával) általi frissítése érvényesítési hibát eredményezhet.

> [!NOTE]
> Annak ellenére, hogy az alapul szolgáló `InputFieldMappingEntry` modell úgy lett módosítva, hogy lehetővé tegye az összesítést a beágyazott környezetekben, a használata csak az alakzatra vonatkozó szaktudás definíciójában érvényes. Ha ezt a képességet más szakismeretekben használja, míg a fordítási időpontban érvényes, az érvényesítési hibát eredményez futásidőben.

## <a name="skills-can-be-identified-by-a-name"></a>A készségek azonosíthatók névvel

A készségkészlet belüli minden egyes képesség mostantól egy új `Name`tulajdonsággal rendelkezik, amely inicializálható a kódban, így könnyebben azonosítható a szaktudás. Ez nem kötelező – ha meg van adva (ez az alapértelmezett beállítás, ha nem történt meg kifejezett kód módosítása), a rendszer az alapértelmezett nevet rendeli hozzá a készségkészlet, a "#" karakterrel ellátott 1-alapú index használatával. Például a következő készségkészlet-definícióban (a legtöbb inicializálás a rövidség kedvéért kimarad):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

a `SentimentSkill` neve `#1`, `WebApiSkill` van hozzárendelve `#2`, `ShaperSkill` hozzá van rendelve `#3` és így tovább.

Ha úgy dönt, hogy egyéni névvel azonosítja a szaktudást, akkor először frissítse az ügyfelek összes példányát az SDK 10-es verziójára. Ellenkező esetben lehetősége van arra, hogy az SDK egy régebbi verzióját használó ügyfél `null` egy képesség `Name` tulajdonságát, ami miatt az ügyfél visszatérhet az alapértelmezett elnevezési sémához.

## <a name="details-about-errors-and-warnings"></a>Hibákkal és figyelmeztetésekkel kapcsolatos részletek

`ItemError` és `ItemWarning` olyan modelleket, amelyek az indexelő végrehajtása során felmerülő hibák és figyelmeztetések (vagy) részletes adatait tartalmazzák, hogy három új tulajdonságot foglaljanak bele az indexelő hibakeresési célú támogatásának céljával. Ezek a tulajdonságok a következők:

- `Name`: annak a forrásnak a neve, amelyen a hiba származik. Előfordulhat például, hogy egy adott képességre hivatkozik a csatolt készségkészlet.
- `Details`: a hibával vagy figyelmeztetéssel kapcsolatos további részletes információk.
- `DocumentationLink`: az adott hibára vagy figyelmeztetésre vonatkozó hibaelhárítási útmutatóra mutató hivatkozás.

> [!NOTE]
> Elkezdjük felstrukturálni a hibákat és a figyelmeztetéseket, hogy a lehető legpontosabban szerepeljenek ezek a hasznos információk. Dolgozunk annak biztosításán, hogy az összes hiba és figyelmeztetés ezen adatok jelennek meg, de a folyamat folyamatban van, és ezek a további részletek nem mindig lesznek feltöltve.

## <a name="next-steps"></a>További lépések

- A Shapeer-képesség változásai a legnagyobb hatással lehetnek az új vagy meglévő kódokra. A következő lépésként mindenképpen tekintse át ezt a példát a bemeneti struktúra: [formáló ÜGYESSÉGI JSON-definíció példájának](cognitive-search-skill-shaper.md) szemléltetése.
- Ugorjon végig a [mesterséges intelligenciával foglalkozó áttekintésben](cognitive-search-concept-intro.md).
- Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a probléma címét az "[Azure Cognitive Search]" előtaggal adja meg.

