---
title: Szándékok és entitások - LUIS
titleSuffix: Azure Cognitive Services
description: Egyetlen szándék egy olyan feladatot vagy műveletet jelöl, amelyet a felhasználó végre kíván hajtani. A felhasználó beszédeleme mögött rejlő célt vagy kívánságot jelölik. Határozza meg a szándékok, amelyek megfelelnek a felhasználók által az alkalmazásban elkívánt műveleteket.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220973"
---
# <a name="intents-in-your-luis-app"></a>Szándékok a LUIS alkalmazásban

A szándék egy olyan feladatot vagy műveletet jelöl, amelyet a felhasználó végre kíván hajtani. Ez a cél vagy cél kifejezve a felhasználó [utterance (kifejezés).](luis-concept-utterance.md)

Határozza meg a szándékok, amelyek megfelelnek a felhasználók által az alkalmazásban elkívánt műveleteket. Egy utazási alkalmazás például több leképezést határoz meg:

Utazási alkalmazás leképezései   |   Példák kimondott szövegekre   | 
------|------|
 RepülőjegyFoglalás     |   "Foglaljon nekem egy repülőjegyet Rióba a jövő héten" <br/> "Repülj Rióra 24-én" <br/> "Szükségem van egy repülőjegyre jövő vasárnap Rio de Janeiróba."    |
 Üdvözlés     |   "Sziasztok" <br/>"Helló" <br/>"Jó reggelt"  |
 CheckWeather (Időjárás) | "Milyen az idő Bostonban?" <br/> "Mutasd meg a hétvégére vonatkozó előrejelzést" |
 None         | "Szerezz egy süti receptet"<br>"A Lakers nyert?" |

Minden alkalmazás előre definiált szándékkal érkezik, "[Nincs](#none-intent)", amely a tartalék szándék. 

## <a name="prebuilt-domains-provide-intents"></a>Az előre összeállított tartományok leképezéseket biztosítanak
A megadott szándékokon kívül előre összeállított leképezéseket is használhat az [előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md)egyikéből. 

## <a name="return-all-intents-scores"></a>Az összes szándék pontszámának visszaadása
Egy utterance (kifejezés) egyetlen szándékhoz rendeli hozzá. Amikor a LUIS kap egy utterance (kifejezés) a végponton, alapértelmezés szerint, az adott utterance (kifejezés) felső szándékát adja vissza. 

Ha azt szeretné, hogy a pontszámok az utterance (kifejezés) összes leképezések, megadhat egy jelzőt a lekérdezési karakterlánc az előrejelzési API-t. 

|Előrejelzési API-verzió|Jelző|
|--|--|
|2. verzió|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Szándék az entitáshoz képest
A szándék az alkalmazás által a felhasználó számára végrehajtott műveletet jelöli, és a teljes utterance (kifejezés) alapján. Egy utterance (kifejezés) csak egy felső pontozási szándékkal rendelkezhet, de sok entitást rendelkezhet. 

<a name="how-do-intents-relate-to-entities"></a>

Hozzon létre egy szándékot, ha a felhasználó _szándéka_ egy műveletet indít el az ügyfélalkalmazásban, például a checkweather() függvény hívását. Ezután hozzon létre entitásokat a művelet végrehajtásához szükséges paraméterek ábrázolására. 

|Szándék   | Entitás | Példa kimondott szöveg   | 
|------------------|------------------------------|------------------------------|
| CheckWeather (Időjárás) | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.dateTimeV2.date","entity": "holnap","felbontás":"2018-05-23" } | Milyen az `Seattle` `tomorrow`idő? |
| CheckWeather (Időjárás) | { "típus": "date_range", "entitás": "ezen a hétvégén" } | Az előrejelzés megjelenítése`this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Előre összeállított tartományi leképezések

[Előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md) leképezések utterances. 

## <a name="none-intent"></a>A None szándék

A **Nincs** szándék jön létre, de szándékosan üresen marad. A **Nincs** szándék kötelező szándék, és nem törölhető vagy nevezhető át. Töltse ki a tartományon kívüli kimondott szövegekkel.

A **Nincs** szándék a tartalék szándék, fontos minden alkalmazásban, és rendelkeznie kell a teljes utterances 10%-a. Luis utterances, amelyek nem fontosak az alkalmazás tartományban (tárgyterület) nem fontos a LUIS utterances tanítására. Ha nem ad hozzá utterances a **Nincs** szándék, luis kényszeríti a tartományon kívüli utterance sance s in the domain intents. Ez megdönti az előrejelzési pontszámok a LUIS a kimondott szöveg helytelen szándékkal. 

Amikor egy utterance (kifejezés) előre jelzett, mint a Nincs szándék, az ügyfélalkalmazás több kérdést tehet fel, vagy egy menüt, amely irányítja a felhasználót érvényes választási lehetőségeket. 

## <a name="negative-intentions"></a>Negatív szándékok 
Ha meg szeretné határozni a negatív és pozitív szándékokat, például a **"Szeretnék** egy autót" és a **"Nem** akarok autót", létrehozhat két szándékot (egy pozitív és egy negatív), és mindegyikhez hozzáadhatja a megfelelő kijelentéseket. Vagy létrehozhat egyetlen szándékot, és megjelölheti a két különböző pozitív és negatív kifejezést entitásként.  

## <a name="intents-and-patterns"></a>Szándékok és minták

Ha példa kimondott szöveggel rendelkezik, amely részben vagy egészben definiálható reguláris kifejezésként, fontolja meg a [mintával](luis-concept-patterns.md)párosított [reguláris kifejezés entitás](luis-concept-entity-types.md#regular-expression-entity) használatát. 

Reguláris kifejezés entitás használata garantálja az adatok kinyerését, hogy a minta egyeztetve. A minta egyeztetési garantálja a pontos szándék ot adja vissza. 

## <a name="intent-balance"></a>Szándék egyenlege
Az alkalmazás tartományi leképezések kell rendelkeznie az egyes leképezések utterances egyensúlyával. Nem rendelkezik egy szándékkal 10 utterances és egy másik szándék 500 utterances. Ez nem kiegyensúlyozott. Ha van ez a helyzet, tekintse át a szándékot 500 utterances, hogy ha sok a szándékok átszervezhető egy [minta.](luis-concept-patterns.md) 

A **Nincs** szándék nem szerepel az egyenlegben. Ez a szándék tartalmaznia kell az alkalmazás teljes utterances 10%-át.

## <a name="intent-limits"></a>Szándékkorlátok
Tekintse át [a korlátokat,](luis-boundaries.md#model-boundaries) hogy megtudja, hány leképezést adhat hozzá egy modellhez. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha a leképezések maximális számánál többre van szüksége 
Először fontolja meg, hogy a rendszer túl sok szándékot használ-e. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több leképezés egyesíthető egyetlen leképezésben entitásokkal 
A túlságosan hasonló szándékok megnehezítik a LUIS számára a különbséget közöttük. A szándékok nak elég változatosnak kell lenniük ahhoz, hogy rögzítsék a felhasználó által kért fő feladatokat, de nem kell rögzíteniük a kód minden útvonalát. Például a BookFlight és a FlightCustomerService lehet külön szándékegy utazási alkalmazásban, de a BookInternationalFlight és a BookDomesticFlight túl hasonló. Ha a rendszernek meg kell különböztetnie őket, használjon entitásokat vagy más logikát a szándékok helyett. 

### <a name="dispatcher-model"></a>Diszpécser modell
További információ a LUIS- és A QnA-készítő alkalmazások és a [feladási modell kombinálásáról.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Kérjen segítséget a jelentős számú leképezéssel rendelkező alkalmazásokhoz
Ha a leképezések számának csökkentése vagy a szándékok több alkalmazásra való felosztása nem működik, forduljon az ügyfélszolgálathoz. Ha az Azure-előfizetés támogatási szolgáltatásokat is tartalmaz, forduljon [az Azure technikai támogatási szolgálatához.](https://azure.microsoft.com/support/options/) 

## <a name="next-steps"></a>További lépések

* További információ [az okról az entitásokról,](luis-concept-entity-types.md)amelyek fontos szavak a szándékokhoz kapcsolódóan
* Ismerje meg, hogyan [adhat hozzá és kezelhet leképezéseket](luis-how-to-add-intents.md) a LUIS-alkalmazásban.
* A szándékkal [kapcsolatos gyakorlati tanácsok áttekintése](luis-concept-best-practices.md)
