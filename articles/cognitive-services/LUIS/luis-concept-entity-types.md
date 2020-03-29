---
title: Entitástípusok - LUIS
titleSuffix: Azure Cognitive Services
description: 'Entitások kinyerése adatokat az utterance (kifejezés). Az entitástípusok az adatok kiszámítható kinyerését biztosítják. Az entitásoknak két típusa van: gép megtanult és nem gép által megtanult. Fontos tudni, hogy milyen típusú entitásdolgozik a kimondott szöveg.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221028"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entitások és céljuk a LUIS-ban

Az entitások elsődleges célja, hogy az ügyfélalkalmazás számára kiszámítható adatok kinyerését. Egy _opcionális_, másodlagos célja, hogy a szándék vagy más entitások leírókkal történő előrejelzésének növelése.

Az entitásoknak két típusa van:

* gép-tanult - a kontextusból
* nem gépmegtanult - pontos szövegegyezéshez, mintaegyezéshez vagy előre összeállított entitások általi észleléshez

A gép által megtanult entitások az adatkinyerési lehetőségek legszélesebb körét biztosítják. A nem gépben megtanult entitások szövegegyeztetéssel dolgoznak, és egymástól függetlenül vagy a gép által megtanult entitás [megkötéseként](#design-entities-for-decomposition) használhatók.

## <a name="entities-represent-data"></a>Az entitások adatokat jelölnek

Az entitások olyan adatok, amelyeket le szeretne kérni az utterance (kifejezés), például nevek, dátumok, terméknevek vagy bármely jelentős szócsoport. Az utterance (kifejezés) számos entitást tartalmazhat, vagy egyáltalán nem. Előfordulhat, _hogy_ egy ügyfélalkalmazásnak szüksége van az adatokra a feladat végrehajtásához.

Entitások kell címkézni következetesen az összes betanítási utterances minden szándék egy modellben.

 Saját entitásokat definiálhat, vagy előre összeállított entitásokat használhat, hogy időt takarítson meg az olyan közös fogalmak számára, mint a [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinális](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)és [telefonszám](luis-reference-prebuilt-phonenumber.md).

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|Vásároljon 3 jegyet New York-ba|Előre összeállított szám<br>Hely.Cél|3<br>New York|
|Vegyél egy jegyet New York-London március 5-én|Hely.Származási<br>Hely.Cél<br>Előre összeállított datetimeV2|New York<br>London<br>2018. március 5.,|

### <a name="entities-are-optional"></a>Az entitások nem kötelezőek

Míg a leképezések szükségesek, entitások nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden koncepciójához, de csak az ügyfélalkalmazás művelethez szükséges entitásokat.

Ha a kimondott szöveg nem rendelkezik az ügyfélalkalmazás által igényelt adatokkal, nem kell entitásokat hozzáadnia. Az alkalmazás fejlesztése és az adatok új szükségességének azonosításához, a luis modell később hozzáadhatja a megfelelő entitásokat.

## <a name="entity-compared-to-intent"></a>Entitás a szándékhoz képest

Az entitás egy adatfogalmat képvisel az utterance (kifejezés) kinyerése belül.

Az utterance (kifejezés) adott esetben entitásokat is tartalmazhat. Összehasonlításképpen, az utterance (kifejezés) szándék ának előrejelzése _szükséges,_ és a teljes utterance (kifejezés) jelöli. A LUIS-nak példautters egy szándék tartalmazza.

Vegye figyelembe a következő 4 utterances:

|Kimondott szöveg|Előre jelzett szándék|Kinyert entitások|Magyarázat|
|--|--|--|--|
|Súgó|segítség|-|Nincs mit kiszedni.|
|Küldj valamit|küldésValami|-|Nincs mit kiszedni. A modell nincs betanítva ebben `something` a környezetben kivonat, és nincs címzett sem.|
|Bob ajándék küldése|küldésValami|`Bob`, `present`|A modell be van tanítva a [personName](luis-reference-prebuilt-person.md) előre összeállított `Bob`entitás, amely kibkérte a nevet. A gép által megtanult entitást használták a kibontáshoz. `present`|
|Küldj Bobnak egy doboz csokoládét.|küldésValami|`Bob`, `box of chocolates`|A két fontos adatdarabot `Bob` `box of chocolates`és a t. entitások kinyerték.|

## <a name="design-entities-for-decomposition"></a>Tervezési entitások a bomláshoz

Jó entitás tervezés, hogy a legfelső szintű entitás egy gép által megtanult entitás. Ez lehetővé teszi az entitás tervének időbeli módosítását és az **alösszetevők (gyermekentitások)** használatát, opcionálisan **korlátozásokkal** és **leírókkal,** hogy a legfelső szintű entitást az ügyfélalkalmazás által szükséges részekre bomlanak le.

A bontás tervezése lehetővé teszi a LUIS számára, hogy az ügyfélalkalmazásnak az entitásmegoldás mély fokát adja vissza. Ez lehetővé teszi, hogy az ügyfélalkalmazás az üzleti szabályokra összpontosítson, és az adatok feloldását a LUIS-ra hagyja.

### <a name="machine-learned-entities-are-primary-data-collections"></a>A gépáltal megtanult entitások elsődleges adatgyűjtemények

[**A gép által megtanult entitások**](tutorial-machine-learned-entity.md) a legfelső szintű adategység. Az alösszetevők gép által megtanult entitások gyermekentitásai.

A gép megtanult entitás a betanítási kimondott szövegkörnyezet alapján aktiválódik. **A korlátozások** olyan nem kötelező szabályok, amelyek olyan gépmegtanult entitásokra vonatkoznak, amelyek tovább korlátozzák a nem gépről megtanult entitás, például a [Lista](reference-entity-list.md) vagy a [Regex](reference-entity-regular-expression.md)pontos szövegének megfelelő definíciója alapján történő aktiválást . Például egy `size` gép megtanult entitás rendelkezhet `sizeList` egy listaentitás megkötésével, amely az entitást csak akkor korlátozza, `size` ha az `sizeList` entitásban lévő értékek et tapasztal.

[**A leírók**](luis-concept-feature.md) olyan jellemzők, amelyek növelik a szavak vagy kifejezések relevanciáját az előrejelzéshez. Ezek *nevezzük leírók,* mert egy szándék vagy entitás *leírására* használják őket. A leírók leírják az adatok megkülönböztető jellemzőit vagy attribútumait, például a luis által megfigyelt és megtanult fontos szavakat vagy kifejezéseket.

Amikor létrehoz egy kifejezéslista-funkciót a LUIS-alkalmazásban, alapértelmezés szerint globálisan engedélyezve van, és egyenletesen vonatkozik az összes leképezésre és entitásra. Ha azonban a kifejezéslistát egy gép által megtanult entitás (vagy *modell)* leírójaként (jellemzőjeként) alkalmazza, akkor hatóköre csak az adott modellre vonatkozik, és már nem használatos az összes többi modellel. Egy kifejezéslista használata a modell leírójaként segít a bontásban azáltal, hogy segíti a modell pontosságát, amelyre alkalmazza.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Az entitások típusai

Válassza ki az entitást az adatok kinyerésének és a kibontás után való ábrázolásának módjától függően.

|Entitástípus|Cél|
|--|--|
|[**Gépmegtanult**](tutorial-machine-learned-entity.md)|Gépmegtanult entitások tanulnak a szövegkörnyezetben az utterance (kifejezés) környezetből. Entitások fölérendelt csoportosítása, entitástípustól függetlenül. Ez jelentőssé teszi az elhelyezés változását a példa kimondott szövegekben. |
|[**Lista**](reference-entity-list.md)|A pontos szöveggel kinyert elemek és szinonimáik listája megegyezik a **szöveggel.**|
|[**Minta.minden**](reference-entity-pattern-any.md)|Olyan entitás, ahol nehéz meghatározni a gazdálkodó egység végét. |
|[**Előre elkészített**](luis-reference-prebuilt-entities.md)|Már be van tanítva adott típusú adatok, például URL vagy e-mail kinyerésére. Ezen előre összeállított entitások némelyike a nyílt forráskódú [recognizers-text](https://github.com/Microsoft/Recognizers-Text) projektben van definiálva. Ha az adott kultúra vagy entitás jelenleg nem támogatott, járuljon hozzá a projekthez.|
|[**Reguláris kifejezés**](reference-entity-regular-expression.md)|Reguláris kifejezést használ a **pontos szövegegyezéshez.**|

## <a name="extracting-contextually-related-data"></a>Környezetfüggő adatok kinyerése

Egy utterance (kifejezés) tartalmazhat egy entitás két vagy több előfordulása, ahol az adatok jelentése az utterance (kifejezés) környezetén alapul. Egy példa egy utterance (kifejezés) egy járat foglalása, amely két hely, kiindulási és úti cél.

`Book a flight from Seattle to Cairo`

Az `location` entitás két példáját ki kell bontani. Az ügyfélalkalmazásnak ismernie kell az egyes helytípusok típusát a jegyvásárlás befejezéséhez.

A környezetfüggő adatok kinyerésének két technikája van:

 * Az `location` entitás egy gép által megtanult entitás, és `origin` két `destination` alösszetevő-entitást használ a (preferált) rögzítéséhez.
 * Az `location` entitás két `origin` **szerepkört** használ, és`destination`

Több entitás létezhet egy utterance (kifejezés) és kibontható bomlás vagy szerepkörök használata nélkül, ha a környezet, amelyben használják, nincs jelentősége. Például ha az utterance (kifejezés) `I want to travel to Seattle, Cairo, and London.`tartalmazza a helyek listáját, ez egy olyan lista, ahol minden elem nem rendelkezik további jelentéssel.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Környezet definiálása a gép megtanult entitás alösszetevő-entitásainak használatával

Egy gép [**megtanult entitás**](tutorial-machine-learned-entity.md) segítségével kinyerheti az adatokat, amely leírja a művelet a járat foglalása, majd lebontani a legfelső szintű entitás a külön részek által igényelt az ügyfélalkalmazás.

Ebben a `Book a flight from Seattle to Cairo`példában a legfelső szintű `travelAction` entitás lehet, `flight from Seattle to Cairo`és címkézett kivonat. Ezután létrejön két alösszetevő-entitás, a neve, `origin` és `destination`mindkettő az `geographyV2` előre összeállított entitás megkötésével. A betanítási utterances, a `origin` és `destination` megfelelően vannak címkézve.

### <a name="using-entity-role-to-define-context"></a>Entitásszerepkör használata környezet definiálására

A szerepkör egy elnevezett alias egy entitás az utterance (kifejezés) környezete alapján. A szerepkör bármely előre összeállított vagy egyéni entitástípussal használható, és példakimondott szövegben és mintázatban is használható. Ebben a példában az `location` entitás `origin` `destination` nak két szerepkört kell, és mindkettőt meg kell jelölni a példa utterances.

Ha a `location` LUIS megtalálja a szerepkört, de nem tudja meghatározni a szerepkört, a helyentitás továbbra is visszakerül. Az ügyfélalkalmazásnak nyomon kell követnie egy kérdést, hogy meghatározza, milyen típusú helyet jelent a felhasználó.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha az entitások maximális számánál többre van szüksége

Ha a korlátnál többre van szüksége, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön részletes információkat a rendszerről, nyissa meg a [LUIS](luis-reference-regions.md#luis-website) webhelyet, és válassza a **Támogatás**lehetőséget. Ha az Azure-előfizetés támogatási szolgáltatásokat is tartalmaz, forduljon [az Azure technikai támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="entity-prediction-status"></a>Entitás előrejelzési állapota

A LUIS-portál azt mutatja, ha az entitás egy példa utterance (kifejezés) entitás előrejelzés, mint a kiválasztott entitás. Ez a különböző pontszám az aktuálisbetanított modellen alapul.

## <a name="next-steps"></a>További lépések

Ismerje meg a jó [kimondott szövegfogalmakat.](luis-concept-utterance.md)

További információ az [entitások hozzáadása](luis-how-to-add-entities.md) a LUIS-alkalmazáshoz való hozzáadásáról.

Lásd: [A strukturált adatok kinyerése a felhasználói utterance (konklúsz) a gép által megtanult entitások nyelvi megértés (LUIS) című témakörben,](tutorial-machine-learned-entity.md) hogy megtudja, hogyan nyerheti ki a strukturált adatokat egy utterance (kifejezés) a gép által megtanult entitás használatával.
 
