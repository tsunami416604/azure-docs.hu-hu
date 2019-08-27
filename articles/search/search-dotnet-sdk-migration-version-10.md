---
title: Frissítés a Azure Search .NET SDK 10-es verziójára – Azure Search
description: Telepítse át a kódot a Azure Search .NET SDK 10-es verziójára a régebbi verzióról. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
author: arv100kri
manager: briansmi
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: arjagann
ms.custom: seodec2018
ms.openlocfilehash: 193f886fe92c41fa51dbc5acbca89f0a5aa6043e
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020263"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-10"></a>Frissítés a Azure Search .NET SDK 10-es verziójára

Ha a [Azure Search .net SDK](https://aka.ms/search-sdk)9,0-es vagy régebbi verzióját használja, ez a cikk segítséget nyújt az alkalmazás a 10-es verzió használatára való frissítéséhez.

Az SDK-val kapcsolatos általános áttekintést a példákat lásd: [Azure Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A 10-es verzió számos funkciót és hibajavítást tartalmaz, így a REST API verziójának `2019-05-06`legújabb kiadásával azonos működési szintre helyezheti. Olyan esetekben, amikor a változás megszakítja a meglévő kódot, végigvezeti a [probléma megoldásához szükséges lépéseken](#UpgradeSteps).

> [!NOTE]
> Ha a 8,0-es vagy régebbi verziót használja, először frissítsen a 9-es verzióra, majd frissítsen a 10-es verzióra. Útmutatásért lásd: [a Azure Search .net SDK 9-es verziójára való frissítés](search-dotnet-sdk-migration-version-9.md) .
>
> Az Azure Search Service-példány számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>A 10-es verzió újdonságai
A Azure Search .net SDK 10-es verziója a Azure Search REST API (`2019-05-06`) legújabb általánosan elérhető verzióját célozza meg a következő frissítésekkel:

* Két új szaktudás bevezetése – a [feltételes szaktudás](cognitive-search-skill-conditional.md) és a [szöveg fordítási](cognitive-search-skill-text-translation.md)képességei.
* A rendszer átalakította a [formálói képességek](cognitive-search-skill-shaper.md) bemeneteit, hogy az összevont környezetek összevonását is elférjen. További információt ebben a példában a [JSON](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)-definícióban talál.
* Két új mező- [hozzárendelési függvény](search-indexer-field-mappings.md)hozzáadása:
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Az [Indexelő végrehajtási állapotában](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) megjelenő hibák és figyelmeztetések bizonyos esetekben további részleteket is tartalmazhatnak, amelyek segítenek a hibakeresésben. `IndexerExecutionResult`frissítve lett, hogy tükrözze ezt a viselkedést.
* A [készségkészlet](cognitive-search-defining-skillset.md) belül meghatározott egyéni készségeket egy `name` tulajdonság megadásával lehet azonosítani.
* `ServiceLimits`az [összetett típusok](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) korlátait jeleníti meg, és `IndexerExecutionInfo` megjeleníti a kapcsolódó indexelő korlátokat/kvótákat.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései

1. Frissítse a NuGet-referenciát `Microsoft.Azure.Search` a NuGet Package Manager konzoljának használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

2. Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. 

3. Ha a Build sikertelen, ki kell javítania az egyes Build-hibákat. A lehetséges Build-hibák megoldásával kapcsolatos részletekért tekintse meg a 10. verzióban megjelenő változások feltörését ismertető [részt](#ListOfChanges) .

4. A felépítési hibák vagy figyelmeztetések kijavítása után módosíthatja az alkalmazást, hogy igénybe vehesse az új funkciókat. Az SDK új funkciói részletesen ismertetik a [10-es verzió újdonságait](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>A 10. verzióban feltört változások

Több, a 10-es verzióban felmerülő változás miatt a kód módosítására is szükség lehet az alkalmazás újraépítése mellett.

> [!NOTE]
> Az alábbi módosítások listája nem teljes. Bizonyos változások valószínűleg nem eredményeznek fordítási hibákat, de technikailag megszakadnak, mivel a bináris kompatibilitást a Azure Search .NET SDK-szerelvények korábbi verzióitól függő szerelvényekkel bontják le. Az ebben a kategóriában található jelentős változások a javaslatok mellett is szerepelnek. A bináris kompatibilitási problémák elkerülése érdekében hozza létre újra az alkalmazást a 10-es verzióra való frissítéskor.

### <a name="custom-web-api-skill-definition"></a>Egyéni webes API-szaktudás definíciója

Az [egyéni webes API-képesség](cognitive-search-custom-skill-web-api.md) definíciója helytelenül lett megadva a 9. és a régebbi verzióban. 

Egy szótárt `WebApiSkill` tartalmazó `HttpHeaders` Object tulajdonságként megadott modell . Ha olyan készségkészlet `WebApiSkill` hoz létre, amely ilyen módon lett létrehozva, kivételt eredményezne, mert a REST API a kérést helytelenül alakította ki. Ezt a problémát kijavítottuk a `HttpHeaders` `WebApiSkill` modell legfelső **szintű szótári tulajdonságának** használatával, amely a REST API érvényes kérelmének minősül.

Ha például korábban a `WebApiSkill` következőt próbálta létrehozni:

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

A formáló képesség mostantól lehetővé teszi a bemeneti konszolidációt a beágyazott környezetekben. Ennek a módosításnak az engedélyezéséhez `InputFieldMappingEntry` módosítva lett, hogy csak egy `Source` tulajdonság vagy a és `Inputs` a `SourceContext` tulajdonságok megadásával legyen létrehozva.

Valószínűleg nem lesz szükség a kód módosítására; vegye figyelembe azonban, hogy a két kombinációnak csak egy része engedélyezett. Ez a következőket jelenti:

- `InputFieldMappingEntry` A csak`Source` az inicializálási lehetőség létrehozása érvényes.
- `InputFieldMappingEntry` Csak a`SourceContext` és`Inputs` a inicializálása érvényes.
- A három tulajdonságot tartalmazó összes többi kombináció érvénytelen.

Ha úgy dönt, hogy elkezdi használni ezt az új képességet, győződjön meg arról, hogy az összes ügyfelet először a 10-es verzióra frissíti, mielőtt a módosítást elvégzi. Ellenkező esetben előfordulhat, hogy az ügyfél (az SDK egy régebbi verziójával) általi frissítése érvényesítési hibát eredményezhet.

> [!NOTE]
> Annak ellenére, hogy `InputFieldMappingEntry` az alapul szolgáló modell úgy lett módosítva, hogy lehetővé tegye az összevonás beágyazott környezetekben való konszolidálását, a használata csak az alakzatra vonatkozó szaktudás definíciójában érvényes. Ha ezt a képességet más szakismeretekben használja, míg a fordítási időpontban érvényes, az érvényesítési hibát eredményez futásidőben.

## <a name="skills-can-be-identified-by-a-name"></a>A készségek azonosíthatók névvel

A készségkészlet belüli minden egyes képesség mostantól egy új `Name`tulajdonsággal rendelkezik, amely a kódban is inicializálható, így könnyebben azonosítható a szaktudás. Ez nem kötelező – ha meg van adva (ez az alapértelmezett beállítás, ha nem történt meg kifejezett kód módosítása), a rendszer az alapértelmezett nevet rendeli hozzá a készségkészlet, a "#" karakterrel ellátott 1-alapú index használatával. Például a következő készségkészlet-definícióban (a legtöbb inicializálás a rövidség kedvéért kimarad):

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

`SentimentSkill`hozzá van rendelve egy `#1`név `WebApiSkill` , hozzá `#2` vanrendelve`ShaperSkill` , ésígytovább.`#3`

Ha úgy dönt, hogy egyéni névvel azonosítja a szaktudást, akkor először frissítse az ügyfelek összes példányát az SDK 10-es verziójára. Ellenkező esetben előfordulhat, hogy az SDK régebbi verzióját használó ügyfelek kihasználhatják `null` `Name` a képességek tulajdonságát, így az ügyfél visszatérhet az alapértelmezett elnevezési sémához.

## <a name="details-about-errors-and-warnings"></a>Hibákkal és figyelmeztetésekkel kapcsolatos részletek

`ItemError`az `ItemWarning` indexelő végrehajtása során felmerülő hibák és figyelmeztetések (vagy) részletes adatait tartalmazó modellek módosítva lettek, hogy három új tulajdonságot tartalmazzanak, amelyek célja az indexelő hibakeresése. Ezek a tulajdonságok a következők:

- `Name`: Annak a forrásnak a neve, amelyen a hiba származik. Előfordulhat például, hogy egy adott képességre hivatkozik a csatolt készségkészlet.
- `Details`: További részletes információk a hibáról vagy figyelmeztetésről.
- `DocumentationLink`: Az adott hibára vagy figyelmeztetésre vonatkozó hibaelhárítási útmutatóra mutató hivatkozás.

> [!NOTE]
> Elkezdjük felstrukturálni a hibákat és a figyelmeztetéseket, hogy a lehető legpontosabban szerepeljenek ezek a hasznos információk. Dolgozunk annak biztosításán, hogy az összes hiba és figyelmeztetés ezen adatok jelennek meg, de a folyamat folyamatban van, és ezek a további részletek nem mindig lesznek feltöltve.

## <a name="next-steps"></a>További lépések

- A Shapeer-képesség változásai a legnagyobb hatással lehetnek az új vagy meglévő kódokra. A következő lépésként mindenképpen tekintse át ezt a példát a bemeneti struktúrát bemutató példaként: [Példa a shapeer skill JSON-definícióra](cognitive-search-skill-shaper.md)
- Folytassa a [kognitív keresési útmutató](cognitive-search-concept-intro.md)bevezetésével.
- Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, a probléma az [Azure .net SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues)-tárházában is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

