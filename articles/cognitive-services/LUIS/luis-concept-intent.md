---
title: Szándékok és entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Egyetlen szándék a felhasználó által végrehajtani kívánt feladatot vagy műveletet jelöli. Egy célra vagy a cél-ben a felhasználó utterance (kifejezés) fejezzük ki. Meghatározhatja egy adott szándékot megfelelő műveleteket hajthat végre felhasználókat szeretné állítani az alkalmazásban.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280887"
---
# <a name="intents-in-your-luis-app"></a>Szándékok a LUIS-alkalmazásban

Megjelölésű jelöli egy feladatot vagy műveletet a felhasználó szeretné végrehajtani. Ez a felhasználó [teljes](luis-concept-utterance.md)kifejezésében kifejezett cél vagy cél.

Meghatározhatja egy adott szándékot megfelelő műveleteket hajthat végre felhasználókat szeretné állítani az alkalmazásban. Például egy utazási alkalmazás számos leképezések határozza meg:

Utazás alkalmazásszándékkal   |   Példák kimondott szövegekre   | 
------|------|
 RepülőjegyFoglalás     |   Például "book nekem, Rio repülőjegyet következő hét" <br/> "Repülési nekem, Rio a 24th a" <br/> "Van szükségem egy adatsík jegyet Río de Janeiro tovább vasárnap"    |
 Üdvözlés     |   "Hi" <br/>"Hello" <br/>"Jó reggeli"  |
 CheckWeather | "Mi az az időjárás, például Bostonban?" <br/> "Show me a hétvégi vonatkozó előrejelzést" |
 Nincsenek         | "Get me egy cookie-k recept"<br>"Volt a Lakers win?" |

Minden alkalmazás a "[nincs](#none-intent)" előre definiált szándékkal, azaz a tartalék szándékkal van ellátva. 

## <a name="prebuilt-domains-provide-intents"></a>Előre összeállított tartományok leképezések használata
A definiált szándékon kívül az előre [elkészített tartományok](luis-how-to-use-prebuilt-domains.md)egyikének előre összekészített leképezéseit is használhatja. 

## <a name="return-all-intents-scores"></a>Minden leképezések pontszámokat ad vissza
Egyetlen célja az utterance (kifejezés) rendelheti hozzá. Ha a LUIS a végpontra vonatkozó kiírást kap, alapértelmezés szerint a rendszer visszaadja az adott kifejezéshez legfelső szándékot. 

Ha azt szeretné, hogy a pontszámok a teljes értékre legyenek kiválasztva, megadhat egy jelzőt az előrejelzési API lekérdezési karakterláncában. 

|Előrejelzési API verziója|Jelző|
|--|--|
|2\. verzió|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>A szándék entitás képest
A szándék azt a műveletet jelöli, amelyet az alkalmazásnak el kell végeznie a felhasználó számára, és a teljes Kimondás alapján kell megjelennie. Az utterance (kifejezés) pontozási szándéka csak egy top rendelkezhet, de számos entitás veheti fel. 

<a name="how-do-intents-relate-to-entities"></a>

Hozzon létre egy szándékot, ha a felhasználó _szándéka_ egy műveletet indít el az ügyfélalkalmazás, például a checkweather () függvény hívása. Ezután hozzon létre entitásokat a művelet végrehajtásához szükséges paraméterek ábrázolásához. 

|Szándék   | Entitás | Példa kimondott szöveg   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "hely", "entitás": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "holnap", "feloldás": "2018-05-23"} | Milyen időjárási viszonyok vannak a `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entitás": "a hétvégi"} | A `this weekend` előrejelzésének megjelenítése | 
||||

## <a name="prebuilt-domain-intents"></a>Előre összeállított tartományban szándékok

Az [előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md) a hosszúságú kimondott szöveg-vel való leképezéseket biztosítanak. 

## <a name="none-intent"></a>A None szándék

A rendszer nem hozza létre a **nincs** szándékot, de a célra üresen hagyta. A **none** cél a kötelező szándék, ezért nem törölhető és nem nevezhető át. Töltse fel, hogy a tartomány kívül esnek megcímkézzen.

A **none** cél a tartalék leképezés, amely minden alkalmazásban fontos, és az összes hosszúságú kimondott szöveg 10%-át kell tartalmaznia. A LUIS utterances, amelyek nem fontos alkalmazástartomány (tárgy területen), akik szolgál. Ha nem ad hozzá semmilyen hosszúságú kimondott szöveg a **none** szándékhoz, a Luis a tartományon kívüli teljes tartománynevet kényszeríti az egyik tartományon belüli leképezésre. Oktatási LUIS az utterance (kifejezés) a nem megfelelő leképezésének ez fog tevékenységdiagramon előrejelzési pontszámokat. 

Ha a kiírást a nem szándék alapján jósolják meg, az ügyfélalkalmazás további kérdéseket tehet fel, vagy megadhat egy menüt, amely a felhasználót az érvényes választásokra irányítja. 

## <a name="negative-intentions"></a>Negatív céljaira 
Ha meg szeretné határozni a negatív és pozitív célokat, például a **"szeretnék** autót" és a "nem **szeretnék** autót", létrehozhat két leképezést (egy pozitív és egy negatív), és hozzáadhatja a megfelelő hosszúságú kimondott szöveg. Vagy hozzon létre egy egyetlen célja, és jelölje meg a két különböző pozitív és negatív kifejezés egy egységként.  

## <a name="intents-and-patterns"></a>Szándékok és minták

Ha van példa hosszúságú kimondott szöveg, amely a részben vagy egészben is meghatározható reguláris kifejezésként, érdemes lehet a [reguláris kifejezéssel](luis-concept-entity-types.md#regular-expression-entity) párosítani egy [mintázattal](luis-concept-patterns.md). 

A reguláris kifejezéssel rendelkező entitások garantálják az kivonást, így a minta egyeztetése megtörténik. A minta egyeztetése garantálja a pontos szándékot. 

## <a name="intent-balance"></a>Leképezési terheléselosztása
Az alkalmazás tartományban szándékok utterances egyensúly kell minden egyes szándékot között. Nem rendelkezik egy leképezést és 10 kimondott szöveg és a egy másik leképezést és 500 kimondott szöveg. Ez nem elosztott terhelésű. Ha ezt a helyzetet látja el, tekintse át a 500 hosszúságú kimondott szöveg szándékát, és ellenőrizze, hogy a szándékok közül sok átrendezhető-e egy [mintázatba](luis-concept-patterns.md). 

A **nincs** szándék nem tartalmazza az egyenleget. A leképezés tartalmaznia kell az alkalmazás teljes megcímkézzen 10 %-át.

## <a name="intent-limits"></a>Leképezési korlátok
Tekintse át a [korlátokat](luis-boundaries.md#model-boundaries) , hogy megértse, hány leképezést lehet hozzáadni egy modellhez. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha több, mint a leképezések maximális számát 
Először vegye figyelembe, hogy a rendszer használja-e túl sok szándék fog vonatkozni. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több leképezések egyesíthetők egyetlen célja az entitásokkal 
Leképezések túl hasonló teheti a LUIS megkülönböztetni őket. Szándék legyen elegendő a fő feladatoktól, a felhasználó által kért, de nem szükséges minden elérési utat a kód rögzítés rögzítése változott. Ha például BookFlight és FlightCustomerService lehet, hogy egy utazási alkalmazás külön leképezések, de BookInternationalFlight és BookDomesticFlight túl hasonló. Ha a rendszer megkülönböztetésükhöz van szüksége, használja a entitások vagy más logikai helyett leképezések. 

### <a name="dispatcher-model"></a>Diszpécser modell
További információ a LUIS és a QnA Maker alkalmazások a [küldő modellel](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)való kombinálásával. 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Leképezések jelentős számú használó alkalmazásokkal kapcsolatos segítség kérése
Ha a leképezések számának csökkentése, vagy a leképezések osztani több alkalmazásokba, nem működik, forduljon az ügyfélszolgálathoz. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához. 

## <a name="next-steps"></a>További lépések

* További információ az [entitásokról](luis-concept-entity-types.md), amelyek a szándékok szempontjából fontos szavakat mutatnak
* Megtudhatja, hogyan [veheti fel és kezelheti a szándékait](luis-how-to-add-intents.md) a Luis-alkalmazásban.
* [Ajánlott eljárások](luis-concept-best-practices.md) áttekintése
