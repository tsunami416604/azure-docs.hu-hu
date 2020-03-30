---
title: Frissítés az Azure Cognitive Search .NET SDK 10-es verziójára
titleSuffix: Azure Cognitive Search
description: Kód áttelepítése az Azure Cognitive Search .NET SDK 10-es verziójú régebbi verziókból. Ismerje meg, hogy mi az új, és milyen kódmódosításokra van szükség.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847526"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Frissítés az Azure Cognitive Search .NET SDK 10-es verziójára

Ha az [Azure Search .NET SDK](https://aka.ms/search-sdk)9.0-s vagy régebbi verzióját használja, ez a cikk segít az alkalmazás frissítésében a 10-es verzió használatára.

Az Azure Search névre átvan nevezve Azure Cognitive Search 10-es verzióban, de a névterek és a csomagnevek változatlanok maradnak. Az SDK korábbi verziói (9.0-s és korábbi verziói) továbbra is a korábbi nevet használják. Az SDK használatáról további információt, példákat is tartalmaz: [Az Azure Cognitive Search használata .NET alkalmazásból.](search-howto-dotnet-sdk.md)

A 10-es verzió számos funkciót és hibajavítást ad hozzá, így ugyanaz `2019-05-06`a funkcionális szintre kerül, mint a REST API verzió legutóbbi kiadása. Abban az esetben, ha a módosítás megszakítja a meglévő kódot, végigvezetjük [a probléma megoldásához szükséges lépéseken.](#UpgradeSteps)

> [!NOTE]
> Ha a 8.0-s vagy újabb verziót használja, először frissítsen a 9-es verzióra, majd frissítsen a 10-es verzióra. További információt [az Azure Search .NET SDK 9-es verziójára való frissítés](search-dotnet-sdk-migration-version-9.md) című témakörben talál.
>
> A keresési szolgáltatáspéldány támogatja a REST API-verziók, köztük a legújabb. Továbbra is használhatja a verziót, ha már nem a legújabb, de azt javasoljuk, hogy telepítse át a kódot a legújabb verzió használatára. A REST API használatakor meg kell adnia az API-verziót minden kérelemben az api-version paraméteren keresztül. A .NET SDK használatakor a használt SDK-verzió határozza meg a REST API megfelelő verzióját. Ha egy régebbi SDK-t használ, továbbra is futtathatja a kódot módosítások nélkül, még akkor is, ha a szolgáltatás egy újabb API-verzió támogatásához lett frissítve.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>A 10-es verzió újdonságai
Az Azure Cognitive Search .NET SDK 10-es verziója a`2019-05-06`REST API ( ) legújabb, általánosan elérhető verzióját célozza meg ezekkel a frissítésekkel:

* Két új készség bevezetése - [Feltételes készség](cognitive-search-skill-conditional.md) és [szövegfordítási készség](cognitive-search-skill-text-translation.md).
* [A formázó idikáta](cognitive-search-skill-shaper.md) szakértelem-bemenetek átlettek alakítva, hogy alkalmazkodjanak a beágyazott környezetekből történő konszolidációhoz. További információ: this [example JSON definition](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Két új [mezőleképezési függvény](search-indexer-field-mappings.md)hozzáadása:
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Bizonyos esetekben az [indexelő-végrehajtási állapotban](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) megjelenő hibák és figyelmeztetések további részleteket tartalmaznak, amelyek segítenek a hibakeresésben. `IndexerExecutionResult`frissítésre került, hogy tükrözze ezt a viselkedést.
* A [skillset-en](cognitive-search-defining-skillset.md) belül meghatározott egyéni készségek opcionálisan `name` azonosíthatók egy tulajdonság megadásával.
* `ServiceLimits`[az összetett típusokra](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) vonatkozó határértékeket, valamint `IndexerExecutionInfo` a vonatkozó indexelő korlátokat/kvótákat jeleníti meg.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései

1. Frissítse NuGet-referenciáját a NuGet csomagkezelő konzol `Microsoft.Azure.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

2. Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. 

3. Ha a build sikertelen, minden egyes buildhibát ki kell javítania. Az egyes lehetséges buildelési hibák megoldásáról a [10-es verzió módosításai nak megírása](#ListOfChanges) című témakörben talál.

4. Miután kijavította a buildelési hibákat vagy figyelmeztetéseket, módosíthatja az alkalmazást, hogy kihasználhassa az új funkciók előnyeit, ha szeretné. Az SDK új funkcióit a [10-es verzió újdonságai](#WhatsNew)részletezik.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>A 10-es verzióban végrehajtott változások megtörése

A 10-es verzióban számos olyan törési változás van, amely az alkalmazás újraépítése mellett kódmódosításokat is igényelhet.

> [!NOTE]
> Az alábbi változások listája nem teljes körű. Egyes módosítások valószínűleg nem eredményeznek buildelési hibákat, de technikailag megszakadnak, mivel megszakítják a bináris kompatibilitást az Azure Cognitive Search .NET SDBs korábbi verzióitól függő szerelvényekkel. Az ebbe a kategóriába tartozó jelentős változások at is felsorolják az ajánlásokkal együtt. A bináris kompatibilitási problémák elkerülése érdekében a 10-es verzióra való frissítéskor építse újra az alkalmazást.

### <a name="custom-web-api-skill-definition"></a>Egyéni webes API-szakértelem-definíció

Az egyéni [webes API-szakértelem](cognitive-search-custom-skill-web-api.md) definíciója helytelenül lett megadva a 9-es és újabb verzióban. 

Szótárt `HttpHeaders` `WebApiSkill` _tartalmazó_ objektumtulajdonság modellje. Egy skillset létrehozása `WebApiSkill` egy ilyen módon létrehozott eredményezne kivételt, mert a REST API-t úgy véli, hogy a kérelem rosszul alakult. Ezt a problémát kijavítottuk `HttpHeaders` azáltal, hogy egy legfelső `WebApiSkill` szintű **szótártulajdonságot** hozott a modellen , amely a REST API-tól érvényes kérésnek minősül.

Ha például korábban az alábbiak szerint `WebApiSkill` próbált meg egy példányot a dinsikálni:

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

módosítsa a következőkre, hogy elkerülje a REST API érvényesítési hibáját:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>A shaper szakértelem lehetővé teszi a beágyazott környezet összevonását

A shaper szakértelem mostantól lehetővé teheti a beágyazott környezetekből származó bemeneti konszolidációt. A módosítás engedélyezéséhez `InputFieldMappingEntry` úgy módosítottuk, hogy csak egy `Source` tulajdonság, vagy a `SourceContext` tulajdonságok `Inputs` és a tulajdonságok megadásával lehessen példányosítani.

Valószínűleg nem kell kódmódosításokat végrehajtania; azonban vegye figyelembe, hogy e két kombináció közül csak az egyik megengedett. Ez a következőket jelenti:

- Ha `InputFieldMappingEntry` olyan `Source` t, ahol csak inicializálva van, az érvényes.
- Ha `InputFieldMappingEntry` csak `SourceContext` ott `Inputs` hoz létre, és inicializálva van, az érvényes.
- A három tulajdonságot érintő összes többi kombináció érvénytelen.

Ha úgy dönt, hogy elkezdi kihasználni ezt az új funkciót, győződjön meg arról, hogy minden ügyfele frissítve van a 10-es verzió használatára, mielőtt a módosítást bevezetné. Ellenkező esetben fennáll annak a lehetősége, hogy egy ügyfél frissítése (az SDK egy régebbi verzióját használva) a Shaper szakértelemhez érvényesítési hibákat eredményezhet.

> [!NOTE]
> Annak ellenére, hogy az alapul szolgáló `InputFieldMappingEntry` modell módosult, hogy lehetővé tegye a beágyazott környezetekből történő konszolidációt, használata csak a Shaper szakértelem definícióján belül érvényes. Ha ezt a képességet más képességekben használja, miközben fordításkor érvényes, futásidőben érvényesítési hibát fog eredményezni.

## <a name="skills-can-be-identified-by-a-name"></a>A készségek et név vel lehet azonosítani

Minden készség egy skillset most `Name`már egy új tulajdonság, amely inicializálható a kódot, hogy segítsen azonosítani a készség. Ez nem kötelező - ha meg határozatlan (ami az alapértelmezett, ha nem történt explicit kódmódosítás), akkor egy alapértelmezett nevet kap a szakértelem 1-alapú indexe használatával a skillset, előtaggal a "#" karakter. Például a következő skillset definícióban (a legtöbb inicializálás rövidségből kimarad):

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

`SentimentSkill`van hozzárendelve `#1` `WebApiSkill` egy névhez `ShaperSkill` , `#3` van hozzárendelve `#2`, hozzá van rendelve, és így tovább.

Ha úgy dönt, hogy egyéni név alapján azonosítja a képességeket, először frissítse az ügyfelek összes példányát az SDK 10-es verziójára. Ellenkező esetben fennáll annak a lehetősége, hogy az SDK egy régebbi verzióját használó ügyfél kitudja `null` lépni egy szakértelem `Name` tulajdonságából, ami miatt az ügyfél visszaesik az alapértelmezett elnevezési sémára.

## <a name="details-about-errors-and-warnings"></a>Részletek a hibákról és figyelmeztetésekről

`ItemError`és `ItemWarning` a modellek, amelyek magukban foglalják a hibák és figyelmeztetések részleteit (illetve) során előforduló indexelő végrehajtása módosult, hogy tartalmazza a három új tulajdonságok azzal a céllal, hogy segítse a hibakeresés az indexelő. Ezek a tulajdonságok a következők:

- `Name`: Annak a forrásnak a neve, amelyből a hiba származik. Például utalhat egy adott szakértelemre a csatolt skillsetben.
- `Details`: További részletes részletek a hibáról vagy figyelmeztetésről.
- `DocumentationLink`: Hivatkozás az adott hibával vagy figyelmeztetéssel kapcsolatos hibaelhárítási útmutatóra.

> [!NOTE]
> Elkezdtük felépíteni a hibákat és figyelmeztetéseket, hogy ezeket a hasznos részleteket, amikor csak lehetséges. Dolgozunk annak biztosításán, hogy minden hiba és figyelmeztetés esetében ezek az adatok jelen legyenek, de ez egy folyamatban lévő munka, és ezek a további részletek nem mindig kerülnek kitöltésre.

## <a name="next-steps"></a>További lépések

- A Shaper szakértelem módosításai a legnagyobb hatással vannak az új vagy meglévő kódra. Következő lépésként mindenképpen vizsgálja meg újra ezt a példát, amely a bemeneti struktúrát szemlélteti: [Shaper skill JSON definition példa](cognitive-search-skill-shaper.md)
- Az [AI-dúsítás áttekintése.](cognitive-search-concept-intro.md)
- Örömmel fogadjuk az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, nyugodtan kérjen tőlünk segítséget [stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, az [Azure .NET SDK GitHub tárházban](https://github.com/Azure/azure-sdk-for-net/issues)nyújthat be problémát. Győződjön meg arról, hogy előtag a probléma címe a "[Azure Cognitive Search]".

