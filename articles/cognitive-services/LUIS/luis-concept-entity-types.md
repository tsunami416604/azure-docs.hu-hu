---
title: Entitás típusa
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entitások adatok kinyerése az utterance (kifejezés). Entitástípusok teszik lehetővé az adatok előre jelezhető kinyerése. Az entitások két típusa van: gép megtudhatta, és nem gép megtanult. Fontos tudni, hogy milyen típusú entitás utterances dolgozik.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: d12ea20f9f510b0e2d3d3512d8d8c71a3fb96eec
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372522"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Entitástípusok és a LUIS felhasználási célját

Entitások adatok kinyerése az utterance (kifejezés). Entitástípusok teszik lehetővé az adatok előre jelezhető kinyerése. Az entitások két típusa van: gép megtudhatta, és nem gép megtanult. Fontos tudni, hogy milyen típusú entitás utterances dolgozik. 

## <a name="entity-compared-to-intent"></a>Beszédszándék képest entitás

Az entitás egy szót vagy kifejezést az utterance (kifejezés), amelyeket szeretne kinyert belül jelöli. Az utterance (kifejezés) is tartalmazhat számos entitás vagy nincs minden. Egy entitás egy osztályt, beleértve a hasonló objektumok (helyek, dolog, személyek, események vagy fogalmak) gyűjteményét jelképezi. Entitások ismertetik a leképezés kapcsolódó információk, és néha nélkülözhetetlenek az alkalmazás a feladat végrehajtásához. Például egy News Search alkalmazás tartalmazhat például a "témakör", "forrás", "kulcsszó" és "közzétételi dátuma", amelyek hírkeresés a fontos adatokat. A foglalási utazási alkalmazás, a "hely", "dátum", "légitársaság" "utazási class" és "jegyek" repülési foglalási (a "Címjegyzék repülési" beszédszándék releváns) adatainak.

Ezzel a célja a teljes utterance (kifejezés) előrejelzését jelöli. 

## <a name="entities-help-with-data-extraction-only"></a>Entitások csak a adatkinyerés segítség

Címke, vagy mark entitások entitás kinyerési egyetlen, informatikai céljából nem segít az szándék előrejelzési.

## <a name="entities-represent-data"></a>Entitások adatokat képviselik.

Entitások az utterance (kifejezés) lekérni kívánt adatok. Ez lehet a nevét, a dátum, a termék neve vagy a szó bármilyen csoport. 

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|A New York-i 3 jegyek megvásárlása|Előre összeállított száma<br>Location.Destination|3<br>New York|
|London, New York-i jegyet vásárolni március 5|Location.Origin<br>Location.Destination<br>Előre összeállított datetimeV2|New York<br>London<br>2018. március 5.|

## <a name="entities-are-optional-but-highly-recommended"></a>Entitások nem kötelező, de a erősen ajánlott

Leképezések szükség, míg az entitások nem kötelező. Nem kell minden fogalom, az alkalmazásban, de csak a szükséges műveletet az ügyfélalkalmazás entitások létrehozása. 

Ha a kimondott szöveg nem rendelkezik a robot folytatásához szükséges részleteket, nem kell adja hozzá őket. Az alkalmazás kiforrottá, később is hozzáadhatja. 

Ha nem biztos abban, hogy hogyan használja az adatokat, vegye fel az néhány gyakori előre összeállított entitások például [datetimeV2](luis-reference-prebuilt-datetimev2.md), [sorszámnál](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md), és [telefonszám ](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Azaz a word-címkét

Ha a választott word vagy a word megállapodás azonos, de nem ugyanazt jelenti, nem címkét, az entitáshoz. 

A következő utterances, a word `fair` egy homográfokat van. Akkor helyesírása megegyezik, de eltérő jelentéssel rendelkezik:

|Kimondott szöveg|
|--|
|Milyen típusú megye vásárokon Seattle környékén lévő történik az Ez évi riói nyári?|
|Az a jelenlegi besorolása a Seattle felülvizsgálatra valós?|

Ha egy esemény entitás összes esemény az adatok keresésére, a word címke `fair` az első utterance (kifejezés), de nem a második.

## <a name="entities-are-shared-across-intents"></a>Entitások leképezések vannak osztva.

Entitások közötti leképezések vannak megosztva. Minden olyan egyetlen célja nem tartoznak. Szándékok és entitások társíthatók szemantikailag, de a kizárólagos kapcsolat nem.

Az utterance (kifejezés) a "Book me egy jegyet Párizs", a "Párizs" egy entitás helyre hivatkozik. FELISMERVE, az entitások, amelyek nem szerepelnek a felhasználó utterance (kifejezés), a LUIS segítségével az ügyfélalkalmazásban, válassza ki a konkrét műveleteket a felhasználói kérelem teljesítéséhez.

## <a name="mark-entities-in-none-intent"></a>Megjelölés entitások nincs leképezés

Minden szándék fog vonatkozni, beleértve a **nincs** szándékkal, kell megjelölve entitásokat, amikor csak lehetséges. Ez segít a LUIS-további információ az entitások amelyeknél megcímkézzen és szavakat Mik az entitások körül. 

## <a name="entity-status-for-predictions"></a>Entitás állapota ismeretekkel

A LUIS-portál jelzi, hogy amikor az entitást a egy példa utterance (kifejezés) kisebb, mint a megjelölt entitásból különböző vagy túl közel egy másik entitáshoz, és ezért nem egyértelmű. Ez a példa utterance (kifejezés) egy piros aláhúzás jelzi. 

További információkért lásd: [entitás állapota előrejelzéseket](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Entitástípus

A LUIS számos különböző típusú entitásokat kínál. Válassza ki az entitást, hogyan szeretné használni az adatokat, és hogyan azt kell finomítja után, ki kell olvasni alapján.

Entitások kinyerhetők a machine learning, amely lehetővé teszi a LUIS, és folytathatja az entitás hogyan jelenik meg az utterance (kifejezés). Entitások kinyerhetők a gépi tanulási, pontos szöveget vagy egy adott reguláris kifejezésnek megfelelő nélkül. Minták entitások kinyerhetők a vegyes megvalósításáról. 

Az entitás ki kell olvasni, miután az Entitásadatok információ egyetlen egységként jelölt, vagy más entitásokkal az információk az ügyfélalkalmazás egységet kombinálva.

|Machine-learned|Jelölheti meg|Oktatóanyag|Példa<br>Válasz|Entitástípus|Cél|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Összetett**](#composite-entity)|A csoportosítási entitások, függetlenül attól, entitás típusa.|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Hierarchikus**](#hierarchical-entity)|Egyszerű entitások csoportja.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**lista**](#list-entity)|Elemek listáját és a szinonimákat a pontos egyezés egyeztetése ki kell olvasni.|
|Vegyes||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Az entitás ahol entitás végén tekintve nehéz lenne meghatározni.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Előre összeállított**](#prebuilt-entity)|Már betanított különböző típusú adatok kinyeréséhez.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Reguláris kifejezés**](#regular-expression-entity)|Reguláris kifejezés használatával összevetheti a szöveget.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simple**](#simple-entity)|Egyetlen fogalma az szót vagy kifejezést tartalmaz.|

Egyetlen gép megismert entitások kell példa megcímkézzen számára minden szándékot kell megjelölni. Gép-megismert entitások működnek a legjobban keresztül tesztelésekor [végpont lekérdezések](luis-concept-test.md#endpoint-testing) és [végpont kimondott szöveg felülvizsgálata](luis-how-to-review-endoint-utt.md). 

Pattern.any entitások kell kell megjelölni a [minta](luis-how-to-model-intent-pattern.md) sablon példákat, nem a felhasználó szándékának példák. 

Vegyes entitások entitás észlelési módszerek kombinációját használják.

## <a name="composite-entity"></a>Összetett entitást

Egy összetett entitást épül fel más entitásokkal, például az előre összeállított entitások egyszerű, reguláris kifejezés, a listából, és a hierarchikus entitásokat. A különálló entitások teljes entitás űrlap. 

Ehhez az entitáshoz egy jó fér el, ha az adatokat:

* Kapcsolódnak egymáshoz. 
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Többféle típusú entitás használja.
* Csoportosítva és dolgozza fel az ügyfélalkalmazás az információk egy egységként kell.
* Rendelkezik a különböző gépi tanulási igénylő felhasználó kimondott szöveg.

![Összetett entitást](./media/luis-concept-entities/composite-entity.png)

[Oktatóanyag](luis-tutorial-composite-entity.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Hierarchikus entitás

Egy hierarchikus entitás a gyermekek nevű kontextusban megismert egyszerű entitások egy kategóriát.

Ehhez az entitáshoz egy jó fér el, ha az adatokat:

* Egyszerű entitás.
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Adott szó választott használatával minden egyes gyermek entitásnak jelzi. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Gyermekek gyakran az azonos utterance (kifejezés) találhatók. 
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

Ne használjon, ha:

* Szüksége lesz egy entitás, amely rendelkezik a pontos szöveg egyezések függetlenül környezet számára. Használja a [entitás listában](#list-entity) helyette. 
* Egy entitás egy szülő-gyermek kapcsolat más entitás típusokkal kell rendelkeznie. Használja a [összetett entitást](#composite-entity).

![a hierarchikus](./media/luis-concept-entities/hierarchical-entity.png)

[Oktatóanyag](luis-quickstart-intent-and-hier-entity.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>Szerepkörök és a hierarchikus entitások

[Szerepkörök](luis-concept-roles.md#roles-versus-hierarchical-entities) a minta megoldása ugyanez a probléma, hierarchikus entitások viszont összes entitástípusra vonatkozik. Szerepkörök jelenleg csak az minták érhetőek el. A leképezések Példa utterances szerepkörök nem érhetők el.  

## <a name="list-entity"></a>Listaentitás

Lista entitások mellett azok szinonimáit kapcsolódó szavakat rögzített, lezárt csoportját képviselik. A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. 

Az entitás egy jó fér el, ha a szöveges adatokat:

* Olyan ismert.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. A LUIS túli szöveg pontos egyezés a lista nem használ. Amely szerint elnevezéseket és más változata létezik egy lista entitással nincs feloldva. Kezelheti a változatok, fontolja meg egy [minta](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) választható szöveg szintaxissal.

![lista entitás](./media/luis-concept-entities/list-entity.png)

[Oktatóanyag](luis-quickstart-intent-and-list-entity.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.any entitás

Pattern.any pedig változó hosszúságú való megjelöléséhez, ahol az entitás kezdődik és végződik csak egy minta sablon utterance (kifejezés) használja.  

Az entitás az a jó, mikor igazítása:

* Az entitás a befejezési összetéveszthetők az utterance (kifejezés) fennmaradó szövegét. 
[Oktatóanyag](luis-tutorial-pattern.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#patternany-entity-data)

**Példa**  
Adja meg a keresési ügyfélalkalmazás az könyvek címe alapján, a pattern.any kibontja a teljes cím. A könyv Search használatával pattern.any sablon utterance (kifejezés) `Was {BookTitle} written by an American this year[?]`. 

Az alábbi táblázat minden egyes sor az utterance (kifejezés) két verziója van. A felső utterance (kifejezés) hogyan LUIS kezdetben jelenik meg az utterance (kifejezés), ahol nem egyértelmű a könyv címmel kezdődik és ér véget. Az alsó utterance (kifejezés), hogyan LUIS fogja, hogy a könyv cím helyen a kinyerési mintát esetén. 

|Kimondott szöveg|
|--|
|Az ember akik tévesen His Feleségemmel Hat és más klinikai ellenőrző élményéért-amerikai ebben az évben?<br>Volt **a Man akik tévesen His Feleségemmel Hat és más klinikai ellenőrző** -amerikai által írt ebben az évben?|
|Az Amerikai által írt ebben az évben béka Pajamas a fele alvó állapotban volt?<br>Volt **fél alvó állapotban lévő béka Pajamas** -amerikai által írt ebben az évben?|
|Az adott Szomorúság kisfejű tortaszelet lett: Egy új, az Amerikai által írt ebben az évben?<br>Volt **az adott Szomorúság kisfejű tortaszelet: Egy új** -amerikai által írt ebben az évben?|
|Kapott, a saját zsebébe tenni egy Wocket van! az Amerikai által írt ebben az évben?<br>Volt **a saját zsebébe tenni egy Wocket van!** az Amerikai által írt ebben az évben?|

## <a name="prebuilt-entity"></a>Előre összeállított entitások

Előre összeállított entitások olyan beépített, amelyek közös fogalmaival, mint például az e-mailt, URL-CÍMÉT és telefonszámát. Előre összeállított entitások neve vannak fenntartva. [Az összes előre összeállított entitások](luis-prebuilt-entities.md) kerülnek, amely az alkalmazás a végpont előrejelzési lekérdezés kerül sor, ha azok találhatók meg az utterance (kifejezés). 

Az entitás az a jó, mikor igazítása:

* Az adatok egy gyakori alkalmazási helyzet, a nyelv kulturális környezet előre összeállított entitások által támogatott illeszkedik. 

Előre összeállított entitások is hozzáadható és bármikor eltávolítható.

![Előre összeállított entitások száma](./media/luis-concept-entities/number-entity.png)

[Oktatóanyag](luis-tutorial-prebuilt-intents-entities.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#prebuilt-entity-data)

Ezek az előre összeállított entitások némelyike vannak meghatározva a nyílt forráskódú [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. A megadott kulturális környezet vagy a szervezet jelenleg nem támogatott, ha járulnak hozzá a projekthez. 

### <a name="troubleshooting-prebuilt-entities"></a>Előre összeállított entitások hibaelhárítása

A LUIS-portálon Ha egy előre létrehozott entitás van megjelölve az egyéni entitás helyett lehetősége van néhány bemutatja, hogyan a probléma megoldásához.

Az előre összeállított entitások adva az alkalmazáshoz fog _mindig_ adható vissza, akkor is, ha az utterance (kifejezés) kell bontsa ki az egyéni entitásaihoz tartozó ugyanazt a szöveget. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Példa utterance (kifejezés) címkézett entitás módosítása

Ha az előre összeállított entitások azonos szöveg vagy jogkivonatok, az egyéni entitáshoz, jelölje ki a szöveget a példa utterance (kifejezés), és módosítsa a címkézett utterance (kifejezés). 

Ha az előre összeállított entitások további szöveg és tokenek, mint az egyéni entitás van megjelölve, akkor több lehetősége, hogy hogyan a probléma megoldásához:

* [Távolítsa el a példa utterance (kifejezés)](#remove-example-utterance-to-fix-tagging) metódus
* [Távolítsa el az előre összeállított entitások](#remove-prebuilt-entity-to-fix-tagging) metódus

#### <a name="remove-example-utterance-to-fix-tagging"></a>Távolítsa el a címkézés kijavítani példa utterance (kifejezés) 

Először más, hogy törli a példa utterance (kifejezés), és szoftveres átképezése az alkalmazást. Csak a word újból fel vagy kifejezés, amely az entitást, mint egy példa utterance (kifejezés), majd az entitás- és train megjelölése. Most adja hozzá az vissza, az előre létrehozott entitás és az eredeti példa utterance (kifejezés). Az egyéni entitás továbbra is az előre létrehozott entitás helyett lesz megjelölve. 

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Távolítsa el a címkézés kijavítani előre összeállított entitások

A második lehetősége, hogy az előre összeállított entitások eltávolíthatja az alkalmazásból, majd címkét az egyéni entitás az a példában utterance (kifejezés), majd vegye fel az előre összeállított entitások újból az alkalmazást. Ez a javítás feltételezi, hogy az előre összeállított entitások nem egy összetett entitást része. 

## <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás 

Reguláris kifejezés ideális nyers utterance (kifejezés) szöveget. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változat.  Reguláris kifejezések egyeztetésének szinten karaktert, nem a token szint helyesírás-ellenőrzésének átalakítás után alkalmazza. Ha a reguláris kifejezés túl összetett, például sok zárójelben, Ön nem adhat hozzá a kifejezés a modellbe. Használja a része, de nem minden a [.NET reguláris kifejezés](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) könyvtár. 

Az entitás az a jó, mikor igazítása:

* Az adatok semmilyen változást, amely egyben egységes konzisztens módon formázott.
* A reguláris kifejezés nem kell több mint 2 beágyazási szintet. 

![Reguláriskifejezés-entitás](./media/luis-concept-entities/regex-entity.png)

[Oktatóanyag](luis-quickstart-intents-regex-entity.md)<br>
[Entitás példa JSON-válasz](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Egyszerű entitás 

Egy egyszerű entitás egy általános entitás, amely ismerteti az egy egyetlen fogalom, és megtanulta van, a gép megtanult környezetből. Mivel az egyszerű entitások általában például cégnevet, termékneveket vagy más kategóriák nevek nevét, adjon hozzá egy [kifejezéslista](luis-concept-feature.md) egy egyszerű entitás használatakor a jel használt nevek növelése érdekében. 

Az entitás az a jó, mikor igazítása:

* Az adatok nem konzisztens módon formázott, de ugyanazt jelenti. 

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

[Oktatóanyag](luis-quickstart-primary-and-secondary-data.md)<br/>
[Entitás példaválasz](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Entitás korlátok

Felülvizsgálat [korlátok](luis-boundaries.md#model-boundaries) megértéséhez, hogy hány különböző típusú entitás is hozzáadhat egy modellt.

## <a name="composite-vs-hierarchical-entities"></a>Hierarchikus entitások összetett vs

Összetett hierarchikus alá tartozó alanyokra és egyaránt rendelkezik szülő-gyermek típusú kapcsolatokat és megtanult gép. A machine learning lehetővé teszi, hogy a LUIS tudni, hogy az entitások különböző környezetekben (szavak elrendezésének) alapján. Összetett entitások olyan rugalmasabb, mivel lehetővé teszik különböző entitástípusok gyermekeként. Egy hierarchikus entitás gyermekek csak egyszerű entitásokat is. 

|Típus|Cél|Példa|
|--|--|--|
|Hierarchikus|Szülő-gyermek típusú, egyszerű entitások|Location.Origin=New York<br>Location.Destination=London|
|Összetett|Szülő-gyermek típusú entitások: előre elkészített, list, egyszerű, hierarchikus| szám = 3<br>lista első osztályú =<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha több, mint az entitások maximális száma 

Előfordulhat, hogy szeretné használni a hierarchikus és összetett entitásokat. Hierarchikus entitások tükrözik, amely jellemzőkkel rendelkeznek, vagy egy kategória tagja entitások közötti kapcsolat. A gyermekentitások tagjai az összes, a szülő kategória. Ha például egy hierarchikus PlaneTicketClass nevű előfordulhat, hogy entitásnak EconomyClass és FirstClass gyermekentitások. A hierarchia mélységét csak egy szintjének átnyúlik.  

Összetett entitások egész részét jelölik. Ha például egy összetett PlaneTicketOrder nevű előfordulhat, hogy entitásnak gyermekentitások légitársaság, cél, DepartureCity, DepartureDate és PlaneTicketClass. Létrehozhat egy összetett entitást a már meglévő egyszerű entitások, hierarchikus entitásokat, vagy előre összeállított entitások gyermekei.  

A LUIS is biztosít a lista entitás típusa, amely nem a gép megtudhatta, de lehetővé teszi, hogy a rögzített értékek listáját adja meg a LUIS-alkalmazás. Lásd: [LUIS határok](luis-boundaries.md) tekintse át a listában entitástípus korlátait mutató hivatkozás. 

Ha már tekinthető hierarchikus, összetett, és az entitások listája, és továbbra is több, mint a korlát, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön a rendszer részletes adatait, lépjen a [LUIS](luis-reference-regions.md#luis-website) webhelyet, és válassza ki **támogatási**. Ha az Azure-előfizetés tartalmazza a támogatási szolgálathoz, lépjen kapcsolatba [technikai Azure-támogatás](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>További lépések

Ismerje meg a helyes kapcsolatos alapvető fogalmakat [beszédmódok](luis-concept-utterance.md). 

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
