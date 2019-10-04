---
title: Entity types – LUIS
titleSuffix: Azure Cognitive Services
description: 'Az entitások kinyerik az adatok kinyerését. Az entitások típusai kiszámítható kitermelést biztosítanak. Az entitások két típusa létezik: a gépi megtanult és a nem gépi megtanult. Fontos tudni, hogy milyen típusú entitással dolgozik a hosszúságú kimondott szöveg-ben.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 9919b6d07e874bd306bdba9da2cd3357bedc48f0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564007"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Az entitások típusai és céljai a LUIS-ben

Az entitások kinyerik az adatok kinyerését. Az entitások típusai kiszámítható kitermelést biztosítanak. Az entitások két típusa létezik: a gépi megtanult és a nem gépi megtanult. Fontos tudni, hogy milyen típusú entitással dolgozik a hosszúságú kimondott szöveg-ben. 

## <a name="entity-compared-to-intent"></a>Beszédszándék képest entitás

Az entitás egy szót vagy kifejezést az utterance (kifejezés), amelyeket szeretne kinyert belül jelöli. Az utterance (kifejezés) is tartalmazhat számos entitás vagy nincs minden. Előfordulhat, hogy az ügyfélalkalmazás az entitást a feladat elvégzéséhez vagy a felhasználóhoz való bevezetéséhez szükséges számos választási útmutatóként használja. 

Egy entitás:

* Egy osztályt jelöl, beleértve a hasonló objektumok (helyek, dolgok, személyek, események vagy fogalmak) gyűjteményét. 
* A szándékra vonatkozó információkat ismerteti


Például egy News Search alkalmazás tartalmazhat például a "témakör", "forrás", "kulcsszó" és "közzétételi dátuma", amelyek hírkeresés a fontos adatokat. Az utazási foglalási alkalmazásokban a "location", a "date", a "Airline", az "utazási osztály" és a "jegyek" a repülési foglalás legfontosabb adatai (a "Book Flight" kifejezésre vonatkozik).

Ezzel a célja a teljes utterance (kifejezés) előrejelzését jelöli. 

## <a name="entities-help-with-data-extraction-only"></a>Az entitások csak az adatkivonatolást segítik

Az entitásokat csak az entitások kinyerése céljából címkézheti vagy jelölheti meg, de nem segít a szándék-előrejelzésben.

## <a name="entities-represent-data"></a>Entitások adatokat képviselik.

Entitások az utterance (kifejezés) lekérni kívánt adatok. Ez lehet a nevét, a dátum, a termék neve vagy a szó bármilyen csoport. 

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|A New York-i 3 jegyek megvásárlása|Előre összeállított száma<br>Location.Destination|3<br>New York|
|London, New York-i jegyet vásárolni március 5|Location.Origin<br>Location.Destination<br>Előre összeállított datetimeV2|New York<br>London<br>2018. március 5.|

## <a name="entities-are-optional-but-highly-recommended"></a>Entitások nem kötelező, de a erősen ajánlott

Leképezések szükség, míg az entitások nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, de csak azok számára, akiknek szükségük van az ügyfélalkalmazás beavatkozására. 

Ha a kimondott szöveg nem rendelkezik a robot folytatásához szükséges részleteket, nem kell adja hozzá őket. Az alkalmazás kiforrottá, később is hozzáadhatja. 

Ha nem tudja, hogyan használja fel az adatokat, adjon hozzá néhány gyakori előre elkészített entitást, például a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [sorszámot](luis-reference-prebuilt-ordinal.md), az [e-mailt](luis-reference-prebuilt-email.md)és a [telefonszámot](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Azaz a word-címkét

Ha a választott word vagy a word megállapodás azonos, de nem ugyanazt jelenti, nem címkét, az entitáshoz. 

A következő utterances, a word `fair` egy homográfokat van. Akkor helyesírása megegyezik, de eltérő jelentéssel rendelkezik:

|Kimondott szöveg|
|--|
|Milyen típusú megye vásárokon Seattle környékén lévő történik az Ez évi riói nyári?|
|Az a jelenlegi besorolása a Seattle felülvizsgálatra valós?|

Ha egy esemény entitás összes esemény az adatok keresésére, a word címke `fair` az első utterance (kifejezés), de nem a második.

## <a name="entities-are-shared-across-intents"></a>Entitások leképezések vannak osztva.

Entitások közötti leképezések vannak megosztva. Minden olyan egyetlen célja nem tartoznak. A szándékok és entitások szemantikailag társíthatók, de nem kizárólagos kapcsolat.

A "Párizs", a "Book to Paris" kifejezésre való lemondás után a "Paris" egy olyan entitás, amely a helyükre hivatkozik. A felhasználó teljességében említett entitások felismerésével a LUIS segít az ügyfélalkalmazás számára kiválasztani azokat a műveleteket, amelyek a felhasználó kérelmének teljesítéséhez szükségesek.

## <a name="mark-entities-in-none-intent"></a>Entitások megjelölése nincs szándékban

Az összes szándéknak, beleértve a **none** szándékot is, a jelölt entitásokkal kell rendelkeznie, ha lehetséges. Ez segít a LUIS-további információ az entitások amelyeknél megcímkézzen és szavakat Mik az entitások körül. 

## <a name="entity-status-for-predictions"></a>Entitás állapota ismeretekkel

A LUIS-portálon megtudhatja, hogy ha egy ilyen kifejezésben szereplő entitás különbözik a megjelölt entitástól, vagy túl van-e egy másik entitáshoz, ezért nem egyértelmű. Ezt a példában szereplő vörös aláhúzás jelzi. 

További információ: entitások [állapotának előrejelzése](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Entitástípus

A LUIS számos típusú entitást kínál. Válassza ki az entitást az Adatkivonatok és a kinyerés utáni megjelenítésük alapján.

Az entitások gépi tanulással is kivonhatók, ami lehetővé teszi a LUIS számára, hogy folytassa a tanulást arról, hogy az entitás hogyan jelenik meg a leválasztásban. Az entitások gépi tanulás nélkül is kiállíthatók, a pontos szöveggel vagy reguláris kifejezéssel. A mintákban lévő entitások vegyes implementációval kivonhatók. 

Az entitás kibontása után az entitás adatai egyetlen adategységként vagy más entitásokkal kombinálhatók az ügyfél által használható információk egységének megalkotása céljából.

|Gépi megtanult|Jelölheti|Oktatóanyag|Példa<br>Válasz|Entitástípus|Cél|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Összetett**](#composite-entity)|Entitások csoportosítása, az entitás típusától függetlenül.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Listáját**](#list-entity)|A pontos szöveges egyezéssel kinyert elemek és szinonimáik listája.|
|Vegyes||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Minta. any**](#patternany-entity)|Az entitás, amelyben nehéz meghatározni az entitás végét.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Már be van tanítva különféle típusú adatok kinyerésére.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Reguláris kifejezés**](#regular-expression-entity)|Reguláris kifejezést használ a szöveg egyeztetéséhez.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Egyszerű**](#simple-entity)|Egyetlen fogalmat tartalmaz a Word vagy a kifejezésben.|

Csak a géppel megtanult entitásokat kell megjelölni a példában szereplő hosszúságú kimondott szöveg. A gép által megtanult entitások a legjobban [](luis-concept-test.md#endpoint-testing) működnek, ha végponti lekérdezésekkel és a [végpontok hosszúságú kimondott szöveg áttekintésével](luis-how-to-review-endoint-utt.md)vannak tesztelve. 

Minta. minden entitást meg kell adni a [minta](luis-how-to-model-intent-pattern.md) sablon példákban, nem pedig a leképezési felhasználói példákban. 

A vegyes entitások az entitások észlelési módszereinek kombinációját használják.

## <a name="machine-learned-entities-use-context"></a>Gépi megtanult entitások használata kontextus

A géppel megtanult entitások a teljes kontextusban tanulnak. Így az elhelyezés variációja jelentős hosszúságú kimondott szöveg jelent. 

## <a name="non-machine-learned-entities-dont-use-context"></a>A nem gépi megtanult entitások nem használnak kontextust

A következő nem gépi megtanult entitások nem vesznek figyelembe teljes kontextust a megfelelő entitások esetén: 

* [Előre összeállított entitások](#prebuilt-entity)
* [Regex entitások](#regular-expression-entity)
* [Entitások listája](#list-entity) 

Ezek az entitások nem igénylik a modell címkézését vagy betanítását. Miután hozzáadta vagy konfigurálta az entitást, a rendszer kinyeri az entitásokat. A kompromisszum az, hogy ezek az entitások nem egyeznek meg, ha a kontextust figyelembe vették, a egyezés nem lett volna elérhető. 

Ez az új modellekben gyakran előforduló entitások listázásával történik. Létrehozhatja és tesztelheti a modelljét egy lista entitással, de amikor közzéteszi a modellt, és fogad lekérdezéseket a végponttól, a modell a kontextus hiánya miatt nem egyezik. 

Ha a szavakat vagy kifejezéseket szeretné egyeztetni, és a kontextust figyelembe venni, két lehetőség közül választhat. Az első egy egyszerű entitás használata egy kifejezési listával párosítva. A kifejezések listája nem lesz felhasználva a megfeleltetéshez, hanem a viszonylag hasonló szavak (felcserélhető lista) jelzésére is használható. Ha pontos egyezést kell adni a kifejezések listájának változatai helyett, használja az alább ismertetett szerepkörrel rendelkező lista entitást.

### <a name="context-with-non-machine-learned-entities"></a>Nem gépi megtanult entitások kontextusa

Ha azt szeretné, hogy a nem gépi megtanult entitások esetében a részletek kontextusát is figyelembe kell [](luis-concept-roles.md)vennie, használja a szerepköröket.

Ha olyan nem gépi megtanult entitást használ, mint például az [előre](#prebuilt-entity)összeállított entitások, a [regex](#regular-expression-entity) entitások vagy a [lista](#list-entity) entitások, amelyek megfelelnek a kívánt példánynak, érdemes lehet két szerepkörrel rendelkező entitást létrehozni. Az egyik szerepkör rögzíti, hogy mit keres, és egy szerepkör rögzíti, hogy mit keres. A két verziót is meg kell adni, például hosszúságú kimondott szöveg.  

## <a name="composite-entity"></a>Összetett entitást

Az [összetett entitások](reference-entity-composite.md) más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap. 

## <a name="list-entity"></a>Listaentitás

Az [entitások listája](reference-entity-list.md) a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján. Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. 

## <a name="patternany-entity"></a>Pattern.any entitás

[Minta.](reference-entity-pattern-any.md) a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik.  

## <a name="prebuilt-entity"></a>Előre összeállított entitások

Az előre elkészített entitások olyan beépített típusok, amelyek közös fogalmakat, például e-maileket, URL-címeket és telefonszámokat képviselnek. Előre összeállított entitások neve vannak fenntartva. A rendszer az alkalmazáshoz hozzáadott [összes előre elkészített entitást](luis-prebuilt-entities.md) visszaadja a végpont-előrejelzési lekérdezésben, ha azok a teljes kifejezésben találhatók. 

Az entitás jól illeszkedik, ha:

* Az adatai közös használati esetnek felelnek meg, amelyet az előre elkészített entitások támogatnak a nyelvi kulturális környezethez. 

Az előre elkészített entitások bármikor hozzáadhatók és eltávolíthatók.

![Előre összeállított entitások száma](./media/luis-concept-entities/number-entity.png)

[Oktatóanyag](luis-tutorial-prebuilt-intents-entities.md)<br>
[Példa JSON-válasz entitásra](luis-concept-data-extraction.md#prebuilt-entity-data)

Ezen előre összeépített entitások némelyike a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projektben van meghatározva. A megadott kulturális környezet vagy a szervezet jelenleg nem támogatott, ha járulnak hozzá a projekthez. 

### <a name="troubleshooting-prebuilt-entities"></a>Előre összeépített entitások hibaelhárítása

Ha a LUIS-portálon egy előre összeállított entitás nem az egyéni entitás helyett van címkézve, néhány lehetőség közül választhat, hogy miként lehet ezt elhárítani.

Az alkalmazáshoz hozzáadott előre elkészített entitásokat a rendszer _mindig_ visszaadja, még akkor is, ha a teljes érték kinyeri az egyéni entitásokat ugyanahhoz a szöveghez. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Címkézett entitás módosítása – példa: Kimondás

Ha az előre elkészített entitás ugyanaz a szöveg vagy jogkivonatok, mint az egyéni entitás, válassza ki a példa szövegét, és módosítsa a címkézett szöveget. 

Ha az előre elkészített entitás több szöveggel vagy jogkivonattal van megjelölve, mint az egyéni entitás, több lehetőség közül választhat:

* [Példa](#remove-example-utterance-to-fix-tagging) -Kimondás módszerének eltávolítása
* Az [előre összeépített entitás](#remove-prebuilt-entity-to-fix-tagging) metódusának eltávolítása

#### <a name="remove-example-utterance-to-fix-tagging"></a>Példa a címkézés kijavítására 

Első lépésként távolítsa el a példa kiválasztását. 

1. Törölje a példa megnyilatkozása.
1. Az alkalmazás újratanítása. 
1. Adja hozzá a vissza csak azt a szót vagy kifejezést, amely az entitás, amely előre elkészített entitásként van megjelölve, teljes példaként. A szó vagy kifejezés továbbra is az előre elkészített entitás jelölése lesz. 
1. Válassza ki az entitást a példában a Kimondás **oldalon,** és váltson át az egyéni entitásra, és ismételje meg a betanítást. Ennek meg kell akadályoznia, hogy a LUIS pontosan megjelölje ezt a szöveget az előre összeépített entitásként az adott szöveget használó hosszúságú kimondott szöveg. 
1. Adja hozzá a teljes eredeti példát a szándékhoz. Az egyéni entitást továbbra is az előre elkészített entitás helyett kell megjelölni. Ha az egyéni entitás nincs megjelölve, további példákat kell hozzáadnia a szöveghez a hosszúságú kimondott szöveg-ben.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Előre elkészített entitás eltávolítása a címkézés javításához

1. Távolítsa el az előre elkészített entitást az alkalmazásból. 
1. A **cél** lapon jelölje meg az egyéni entitást a példa Kimondás oldalon.
1. Tanítsa be az alkalmazást.
1. Adja hozzá az előre elkészített entitást az alkalmazáshoz, és tanítsa be az alkalmazást. Ez a javítás feltételezi, hogy az előre elkészített entitás nem része egy összetett entitásnak.

## <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás 

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

## <a name="simple-entity"></a>Egyszerű entitás

A [egyszerű entitás](reference-entity-simple.md) egy gép megtanult érték. Lehet, hogy egy szót vagy kifejezést.
## <a name="entity-limits"></a>Entitás korlátok

Felülvizsgálat [korlátok](luis-boundaries.md#model-boundaries) megértéséhez, hogy hány különböző típusú entitás is hozzáadhat egy modellt.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha több, mint az entitások maximális száma 

Előfordulhat, hogy az entitás-szerepkörökkel együtt összetett entitásokat kell használnia.

Összetett entitások egész részét jelölik. Ha például egy összetett PlaneTicketOrder nevű előfordulhat, hogy entitásnak gyermekentitások légitársaság, cél, DepartureCity, DepartureDate és PlaneTicketClass.

A LUIS olyan típusú entitást is biztosít, amely nem a gép által megismert, de lehetővé teszi, hogy a LUIS-alkalmazás meghatározza az értékek rögzített listáját. Lásd: [LUIS határok](luis-boundaries.md) tekintse át a listában entitástípus korlátait mutató hivatkozás. 

Ha ezeket az entitásokat tekinti át, és továbbra is nagyobbnak kell lennie a korlátnál, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön a rendszer részletes adatait, lépjen a [LUIS](luis-reference-regions.md#luis-website) webhelyet, és válassza ki **támogatási**. Ha az Azure-előfizetés tartalmazza a támogatási szolgálathoz, lépjen kapcsolatba [technikai Azure-támogatás](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>További lépések

Ismerje meg a helyes kapcsolatos alapvető fogalmakat [beszédmódok](luis-concept-utterance.md). 

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.
