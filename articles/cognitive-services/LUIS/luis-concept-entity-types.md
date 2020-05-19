---
title: Entity types – LUIS
description: Egy entitás kinyeri az adatait a felhasználótól az előrejelzési futtatókörnyezetben. Egy _opcionális_, másodlagos cél a szándék vagy más entitások előrejelzésének növelése az entitás szolgáltatásként való használatával.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585640"
---
# <a name="extract-data-with-entities"></a>Adatok kinyerése entitásokkal

Egy entitás kinyeri az adatait a felhasználótól az előrejelzési futtatókörnyezetben. Egy _opcionális_, másodlagos cél a szándék vagy más entitások előrejelzésének növelése az entitás szolgáltatásként való használatával.

Többféle típusú entitás létezik:

* [Gépi tanulással létrejött entitás](reference-entity-machine-learned-entity.md)
* Nem gépi használatú – a szükséges [szolgáltatásként használt funkció](luis-concept-feature.md) – a szöveges egyezések, a minták egyezései vagy az előre elkészített entitások észlelése
* [Minta. any](#patternany-entity) – a szabad formátumú szöveg kibontása, például a könyv címeinek [mintából](reference-entity-pattern-any.md) való kinyerése

A géppel megtanult entitások az kinyerési lehetőségek széles skáláját biztosítják. A nem gépi megtanult entitások szöveg-egyeztetés szerint működnek, és a gép által megtanult entitás vagy szándék [szükséges funkciójaként](#design-entities-for-decomposition) használatosak.

## <a name="entities-represent-data"></a>Az entitások az adathalmazokat jelölik

Az entitások olyan adatok, amelyeket le szeretne kérni a kiértékelésből, például a neveket, a dátumokat, a terméknévokat vagy a szavak jelentős csoportjait. A Kimondás számos entitást tartalmazhat, vagy egyetlen sem. Az ügyfélalkalmazás feladatának elvégzéséhez szükség _lehet_ az adatokra.

Az entitásokat következetesen kell megcímkézni a modell minden egyes szándékának összes képzési hosszúságú kimondott szöveg.

 Megadhatja saját entitásait, vagy az előre elkészített entitások használatával időt takaríthat meg a gyakori fogalmak, például a [datetimeV2](luis-reference-prebuilt-datetimev2.md), a [sorszám](luis-reference-prebuilt-ordinal.md), az [e-mail](luis-reference-prebuilt-email.md)és a [telefonszámok](luis-reference-prebuilt-phonenumber.md)számára.

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|3 jegy vásárlása a New Yorkba|Előre elkészített szám<br>Cél|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Az entitások nem kötelezőek, de ajánlottak

A [szándékok](luis-concept-intent.md) megadásakor az entitások megadása nem kötelező. Nem kell entitásokat létrehoznia az alkalmazás minden fogalmához, de csak azokra az esetekre, amikor az ügyfélalkalmazás igényli az adatkezelést, vagy ha az entitás egy másik entitás vagy szándék felé irányuló célzásként vagy jelzésként működik.

Az alkalmazás fejlődése és az új adatkezelési igények azonosítása érdekében később is hozzáadhat megfelelő entitásokat a LUIS-modellhez.

## <a name="entity-compared-to-intent"></a>Entitás a szándékhoz képest

Az entitás egy adatkoncepciót képvisel _a teljes_kifejezésen belül. A cél a _teljes Kimondás_.

Vegye figyelembe a következő négy hosszúságú kimondott szöveg:

|Kimondott szöveg|Előre jelzett szándék|Kinyert entitások|Magyarázat|
|--|--|--|--|
|Súgó|segítség|-|Nincs kibontva.|
|Küldés valami|sendSomething|-|Nincs kibontva. A modell nem rendelkezik a kinyeréséhez szükséges funkcióval `something` , és nincs megadva címzett.|
|Bob a present küldése|sendSomething|`Bob`, `present`|A modell kibontása az `Bob` előre elkészített entitás kötelező funkciójának hozzáadásával `personName` . A rendszer egy géppel megtanult entitást használ a kinyeréséhez `present` .|
|Bob a csokoládét tartalmazó doboz elküldése|sendSomething|`Bob`, `box of chocolates`|A két fontos adatot `Bob` és a-t a `box of chocolates` géppel megtanult entitások kinyerték.|

## <a name="design-entities-for-decomposition"></a>Entitások megtervezése a dekompozícióhoz

A géppel megtanult entitások lehetővé teszik az alkalmazás sémájának megtervezését a dekompozícióhoz, a nagyméretű koncepciók alentitásokra való feltörését.

A kiépítésének megtervezése lehetővé teszi a LUIS számára, hogy a nagymértékű entitás-feloldást adja vissza az ügyfélalkalmazás számára. Ez lehetővé teszi, hogy az ügyfélalkalmazás az üzleti szabályokra koncentráljon, és az adatfelbontást a LUIS-re bízza.

A gép által megtanult entitás-eseményindítók a példa hosszúságú kimondott szöveg keresztül megszerzett kontextus alapján.

A [**géppel megtanult entitások**](tutorial-machine-learned-entity.md) a legfelső szintű kivonók. Az alentitások a géppel megtanult entitások alárendelt entitásai.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Az entitások típusai

A szülő alentitásának egy géppel megtanult entitásnak kell lennie. Az alentitások nem gépi megtanult entitást használhatnak [szolgáltatásként](luis-concept-feature.md).

Válassza ki az entitást az Adatkivonatok és a kinyerés utáni megjelenítésük alapján.

|Entitástípus|Cél|
|--|--|
|[**Gépi megtanult**](tutorial-machine-learned-entity.md)|Beágyazott, összetett adatok kinyerése a címkével ellátott példákból. |
|[**Listáját**](reference-entity-list.md)|A **pontos szöveges egyezéssel**kinyert elemek és szinonimáik listája.|
|[**Minta. any**](#patternany-entity)|Nehéz megállapítani, hogy az entitás véget ért-e, mert az entitás szabad formátumú. Csak [mintákban](luis-concept-patterns.md)érhető el.|
|[**Prebuilt**](luis-reference-prebuilt-entities.md)|Már betanítva bizonyos típusú adatok, például URL-cím vagy e-mailek kinyerésére. Ezen előre összeépített entitások némelyike a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projektben van meghatározva. Ha az adott kulturális környezet vagy entitás jelenleg nem támogatott, akkor hozzájárul a projekthez.|
|[**Reguláris kifejezés**](reference-entity-regular-expression.md)|Reguláris kifejezést használ a **pontos szöveges egyeztetéshez**.|

## <a name="extracting-contextually-related-data"></a>Kontextussal kapcsolatos adatok kinyerése

A Kimondás egy olyan entitás két vagy több előfordulását is tartalmazhatja, amelyben az információ jelentése a teljes kontextuson belüli kontextuson alapul. Ilyen például a két földrajzi hellyel, a forrással és a rendeltetéssel rendelkező repülés foglalásának kimondása.

`Book a flight from Seattle to Cairo`

A két helyet úgy kell kinyerni, hogy az ügyfél-alkalmazás tudja az egyes helyek típusát, hogy elvégezze a jegy megvásárlását.

A forrás és a cél kinyeréséhez hozzon létre két alentitást a Ticket Order Machine által megismert entitás részeként. Mindegyik alentitáshoz hozzon létre egy szükséges szolgáltatást, amely geographyV2 használ.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Az entitások korlátozásához szükséges szolgáltatások használata

További információ a [szükséges funkciókról](luis-concept-feature.md)

## <a name="patternany-entity"></a>Pattern.any entitás

A minta. any csak egy [mintában](luis-concept-patterns.md)érhető el.

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Az entitásokra vonatkozó alkalmazások korlátainak túllépése

Ha a [korlátnál](luis-limits.md#model-limits)nagyobbra van szüksége, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön részletes információkat a rendszeréről, lépjen a [Luis](luis-reference-regions.md#luis-website) webhelyére, és válassza a **támogatás**lehetőséget. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához.

## <a name="entity-prediction-status"></a>Entitás-előrejelzés állapota

A LUIS-portál azt mutatja be, hogy az entitás eltérő egyed-előrejelzéssel rendelkezik, mint a kiválasztáshoz kiválasztott entitás. Ez a különböző pontszám az aktuálisan betanított modellen alapul. Ezekkel az információkkal a következő műveleteket hajthatja végre a betanítási hibák elhárításához:
* Hozzon létre egy [szolgáltatást](luis-concept-feature.md) az entitás számára az entitás koncepciójának azonosítása érdekében
* További [példa hosszúságú kimondott szöveg](luis-concept-utterance.md) és címke hozzáadása az entitáshoz
* [Tekintse át az aktív tanulási javaslatokat](luis-concept-review-endpoint-utterances.md) az előrejelzési végponton fogadott bármely hosszúságú kimondott szöveg, amely segíthet az entitás koncepciójának azonosításában.

## <a name="next-steps"></a>További lépések

Ismerje meg a jó [hosszúságú kimondott szöveg](luis-concept-utterance.md)kapcsolatos fogalmakat.

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.

Lásd [: oktatóanyag: strukturált adatok kinyerése a felhasználóktól a Language Understanding (Luis) géppel megismert entitások](tutorial-machine-learned-entity.md) alapján, amelyből megtudhatja, hogyan kinyerheti a strukturált adatokból való kinyerését a géppel megtanult entitás használatával.

