---
title: 'Oktatóanyag: Featurization és a szolgáltatás specifikáció – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Oktatóanyag a gépi tanulásban használt jellemzősítéshez és a jellemzők specifikálásához a Custom Decision Service-ben.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 9e091d3899132509d16854ebdbe14bcbc491deec
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870231"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Oktatóanyag: Featurization és a szolgáltatás meghatározása

Ez az oktatóanyag a Custom Decision Service fejlett gépi tanulási funkcióival foglalkozik. Az oktatóanyag két részből áll: [a jellemzők megállapításából](#featurization-concepts-and-implementation) és [a jellemzők specifikálásából](#feature-specification-format-and-apis). A jellemzősítés azt jelenti, hogy az adatokat „jellemzőkként” kezeljük a gépi tanuláshoz. A jellemzők specifikálása a JSON-formátumot és a jellemzők meghatározására szolgáló kiegészítő API-kat takarja.

A Custom Decision Service gépi tanulási folyamatait alapértelmezés szerint a felhasználók is átláthatják. A rendszer automatikusan kivonja a jellemzőket a tartalmakból, és egy standard megerősítő tanulási algoritmust alkalmaz. A szolgáltatás kivonása használja számos egyéb Azure Cognitive Services: [Entitáskapcsolás](../entitylinking/home.md), [Szövegelemzés](../text-analytics/overview.md), [Emotion](../emotion/home.md), és [számítógépes Látástechnológiai](../computer-vision/home.md). Amennyiben csak az alapszintű funkciókat szeretné használni, ez az oktatóanyag kihagyható.

## <a name="featurization-concepts-and-implementation"></a>Jellemzősítés: fogalmak és megvalósítás

A Custom Decision Service a döntéseket egyenként hozza meg. Minden egyes döntés során a rendszer több alternatíva, úgynevezett műveletek közül választ. Az alkalmazástól függően a döntés során egyetlen műveletet vagy műveletek (rövid) rangsorolt listája is kiválasztható.

Előfordulhat például, hogy azt szeretnénk személyre szabni, milyen cikkek jelenjenek meg egy webhely nyitóoldalán. A műveletek itt a cikkeknek felelnek meg, és a döntések arról szólnak, hogy mely cikkeket jelenítse meg a rendszer egy adott felhasználó számára.

Mindegyik műveletet egy tulajdonságok (a továbbiakban: *jellemzők*) által alkotott vektor képviseli. Az automatikusan kivont jellemzők mellett új jellemzők is megadhatók. A Custom Decision Service arra is utasítható, hogy jegyezzen fel egyes jellemzőket, de a gépi tanulás során hagyja őket figyelmen kívül.

### <a name="native-vs-internal-features"></a>Natív és belső jellemzők

A jellemzőket olyan formátumban érdemes megadni, amely az adott alkalmazásban a legkézenfekvőbb, legyen az szám, sztring vagy tömb. Ezeket a jellemzőket „natív jellemzőknek” nevezzük. A Custom Decision Service minden natív jellemzőt lefordít egy vagy több numerikus jellemzővé. Ezek az úgynevezett „belső jellemzők”.

A belső jellemzővé történő fordítás során:

- a numerikus jellemzők nem változnak.
- a numerikus tömbök több numerikus jellemzőre fordulnak le. Annyi jellemző jön létre, ahány elem van a tömbben.
- a `"Name":"Value"` (Név:Érték) formátumú, sztring értékű jellemző alapértelmezés szerint egy `"NameValue"` (NévÉrték) nevű jellemzővé fordul, amelynek az értéke 1.
- Opcionálisan a sztringeket egy [bag-of-words](https://en.wikipedia.org/wiki/Bag-of-words_model) (szóhalmaz) modell is képviselheti. A rendszer ezután a sztringben lévő mindegyik szóhoz létrehoz egy belső jellemzőt, amelynek az értéke a szó előfordulásainak a száma lesz.
- A nulla értékű belső jellemzőket a rendszer elhagyja.

### <a name="shared-vs-action-dependent-features"></a>Megosztott és műveletfüggő jellemzők

Egyes jellemzők a teljes döntésre vonatkoznak, és minden műveletnél azonosak. Ezeket *megosztott jellemzőknek* nevezzük. Más jellemzők azonban csak egy adott műveletre vonatkoznak. Ezeket *műveletfüggő jellemzőknek* (action-dependent feature, ADF) nevezzük.

Az általunk használt példában a megosztott jellemzők a felhasználót és/vagy a világ állapotát írhatják le. Olyan jellemzőkről van itt szó, mint a földrajzi hely, a felhasználó kora és neme, valamint a jelenleg zajló jelentősebb események. A műveletfüggő jellemzők megszabhatják egy adott cikk tulajdonságait, például azokat a témaköröket, amelyeket a cikk érint.

### <a name="interacting-features"></a>Egymásra ható jellemzők

A jellemzők gyakran „hatnak egymásra”, azaz az egyes jellemzők hatása más jellemzőktől is függ. Az X jellemző például arra vonatkozik, hogy a felhasználót érdekli-e a sport, az Y jellemző pedig azt adja meg, hogy egy adott cikk valamilyen sporttal foglalkozik-e. Ilyenkor az Y jellemző hatása nagy mértékben függ az X jellemzőtől.

Az X és az Y jellemző kölcsönhatásának leképezésére létrehozhat egy *négyzetes* jellemzőt, amelynek az értéke X\*Y. (Úgy is mondjuk, hogy X „keresztezve” van Y-nal.) Megválaszthatja, hogy mely jellemzőpárok legyenek keresztezve.

> [!TIP]
> A megosztott jellemzőket a rangsoruk befolyásolása érdekében érdemes műveletfüggő jellemzőkkel keresztezni. A műveletfüggő jellemzőket a hatékonyabb személyre szabás érdekében általában megosztott jellemzőkkel keresztezzük.

A műveletfüggő jellemzőkkel nem keresztezett megosztott jellemzők minden egyes műveletre ugyanúgy hatnak. A megosztott jellemzőkkel nem keresztezett műveletfüggő jellemző szintén hat mindegyik döntésre. Az ilyen típusú jellemzők csökkenthetik a jutalombecslések varianciáját.

### <a name="implementation-via-namespaces"></a>Implementálás névtereken keresztül

A keresztezett jellemzők (valamint az egyéb jellemzősítési fogalmak) a portál „VW parancssori” felületén implementálhatók. A szintaxis a [Vowpal Wabbit](http://hunch.net/~vw/) parancssori felületen alapul.

Az implementálás központi eleme a *névtér*: ez a jellemzők egy nevesített halmaza. Mindegyik jellemző egyetlen névtérhez tartozik. A névtér explicit módon adható meg, amiikor a jellemző értékét megadja a Custom Decision Service-ben. A jellemzőkre kizárólag így lehet hivatkozni a VW parancssorban.

Egy névtér „megosztott” vagy „műveletfüggő” lehet: vagy csak megosztott jellemzőket tartalmaz, vagy csak egyetlen művelet műveletfüggő jellemzőit.

> [!TIP]
> Ajánlott a jellemzőket explicit módon meghatározott névterekbe csomagolni. A kapcsolódó jellemzőket csoportosítsa egy névtérbe.

Ha a névtér nincs megadva, a rendszer a jellemzőt automatikusan az alapértelmezett névtérhez rendeli.

> [!IMPORTANT]
> A jellemzőknek és a névtereknek nem kell konzisztensnek lenniük a különböző műveletek között. Ez azt jelenti, hogy egy névtér más-más műveletekhez más-más jellemzőket is tartalmazhat. Azt is megteheti, hogy egy névteret egyes műveletekhez megad, másokhoz pedig nem.

Az ugyanabból a (sztring értékű) natív jellemzőből származó belső jellemzőket a rendszer ugyanabba a névtérbe csoportosítja. A különböző névterekben megtalálható natív jellemzőket a rendszer megkülönbözteti egymástól, még akkor is, ha ugyanaz a nevük.

> [!IMPORTANT]
> Bár szokás hosszú, leíró névtér-azonosítókat használni, a VW parancssor nem különbözteti meg azokat a névtereket, amelyeknek az azonosítója ugyanazzal a betűvel kezdődik. A továbbiakban egybetűs névtér-azonosítókat fogunk alkalmazni, például `x` vagy `y`.

Az implementálás részletei:

- Az `x` és az `y` névtér keresztezéséhez írja be a `-q xy` vagy a `--quadratic xy` kifejezést. Ekkor a rendszer az `x` minden egyes jellemzőjét keresztezi az `y` minden egyes jellemzőjével. A `-q x:` kifejezéssel az `x` az összes többi névtérrel, a `-q ::` kifejezéssel minden névtér az összes többivel keresztezhető.

- Az `x` névtérben lévő összes jellemző figyelmen kívül hagyásához használja az `--ignore x` kifejezést.

Ezeket a parancsokat a rendszer minden műveletre külön-külön alkalmazza, amikor definiál egy névteret.

### <a name="estimated-average-as-a-feature"></a>A becsült átlag, mint jellemző

Mi lenne például egy művelet átlagos jutalma, ha minden döntés során azt választanák ki? Egy ilyen átlagos jutalomérték használható a művelet „általános minőségének” mértékegységeként. Nem lehet pontosan megállapítani, hogy egyes döntések során hányszor került sor más műveletek kiválasztására. Megerősítő tanulási technikákkal azonban ez is megbecsülhető. A becslés pontossága idővel általában javul.

Dönthet úgy, hogy ezt a „becsült átlagos jutalmat” is megadja jellemzőként egy adott művelethez. A Custom Decision Service automatikusan frissíti ezt a becsült értéket, amikor új adatok érkeznek be. Ez a jellemző a művelet úgynevezett *marginális jellemzője*. A marginális jellemzők gépi tanuláshoz és naplózási célokra használhatók.

Marginális jellemzők hozzáadásához írja be a `--marginal <namespace>` kifejezést a VW parancssorba. Definiálja a `<namespace>` értékét a JSON-fájlban a következőképpen:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Illessze be ezt a névteret az adott művelet többi műveletfüggő jellemzőjével együtt. Adja meg ugyanezt a definíciót mindegyik döntéshez. Mindegyiknél ugyanazt az `mf_name` és `action_id` értéket használja.

A marginális jellemzőt a rendszer hozzáadja a `<namespace>` névtérhez tartozó mindegyik művelethez. Az `action_id` bármilyen jellemzőnév lehet, amely egyértelműen azonosítja a műveletet. A jellemző neve `mf_name` lesz. A rendszer a különböző `mf_name` névvel rendelkező marginális jellemzőket különböző jellemzőkként kezeli – mindegyik `mf_name` névhez más súlyt tanul meg.

Az alapértelmezett eljárás szerint az `mf_name` minden művelet esetében ugyanaz, így a rendszer mindegyik marginális jellemzőhöz ugyanazt a súlyt tanulja meg.

Több marginális jellemzőt is megadhat ugyanahhoz a művelethez, amelyeknek az értéke megegyezik, de a nevük eltér.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>„1-hot” (állapotonként egy) kódolás

Ha szeretné, egyes jellemzőket bitvektorokként is jelölhet, ahol minden egyes bit a lehetséges értékek egy tartományának felel meg. Az egyes bitek értéke csakis abban az esetben lesz 1, ha a jellemző ebbe a tartományba esik. Így egyetlen „hot” (kiugró) bit van, amelynek az értéke 1, míg a többié 0. Ezt a megoldást *„1hot” kódolásnak* szokták nevezni.

Az 1-hot kódolást általában kategorikus jellemzőkhöz alkalmazzák (például: „földrajzi régió”), amelyek nem rendelkeznek természetükből fakadó numerikus megfelelővel. Olyan numerikus jellemzőkhöz is tanácsos alkalmazni, amelyeknek a jutalomra gyakorolt hatása valószínűleg nem lineáris. Előfordulhat például, hogy egy adott cikk releváns egy adott korcsoport számára, az idősebbeknek vagy fiatalabbaknak azonban irreleváns.

A sztring értékű jellemzők alapértelmezés szerint 1-hot kódolásúak: a rendszer mindegyik lehetséges értékhez egy külön belső jellemzőt hoz létre. A numerikus jellemzőkre vonatkozó és/vagy az egyéni tartományokkal kiegészülő automatikus 1-hot kódolás jelenleg még nem érhető el.

> [!TIP]
> A gépi tanulási algoritmusok egy adott belső jellemző összes lehetséges értékét egységes módon kezelik: egy közös „súlyon” keresztül. Az 1-hot kódolással külön „súly” adható minden értéktartománynak. Kisebb tartományok megadásával jobb jutalmak határozhatók meg, ha már elég adat gyűlt össze, de a jobb jutalmak felé közelítéshez is több adatra lehet szükség.

## <a name="feature-specification-format-and-apis"></a>Jellemzők specifikálása: formátum és API-k

A jellemzőket különböző kiegészítő API-kon keresztül lehet specifikálni. Mindegyik API általános JSON-formátumot használ. Az alábbiakban az API-k és a formátum elméleti áttekintése látható. A specifikációt egy Swagger-séma egészíti ki.

A jellemzőspecifikáció alapvető JSON-formátuma a következőképpen néz ki:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Itt a `<name>` a jellemző nevét, a `<value>` pedig az értékét jelöli. A `<value>` lehet egy sztring, egy egész szám, egy lebegőpontos érték, egy logikai érték vagy egy tömb. A névtérbe nem csomagolt jellemzőket a rendszer automatikusan az alapértelmezett névtérhez rendeli.

Ha egy sztringet szóhalmazként (bag-of-words) szeretne kezelni, használja a speciális `"_text":"string"` szintaxist a `"<name>":<value>` helyett. A rendszer így a sztringben lévő mindegyik szóhoz létrehoz egy belső jellemzőt. A jellemző értéke a szó előfordulásainak száma.

Ha a `<name>` az „_” karakterrel kezdődik (és nem `"_text"`), a rendszer figyelmen kívül hagyja a jellemzőt.

> [!TIP]
> Esetenként előfordul, hogy különböző JSON-forrásokból származó jellemzőket kell egyesíteni. Az egyszerűség kedvéért ezeket az alábbiak szerint jelölheti:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Itt a `<features>` a korábban definiált alapvető jellemzőspecifikációra hivatkozik. Mélyebb szintű „beágyazás” is megengedett. A Custom Decision Service automatikusan megkeresi a jellemzőként (`<features>`) értelmezhető „legmélyebb” JSON-objektumokat.

#### <a name="feature-set-api"></a>Feature Set API

A Feature Set API a jellemzők egy listáját adja vissza a korábban ismertetett JSON-formátumban. Több Feature Set API-végpontot is használhat. Az egyes végpontok jellemzőkészlet-azonosító és URL-cím alapján azonosíthatók. A jellemzőkészlet-azonosítók és az URL-címek közötti leképezést a portálon lehet megadni.

A Feature Set API meghívásához illessze be a kapcsolódó jellemzőkészlet-azonosítót a JSON megfelelő helyére. A műveletfüggő jellemzők esetében a műveletazonosító automatikusan paraméterezi a hívást. Ugyanahhoz a művelethez több jellemzőkészlet-azonosítót is megadhat.

#### <a name="action-set-api-json-version"></a>Action Set API (JSON-verzió)

Az Action Set API egyik verziójában a műveletek és jellemzők JSON-formátumban vannak meghatározva. A jellemzők megadhatók explicit módon és/vagy a Feature Set API-kon keresztül. A megosztott jellemzőket egyszerre lehet megadni az összes művelethez.

#### <a name="ranking-api-http-post-call"></a>Ranking API (HTTP POST-hívás)

A Ranking API egyik verziója HTTP POST-hívásokat alkalmaz. A hívás törzse egy rugalmas JSON-szintaxissal határozza meg a műveleteket és a jellemzőket.

A műveletek megadhatók explicit módon és/vagy a műveletkészlet-azonosítókkal. Amikor a rendszer egy műveletkészlet-azonosítót talál, hívást intéz az annak megfelelő Action Set API-végpontra.

Az Action Set API esetében a jellemzők megadhatók explicit módon és/vagy a Feature Set API-kon keresztül. A megosztott jellemzőket egyszerre lehet megadni az összes művelethez.
