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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 808e110ccb45b0b4f7bf34a43597c1f7a7bc0fed
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422576"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entitások és céljuk a LUIS-ben

Az entitások elsődleges célja, hogy az ügyfélalkalmazások kiszámítható módon kinyerje az adatmennyiséget. Egy _opcionális_, másodlagos cél az, hogy növelje a szándék vagy más entitások előrejelzését a leírókkal.

Két típusú entitás létezik:

* gépi megtanult – környezetből
* nem gépi megtanult – a szöveges egyezések, a minták egyezései, illetve az előre elkészített entitások észlelése

A géppel megtanult entitások az kinyerési lehetőségek széles skáláját biztosítják. A nem gépi megtanult entitások szöveges egyeztetéssel működnek, és a gép által megtanult entitások egymástól függetlenül vagy [megkötésként](#design-entities-for-decomposition) is használhatók.

## <a name="entities-represent-data"></a>Entitások adatokat képviselik.

Az entitások olyan adatok, amelyeket le szeretne kérni a kiértékelésből, például a neveket, a dátumokat, a terméknévokat vagy a szavak jelentős csoportjait. Az utterance (kifejezés) is tartalmazhat számos entitás vagy nincs minden. Az ügyfélalkalmazás feladatának elvégzéséhez szükség _lehet_ az adatokra.

Az entitásokat következetesen kell megcímkézni a modell minden egyes szándékának összes képzési hosszúságú kimondott szöveg.

 Megadhatja saját entitásait, vagy az előre elkészített entitások használatával időt takaríthat meg a gyakori fogalmak, például a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [sorszám](luis-reference-prebuilt-ordinal.md), az [e-mail](luis-reference-prebuilt-email.md)és a [telefonszámok](luis-reference-prebuilt-phonenumber.md)számára.

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|A New York-i 3 jegyek megvásárlása|Előre összeállított száma<br>Location.Destination|3<br>New York|
|London, New York-i jegyet vásárolni március 5|Location.Origin<br>Location.Destination<br>Előre összeállított datetimeV2|New York<br>London<br>2018. március 5.|

### <a name="entities-are-optional"></a>Az entitások nem kötelezőek

Leképezések szükség, míg az entitások nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, de csak azok számára, akiknek szükségük van az ügyfélalkalmazás beavatkozására.

Ha a hosszúságú kimondott szöveg nem rendelkezik az ügyfélalkalmazás által igényelt adattal, nem kell entitásokat felvennie. Az alkalmazás fejlődése és az új adatkezelési igények azonosítása érdekében később is hozzáadhat megfelelő entitásokat a LUIS-modellhez.

## <a name="entity-compared-to-intent"></a>Beszédszándék képest entitás

Az entitás a kibontani kívánt kifejezésen belüli adatkoncepciót jelöli.

A Kimondás opcionálisan tartalmazhat entitásokat is. Összehasonlítva a Kimondás céljának előrejelzése _szükséges_ , és a teljes teljességet jelenti. A LUIS használatához példa hosszúságú kimondott szöveg van szükség.

Vegye figyelembe a következő 4 hosszúságú kimondott szöveg:

|Kimondott szöveg|Előre jelzett leképezés|Kinyert entitások|Magyarázat|
|--|--|--|--|
|Súgó|help|-|Nincs kibontva.|
|Küldés valami|sendSomething|-|Nincs kibontva. A modell nem lett kitanítva `something` kinyerésére ebben a kontextusban, és nincs címzett sem.|
|Bob a present küldése|sendSomething|`Bob`, `present`|A modell a [personName](luis-reference-prebuilt-person.md) előre összeállított entitással lett betanítva, amely a `Bob`nevet kibontotta. `present`kinyeréséhez egy géppel megtanult entitást használtak.|
|Bob a csokoládét tartalmazó doboz elküldése|sendSomething|`Bob`, `box of chocolates`|A két fontos adat, a `Bob` és a `box of chocolates`az entitásokból lett kibontva.|

## <a name="design-entities-for-decomposition"></a>Entitások megtervezése a dekompozícióhoz

A legfelső szintű entitások egy gépi megtanult entitást alkotnak. Ez lehetővé teszi az entitások megtervezését az idő múlásával, valamint az **alösszetevők** (alárendelt entitások) használatát, igény szerint **korlátozásokkal** és **leírókkal**, hogy a legfelső szintű entitást az ügyfélalkalmazás által igényelt részekre lehessen bontani.

A kiépítésének megtervezése lehetővé teszi a LUIS számára, hogy a nagymértékű entitás-feloldást adja vissza az ügyfélalkalmazás számára. Ez lehetővé teszi, hogy az ügyfélalkalmazás az üzleti szabályokra koncentráljon, és az adatfelbontást a LUIS-re bízza.

### <a name="machine-learned-entities-are-primary-data-collections"></a>A géppel megtanult entitások elsődleges adatgyűjtemények

A [**géppel megtanult entitások**](tutorial-machine-learned-entity.md) a legfelső szintű adategységek. Az alösszetevők a géppel megtanult entitások alárendelt entitásai.

Egy géppel megtanult entitás-eseményindítók a hosszúságú kimondott szöveg képzésen alapuló kontextus alapján. A **megkötések** nem kötelezően alkalmazandó szabályok, amelyek egy géppel megtanult entitásra vonatkoznak, amely tovább korlátozza az aktiválást egy nem gépi megtanult entitás, például egy [lista](reference-entity-list.md) vagy [regex](reference-entity-regular-expression.md)pontos szöveges egyeztetése alapján. Egy `size` gép által megtanult entitás például rendelkezhet egy `sizeList` lista entitás korlátozásával, amely korlátozza a `size` entitást, hogy csak akkor induljon el, ha az `sizeList` entitásban található értékek észlelhetők.

A [**leírók**](luis-concept-feature.md) olyan szolgáltatások, amelyek az előrejelzéshez tartozó szavak vagy kifejezések relevanciájának növelésére vonatkoznak. Ezeket *descripters* nevezzük, mert egy szándék vagy egy entitás *leírására* szolgálnak. A leírók leírják az adattípusok vagy az adatattribútumok megkülönböztető jellemzőit, például a fontos szavakat vagy kifejezéseket. a LUIS betartja és megtanulja a-t.

Amikor létrehoz egy kifejezés-lista szolgáltatást a LUIS-alkalmazásban, az alapértelmezés szerint globálisan engedélyezve van, és egyenletesen alkalmazza az összes szándékot és entitást. Ha azonban egy géppel megtanult entitás (vagy *modell*) leíróként (funkcióként) alkalmazza a kifejezést, a hatóköre csak erre a modellre vonatkozik, és az összes többi modellel már nem használható. Ha egy kifejezési listát leíróként használ a modellhez, segít a kibontásban annak a modellnek a pontosságával, amelyre alkalmazva van.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Entitástípus

Válassza ki az entitást az Adatkivonatok és a kinyerés utáni megjelenítésük alapján.

|Entitás típusa|Cél|
|--|--|
|[**Gépi megtanult**](tutorial-machine-learned-entity.md)|A géppel megtanult entitások a teljes kontextusban tanulnak. Entitások szülőjének csoportosítása, az entitás típusától függetlenül. Így az elhelyezés variációja jelentős hosszúságú kimondott szöveg jelent. |
|[**Listáját**](reference-entity-list.md)|A **pontos szöveges egyezéssel**kinyert elemek és szinonimáik listája.|
|[**Minta. any**](reference-entity-pattern-any.md)|Az entitás, amelyben nehéz meghatározni az entitás végét. |
|[**Prebuilt**](luis-reference-prebuilt-entities.md)|Már betanítva bizonyos típusú adatok, például URL-cím vagy e-mailek kinyerésére. Ezen előre összeépített entitások némelyike a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projektben van meghatározva. A megadott kulturális környezet vagy a szervezet jelenleg nem támogatott, ha járulnak hozzá a projekthez.|
|[**Reguláris kifejezés**](reference-entity-regular-expression.md)|Reguláris kifejezést használ a **pontos szöveges egyeztetéshez**.|

## <a name="extracting-contextually-related-data"></a>Kontextussal kapcsolatos adatok kinyerése

A Kimondás egy olyan entitás két vagy több előfordulását is tartalmazhatja, amelyben az információ jelentése a teljes kontextuson belüli kontextuson alapul. Egy példa egy olyan járat foglalásának kimondása, amelynek két helye van, a forrás és a cél.

`Book a flight from Seattle to Cairo`

`location` entitás két példáját ki kell vonni. Az ügyfél-alkalmazásnak ismernie kell a hely típusát, hogy el lehessen végezni a jegyek megvásárlását.

A kontextussal kapcsolatos adatok kinyerésére két módszer áll rendelkezésre:

 * A `location` entitás egy géppel megtanult entitás, és két alösszetevővel rendelkező entitást használ a `origin` és a `destination` rögzítéséhez (előnyben részesített)
 * A `location` entitás a `origin` és a `destination` két **szerepkörét** használja.

A rendszer több entitást is használhat, és a dekompozíció vagy szerepkörök használata nélkül kinyerhető, ha a használt környezet nem bír jelentőséggel. Ha például a Kimondás tartalmazza a helyszínek listáját, `I want to travel to Seattle, Cairo, and London.`, ez egy lista, ahol az egyes elemek nem rendelkeznek további jelentéssel.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Egy géppel megtanult entitás alösszetevő-entitások használata a környezet meghatározásához

Egy [**géppel megtanult entitás**](tutorial-machine-learned-entity.md) segítségével kinyerheti a repülés foglalásának műveleteit leíró információkat, majd a legfelső szintű entitást kibonthatja az ügyfélalkalmazás által igényelt különálló részekre.

Ebben a példában `Book a flight from Seattle to Cairo`a legfelső szintű entitást `travelAction` és címkézték `flight from Seattle to Cairo`kinyeréséhez. Ezután két alösszetevő-entitás jön létre, `origin` és `destination`néven, valamint az előre elkészített `geographyV2` entitásra alkalmazott korlátozással. A betanítási hosszúságú kimondott szöveg a `origin` és `destination` megfelelően van megjelölve.

### <a name="using-entity-role-to-define-context"></a>Az entitások szerepkörének használata a környezet definiálásához

A szerepkör egy entitás névvel ellátott aliasa, amely a teljes értéken belüli kontextuson alapul. A szerepkörök bármely előre elkészített vagy egyéni entitás típussal használhatók, és a példaként használt hosszúságú kimondott szöveg és mintákban is használhatók. Ebben a példában a `location` entitásnak `origin` és `destination` két szerepkörre van szüksége, és mindkettőt a példában szereplő hosszúságú kimondott szöveg kell megjelölni.

Ha a LUIS megkeresi a `location`, de nem tudja meghatározni a szerepkört, a hely entitás továbbra is visszaadja. Az ügyfélalkalmazás nyomon kell követnie egy kérdést, hogy meghatározza, hogy a felhasználó milyen típusú helyet igényel.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha több, mint az entitások maximális száma

Ha a korlátnál nagyobbra van szüksége, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön részletes információkat a rendszeréről, lépjen a [Luis](luis-reference-regions.md#luis-website) webhelyére, és válassza a **támogatás**lehetőséget. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához.

## <a name="entity-prediction-status"></a>Entitás-előrejelzés állapota

A LUIS-portál azt mutatja be, hogy az entitás egy példa szerinti Kimondás esetén a kiválasztott entitástól eltérő egyed-előrejelzéssel rendelkezik. Ez a különböző pontszám az aktuálisan betanított modellen alapul.

## <a name="next-steps"></a>További lépések

Ismerje meg a jó [hosszúságú kimondott szöveg](luis-concept-utterance.md)kapcsolatos fogalmakat.

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.

Lásd [: oktatóanyag: strukturált adatok kinyerése a felhasználóktól a Language Understanding (Luis) géppel megismert entitások](tutorial-machine-learned-entity.md) alapján, amelyből megtudhatja, hogyan kinyerheti a strukturált adatokból való kinyerését a géppel megtanult entitás használatával.
