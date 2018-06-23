---
title: Gépi tanulás - Azure kognitív szolgáltatások |} Microsoft Docs
description: Egy Azure egyéni döntési szolgáltatásban, a felhőalapú API környezetfüggő döntéshozatali machine Learning oktatóanyag.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh
ms.openlocfilehash: 50814d67ee39c6657954610358462d877843416e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349015"
---
# <a name="machine-learning"></a>Gépi tanulás

Ez az oktatóanyag a fejlett gépi tanulási funkció egyéni döntési szolgáltatás kezeli. Az oktatóanyag két részből áll: [featurization](#featurization-concepts-and-implementation) és [specification funkció](#feature-specification-format-and-apis). Featurization hivatkozik az adatok, mint a "szolgáltatások" jelző a machine Learning szolgáltatáshoz. Szolgáltatás megadását a JSON formátum és a kiegészítő API-k meghatározásához funkciókat ismerteti.

Alapértelmezés szerint a gépi tanulási egyéni döntési szolgáltatásban az transzparens az ügyfélnek. Szolgáltatások automatikusan ki kell olvasni a tartalomról, és egy szabványos megerősítése tanulási algoritmust használnak. Szolgáltatás kivonása használja. Ez több más Azure kognitív szolgáltatások: [entitás Linking](../entitylinking/home.md), [Szövegelemzések](../text-analytics/overview.md), [Érzelemfelismerési](../emotion/home.md), és [számítógép stratégiai](../computer-vision/home.md). Ez az oktatóanyag is kimarad, ha csak az alapértelmezett funkció használható.

## <a name="featurization-concepts-and-implementation"></a>Featurization: fogalmakat és megvalósítása

Egyéni döntési szolgáltatás döntéseket egyenként. Minden döntést foglal magában, több alternatívák, más néven, a műveletek között. Az alkalmazástól függően a döntést is dönthet, egyetlen művelettel, vagy a műveletek (rövid) rangsorolt listáját.

Például személyre szabása cikkek az első oldalon egy webhely kiválasztása. Itt cikkek felelnek meg, és minden döntés egy adott felhasználónak megjelenítendő mely cikkeket.

Minden egyes művelethez tulajdonságai, a továbbiakban vektor által képviselt *szolgáltatások*. Új funkciók mellett a kibontott automatikusan funkciókat is megadhat. Azt is beállíthatja, hogy egyéni döntési szolgáltatás jelentkezzen néhány funkció, de a gépi tanulás figyelmen kívül hagyja.

### <a name="native-vs-internal-features"></a>Natív és a belső funkciók

Adja meg a szolgáltatások legtöbb természetes formátumban az alkalmazáshoz, kell azt a számot, egy karakterlánc vagy tömb. Ezeket a szolgáltatásokat hívják ""natív szolgáltatások. Egyéni döntési szolgáltatás egyes natív szolgáltatások fordítja le egy vagy több numerikus funkciót, nevű ""belső szolgáltatások.

Belső funkciók fordítást a következőképpen történik:

- numerikus szolgáltatások ugyanaz maradjon.
- az eszköz egy numerikus tömb több numerikus funkciót, egyet a tömb egyes elemei.
- egy karakterlánc-értékű szolgáltatás `"Name":"Value"` alakítja nevű szolgáltatást alapértelmezés szerint ki van `"NameValue"` és 1 értéket.
- Szükség esetén egy karakterlánc ábrázolhatók [tulajdonságcsomag-az-szavak](https://en.wikipedia.org/wiki/Bag-of-words_model). Egy belső szolgáltatás majd minden szó a karakterláncban, amelynek értéke a Word előfordulási jön létre.
- Belső funkciók nulla értékű hiányoznak.

### <a name="shared-vs-action-dependent-features"></a>Megosztott és művelet függő szolgáltatások összehasonlítása

Néhány funkció tekintse meg a teljes döntést, és az összes műveletet. Ezt nevezik *közös szolgáltatások*. Egy adott művelet néhány egyéb szolgáltatás vonatkoznak. Ezt nevezik *művelet függő szolgáltatások* (AD FS).

A futó példában a megosztott szolgáltatások sikerült írják le, a felhasználót és/vagy a világ állapotát. Szolgáltatások, például földrajzi hely meghatározásának, kor, és nem a felhasználó, és jelentős események most hogy. Művelet függő szolgáltatások sikerült írják le egy adott cikk, például az ebben a cikkben említett témakörök tulajdonságait.

### <a name="interacting-features"></a>Kölcsönösen egymásra ható szolgáltatások

Gyakran "interaktív" funkció: a hatását, hogy egy mások függ. Például X jellemzője, hogy a felhasználó sport iránt érdeklődik. A funkció Y, hogy egy adott cikk arra készül, sport. Majd annak hatását, hogy a szolgáltatás Y szolgáltatás magas függ X.

X és Y szolgáltatások közötti interakció fiókot használja, hozzon létre egy *négyzetösszege* szolgáltatás, amelynek értéke X\*Y. (Azt is tegyük fel például, "közötti" X és Y.) Kiválaszthatja, hogy mely funkciók párok túllépése.

> [!TIP]
> A megosztott szolgáltatás lehet átlépni művelet függő szolgáltatásokkal ahhoz, hogy a dimenziószáma befolyásolják. Egy művelet függő szolgáltatás lehet átlépni a megosztott szolgáltatások személyre szabása érdekében.

Ez azt jelenti egy megosztott szolgáltatás nem minden Active Directory összevonási szolgáltatásokkal átlépte ugyanúgy hogyan befolyásolja a műveletek. Nem minden megosztott szolgáltatás túllépte az ADF túl minden döntés befolyásolja. Az ilyen típusú szolgáltatások csökkentheti ellenszolgáltatás becslése varianciáját.

### <a name="implementation-via-namespaces"></a>Névterek keresztül végrehajtása

A portál a parancssorból"VW" keresztezett szolgáltatásokat (valamint más featurization fogalmak) megvalósítható. A szintaxis alapul a [Vowpal Wabbit](http://hunch.net/~vw/) parancssor.

Központi végrehajtására van fogalma *névtér*: szolgáltatások elnevezett részhalmazát. Egyes szolgáltatások pontosan egy névtérhez tartozik. A névtér explicit módon adható meg, ha a szolgáltatás érték megadott egyéni döntési szolgáltatás. Tekintse meg a szolgáltatás a VW parancssorban az egyetlen lehetőség.

Egy névtér, "megosztott" vagy "művelet függő": csak a megosztott szolgáltatások áll, vagy művelet függő szolgáltatásai ugyanaz a művelet csak áll.

> [!TIP]
> Tanácsos szolgáltatások burkolása explicit módon megadott névterekben. Kapcsolódó szolgáltatások ugyanazt a névteret a csoportban.

Ha a névtér nem áll rendelkezésre, a szolgáltatás automatikusan az alapértelmezett névtér.

> [!IMPORTANT]
> Szolgáltatások és névterek nem kell kell műveleteket. Különösen névtér lehet különböző műveletekhez különböző szolgáltatások. Ezen felül egy adott névtér meghatározása bizonyos műveleteket nem pedig más.

Több belső funkció, amely innen származik: ugyanazon karakterláncos natív szolgáltatás ugyanazt a névteret vannak csoportosítva. Két natív összetevő, melyek különböző névterekben különálló, kezelik, akkor is, ha a szolgáltatás névvel rendelkeznek.

> [!IMPORTANT]
> Míg hosszú, leíró névtér azonosítók közös, VW parancssori nincs megkülönböztetve névterek azonosító ugyanazzal a betűvel kezdődik. A következőkben, névtér azonosítók csak egyetlen betűk, például a `x` és `y`.

A megvalósítás részletei a következők:

- A kereszt-névterek `x` és `y`, írható `-q xy` vagy `--quadratic xy`. Ezután az egyes szolgáltatások `x` áthaladnak a jellemzők `y`. Használjon `-q x:` a kereszt- `x` minden névterű és `-q ::` a kereszt-névterek összes pár.

- Figyelmen kívül hagyása minden funkció névtérben `x`, írható `--ignore x`.

Ezek a parancsok érvényesek minden egyes művelethez külön-külön, amikor meghatározott névterek.

### <a name="estimated-average-as-a-feature"></a>Becsült átlagos szolgáltatásként

Egy gondolat kísérletet, mi lenne, az adott művelet átlagos ellenszolgáltatás Ha kapcsolatos összes döntést a választott azt? Ilyen átlagos ellenszolgáltatás intézkedésként minőségének"általános" művelet használható. Pontosan nem ismert, amikor más műveletek lettek kiválasztva inkább az egyes kapcsolatos döntések meghozásakor. Azonban becslése tanítás valósítható megerősítése keresztül. Ez a becslés minőségének általában javítja az adott idő alatt.

Ha szeretné, a "becsült átlagos ellenszolgáltatás" tartalmazzák az adott művelet szolgáltatásként. Egyéni döntési szolgáltatás volna automatikusan frissíti Ez a becslés, új adat érkezik. A szolgáltatás neve a *marginális szolgáltatás* a művelet. Marginális szolgáltatásai használhatók a machine Learning szolgáltatáshoz, és a naplózás.

Marginális szolgáltatások hozzáadását, írható `--marginal <namespace>` a VW parancssorban. Adja meg `<namespace>` a JSON-ban, az alábbiak szerint:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Ehhez a névtérhez, valamint az olyan más függő művelet funkciókat egy adott művelet beszúrása. Adja meg minden döntést azonos a definíciója `mf_name` és `action_id` kapcsolatos összes döntést a.

A marginális szolgáltatás minden műveletet. a rendszer hozzá `<namespace>`. A `action_id` bármilyen szolgáltatás nevet, amely egyedileg azonosítja a műveletet. A szolgáltatás neve legyen `mf_name`. Különösen marginal szolgáltatásokat másik `mf_name` különböző szolgáltatások tekintendők – egy másik súly van megtanulta, az egyes `mf_name`.

Az alapértelmezett használatát, hogy `mf_name` azonos minden műveletre. Ezután egy súly van megtanulta, az összes marginális szolgáltatás.

Ugyanaz a művelet több marginális funkciói ugyanazokat az értékeket, de különböző neve is megadható.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-közbeni kódolás

Dönthet úgy, hogy bizonyos funkciói bit veszélyének, amelyben mindegyik bit felel meg a lehetséges értékek tartományán. Ez a bit értéke 1 csak, ha a szolgáltatás ebbe a tartományba esik. Emiatt van egy "Forró" bit értéke 1, és a többi értéke 0. Ez a megjelenítési módja gyakran nevezik *1 közbeni kódolás*.

Az 1-közbeni kódolás az általában kategorikus szolgáltatások például az "földrajzi régió" nem rendelkezik egy eredendően jelentéssel bíró interfésztípus numerikus megjelölése. Tanácsos numerikus szolgáltatások, amelyek hatással a ellenszolgáltatás várhatóan nem lineáris. Egy adott cikk lehet például egy adott kora csoportra vonatkozó, és nem számít, hogy bárki számára fiatalabb vagy régebbi.

Bármilyen karakterlánc értékű szolgáltatás, 1-közbeni alapértelmezés szerint kódolású: egy különálló belső szolgáltatás hoznak létre minden lehetséges értéke. Automatikus 1 közbeni kódolás numerikus szolgáltatások és/vagy az egyéni tartományok jelenleg nincsenek megadva.

> [!TIP]
> A gépi tanulási algoritmusok egységes módon kezelje a belső szolgáltatásaira minden lehetséges értékei: keresztül egy közös "weight". Az 1-közbeni kódolás lehetővé teszi, hogy egy külön "weight" a minden egyes értékek tartományán. Így kisebb tartományt vezet jobb megtérüléséhez után elég adat gyűjtött, de előfordulhat, hogy növelje a jobb megtérüléséhez szerveződik szükséges adatok mennyiségét.

## <a name="feature-specification-format-and-apis"></a>Szolgáltatás specifikációja: formátum és API-k

Több kiegészítő API-k segítségével szolgáltatásokat adhat meg. Minden API-k egy közös JSON formátumot használja. Az alábbiakban az API-k és a fogalmi szinten formátumban. A meghatározás van kiegészíteni egy Swagger séma.

Az egyszerű JSON sablon szolgáltatás megadását a következőképpen történik:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Itt `<name>` és `<value>` szolgáltatás nevét és értékét. szolgáltatás, illetve állni. `<value>` egy karakterlánc, egész, lebegőpontos, olyan logikai érték vagy egy tömb lehet. A szolgáltatás nem törik névtérbe automatikusan az alapértelmezett névtérbe.

Egy tulajdonságcsomag-az-szavakat, szöveglánc szintaxissal egy különleges `"_text":"string"` helyett `"<name>":<value>`. Gyakorlatilag minden szó a karakterláncban egy külön belső szolgáltatás jön létre. A word előfordulásainak száma érvénytelen.

Ha `<name>` "_" kezdődik (és nem `"_text"`), akkor a szolgáltatás a rendszer figyelmen kívül hagyja.

> [!TIP]
> Egyes esetekben több JSON forrásból származó szolgáltatásokat egyesít. Kényelmi célokat szolgál akkor is mutatják be azokat az alábbiak szerint:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Itt `<features>` a alapvető szolgáltatás megadását, korábban definiált hivatkozik. "Beágyazási" szintnél mélyebb engedélyezettek túl. Egyéni döntési szolgáltatás automatikusan megkeresi a "legmélyebb" JSON-objektumok, amelyek úgy `<features>`.

#### <a name="feature-set-api"></a>A szolgáltatás beállítása API

A szolgáltatás beállítása API szolgáltatások listáját a korábban ismertetett JSON formátumban ad vissza. Néhány funkció beállítása API-végpontokon használható. Minden egyes végpont szolgáltatás azonosítója és egy URL-cím alapján azonosítja. A szolgáltatás közötti leképezést azonosítók állítsa be, és a portál URL-címek megadása.

Szolgáltatás beállítása API hívása szúrja be a megfelelő funkció azonosítója a megfelelő helyen a JSON-ban. A hívás művelet függő szolgáltatások automatikusan paraméterezni a művelet azonosítója. Megadhat több szolgáltatás azonosítók beállítása ugyanaz a művelet.

#### <a name="action-set-api-json-version"></a>A művelet be API (JSON-verzió)

A művelet be API, amelyben műveletek és a szolgáltatások megadott JSON verziószáma. Szolgáltatások adható meg explicit módon és/vagy a szolgáltatás beállítása API-k használatával. Megosztott szolgáltatások minden műveletre egyszer adható meg.

#### <a name="ranking-api-http-post-call"></a>Prioritás API (HTTP POST híváson)

Prioritás API verziószáma HTTP POST-hívást. A hívás törzsét műveletek és a szolgáltatások egy rugalmas JSON-szintaxis segítségével határozza meg.

Műveletek explicit módon megadott, illetve keresztül műveleti azonosítók beállítása. Amikor a rendszer észlelt egy művelet azonosítója, a megfelelő művelet beállítása API végpontra hívás végrehajtása.

Művelet beállítása API, mint a szolgáltatások adható meg explicit módon és/vagy a szolgáltatás beállítása API-k használatával. Megosztott szolgáltatások minden műveletre egyszer adható meg.