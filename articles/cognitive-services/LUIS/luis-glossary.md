---
title: Szószedet
titleSuffix: Language Understanding - Azure Cognitive Services
description: A szószedet kifejezések magyarázatát tartalmazza, amelyek akkor fordulhatnak végzett munka során az intelligens HANGFELISMERÉSI API-szolgáltatást.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: ced83b83c2d64ba5c4816f378c66dae9f4210391
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874481"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Language understanding szószedet gyakori szóhasználatának és fogalmak
A Language Understanding (LUIS) szószedet ismerteti feltételeit, amelyek találkozhat végzett munka során az intelligens HANGFELISMERÉSI API-szolgáltatást.

## <a name="active-version"></a>Aktív verziója

Az aktív LUIS-verzió verziója, amely megkapja a modell változásait. Az a [LUIS](luis-reference-regions.md) webhely, ha azt szeretné, hogy a módosítások egy olyanra, amely verziója nem aktív, be kell először azt a verziót aktívként.

## <a name="authoring"></a>Szerzői műveletek

Szerzői rendszer azon képessége, létrehozása, kezelése és üzembe helyezéséhez egy [LUIS-alkalmazásokon](#luis-app), vagy használja a [LUIS](luis-reference-regions.md) webhely vagy a [API-k készítése](https://aka.ms/luis-authoring-api).

## <a name="authoring-key"></a>Kulcs létrehozási

Korábbi nevén "Programmatic" kulcsot. Ahhoz, hogy az alkalmazás használja. Nem éles környezetre végpont lekérdezésekhez használatos. További információkért lásd: [korlátok kulcs](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>A Batch szöveg JSON-fájl

A batch-fájl nem JSON-tömböt. A tömb egyes elemei három olyan tulajdonság tartozik: `text`, `intent`, és `entities`. A `entities` tulajdonság értéke egy tömb. A tömb üres is lehet. Ha a `entities` tömb nem üres, pontosan meghatározhatja az entitásokat kell.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Közreműködő

Közreműködő nincs a [tulajdonos](#owner) az alkalmazáshoz, de hozzáadása, szerkesztése és törlése a szándék fog vonatkozni, entitásokat, utterances megegyező alkalmazásengedéllyel rendelkezik.

## <a name="currently-editing"></a>A jelenleg szerkesztett

Ugyanaz, mint a [aktív verziója](#active-version)

## <a name="domain"></a>Tartomány

A LUIS a környezetben egy **tartomány** olyan terület, az ismeretek. A tartomány csak a Tudásbázis alkalmazás területéhez. Ez lehet például az ügynök utazási alkalmazás általános területét. Egy ügynök utazási alkalmazás is lehet adott csak az adott földrajzi helyek, nyelvek és szolgáltatások például a vállalati adatokat területéhez.

## <a name="endpoint"></a>Végpont

A [LUIS végpont](https://aka.ms/luis-endpoint-apis) URL-címe, ahol elküldése után a LUIS-lekérdezéseket a [LUIS-alkalmazásokon](#luis-app) létrehozott és közzétett. A végpont URL-címe tartalmazza a régió, a közzétett alkalmazás, valamint az alkalmazás azonosítóját. A végpont található a **[kulcsokat és a végpontok](luis-how-to-azure-subscription.md)** lapja az alkalmazás, vagy beszerezheti a végpont URL-CÍMÉT a [alkalmazásadatok lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

A példában a végpont hasonlóan néz ki:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|A lekérdezési karakterlánc paraméter|leírás|
|--|--|
|régió| [közzétett régió](luis-reference-regions.md#publishing-regions) |
|appID | A LUIS alkalmazás azonosítója |
|előfizetés-azonosító | Az Azure Portalon létrehozott LUIS-végpont (előfizetés) kulcs |
|válaszok | Utterance (kifejezés) |
|timezoneOffset| minutes|

## <a name="entity"></a>Entitás

[Entitások](luis-concept-entity-types.md) olyan fontos szavak a [beszédmódok](luis-concept-utterance.md) vonatkozó információkat a [szándékot](luis-concept-intent.md), és egyes esetekben ez a alapvető. Egy entitás lényegében egy adattípus, a LUIS.

## <a name="f-measure"></a>F-mérték

A [batch tesztelés](luis-interactive-test.md#batch-testing), a vizsgálat pontosságának mértékegysége.

## <a name="false-negative"></a>Téves negatív (TN)

A [batch tesztelés](luis-interactive-test.md#batch-testing), az adatpontok képviselik, amelyben az alkalmazás nem megfelelően előrejelzett érhető el a leképezés/célentitás a kimondott szöveg.

## <a name="false-positive"></a>False positive (TP)

A [batch tesztelés](luis-interactive-test.md#batch-testing), az adatok pontok olyan időpontokat jelölnek, kimondott szöveg, amelyben az alkalmazás nem megfelelően előre jelzett a célként megadott leképezés/entitás létezik-e.

## <a name="features"></a>Szolgáltatások

A machine learning szolgáltatás egy [funkció](luis-concept-feature.md) egy megkülönböztető is leálljon vagy attribútum, amelyek a rendszer figyelembe veszi.

## <a name="intent"></a>Leképezés

Egy [szándékot](luis-concept-intent.md) feladatot vagy műveletet a felhasználó szeretne végrehajtani jelöli. Egy célra vagy egy felhasználói bevitelt, például a Foglalás repülőjegyet, és a egy számlázási vagy hír keresése kifejezett célja. A LUIS a szándék előrejelzési a teljes utterance (kifejezés) alapul. Entitások, ezzel is megtalálhatja az utterance (kifejezés).

## <a name="labeling"></a>Címkézés

Címkézés szó vagy kifejezés egy leképezés a társítása [utterance (kifejezés)](#utterance) együtt egy [entitás](#entity) (datatype).

## <a name="luis-app"></a>A LUIS-alkalmazás

LUIS-alkalmazásokon egy olyan betanított modell természetes nyelvi feldolgozás, többek között a [leképezések](#intent), [entitások](#entity), és a címkézett [beszédmódok](#utterance).

## <a name="owner"></a>Tulajdonos

Minden alkalmazás rendelkezik egy olyan tulajdonost, aki az alkalmazást létrehozó személy. A tulajdonos adhat hozzá [közreműködők](#collaborator).

## <a name="pattern"></a>Minták
A korábbi minta funkció helyére [minták](luis-concept-patterns.md). Minták segítségével előrejelzési pontosság növeléséhez azáltal, hogy kevesebb példák.

## <a name="phrase-list"></a>Kifejezéslista

A [kifejezéslista](luis-concept-feature.md#what-is-a-phrase-list-feature) tartalmaz egy csoportot az értékek (szavak vagy kifejezések), amely ugyanahhoz az osztályhoz tartozik, és hasonló módon (például az városok vagy a termék nevét) kell kezelni. Egy cserélhető lista szinonimák számít.

## <a name="prebuilt-domains"></a>Előre összeállított tartomány

A [előre összeállított tartomány](luis-how-to-use-prebuilt-domains.md) LUIS-alkalmazások, például otthoni automation (HomeAutomation) vagy éttermi foglalások (RestaurantReservation) adott tartományhoz konfigurálva. A leképezések utterances és entitások ebben a tartományban van konfigurálva.

## <a name="prebuilt-entity"></a>Előre összeállított entitások

A [előre összeállított entitások](luis-prebuilt-entities.md) olyan entitás, LUIS nyújt általános típusú adatok például a száma, URL-cím és e-mailt. Úgy dönt, hogy egy előre létrehozott entitás hozzáadása az alkalmazáshoz.

## <a name="precision"></a>Pontosság
A [batch tesztelés](luis-interactive-test.md#batch-testing), pontosság (más néven prediktív értéke pozitív) a százalékértékében mutatkozó megfelelő utterances lekért megcímkézzen között.

## <a name="programmatic-key"></a>Programozott kulcs

Átnevezve [kulcs létrehozási](#authoring-key).

## <a name="publish"></a>Közzététele

Azt jelenti, hogy így a LUIS közzétételi [aktív verzió](#active-version) érhető el a előkészítési vagy termelési [végpont](#endpoint).  

## <a name="quota"></a>Kvóta

A LUIS-kvóta korlátozásának a [Azure-előfizetés-szintű](https://aka.ms/luis-price-tier). A LUIS-kvóta mindkét kérelmek / másodperc (HTTP-állapot 429) és a egy hónapig (HTTP-állapot 403) teljes kéréseket is korlátozza.

## <a name="recall"></a>Idézze
A [batch tesztelés](luis-interactive-test.md#batch-testing), visszahívása (más néven érzékenységi), a rendszer azon képessége, a LUIS általánosítására.

## <a name="semantic-dictionary"></a>Szemantikai szótár
A szemantikai szótárba áll rendelkezésre az a entitás-lista oldalára, valamint a kifejezés-lista oldalára. A szemantikai szótár nyújt segítséget az aktuális hatókör alapján szó.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Hangulatelemzés biztosít által biztosított megcímkézzen pozitív vagy negatív értékét [Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Beszéd betanítási művelet

Beszéd betanítási művelet lehetővé teszi a beszédfelismerési szolgáltatás lehet felkészül a LUIS-modell.

## <a name="spelling-correction"></a>Javításra

Engedélyezze a Bing helyesírás-ellenőrzőjének használatát, az előrejelzési előtt megcímkézzen kijavítsa a hibásan leírt szavakat.

## <a name="starter-key"></a>Alapszintű kulcs

Ugyanaz, mint a [programozott kulcs](#programmatic-key), átnevezett szerzői műveletek kulcs.

## <a name="subscription-key"></a>Előfizetői azonosító

Az előfizetési kulcs a **végpont** az intelligens HANGFELISMERÉSI szolgáltatás társított kulcs [Azure-ban létrehozott](luis-how-to-azure-subscription.md). Ezt a kulcsot nem kötelező a [kulcs létrehozási](#programmatic-key). Ha egy végpont kulccsal rendelkezik, azt az Authoring Tool kulcs helyett végpont kérelmeket használandó. Láthatja, hogy az aktuális végponti kulcs a végponti URL-cím végén található [ **kulcsokat és a végpontok** oldal](luis-how-to-azure-subscription.md) a [LUIS](luis-reference-regions.md) webhelyén. Az értéke, **előfizetési-kulcsa** név-érték pár.

## <a name="test"></a>Teszt

[Tesztelés](luis-interactive-test.md#test-your-app) LUIS-alkalmazásokon azt jelenti, LUIS továbbítja az utterance (kifejezés), és a JSON megtekintése eredménye.

## <a name="timezoneoffset"></a>Időzóna-eltolás

A végpont timezoneOffset tartalmaz. Ez az a percek alatt szeretné hozzáadni, vagy távolítsa el a datetimeV2 előre összeállított entitások. Például ha az utterance (kifejezés) "eldöntve található?", a visszaadott datetimeV2 az aktuális időpont az ügyfélkérelem. Ha az ügyfél kérelem érkezik egy robot vagy más alkalmazást, amely nem ugyanaz, mint a robot felhasználói, kell adja át az eltolás a robot és a felhasználó között.

Lásd: [időzóna módosítása előre összeállított datetimeV2 entitás](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
A jogkivonat a legkisebb egység is kell-e jelölve, az entitásokban, hogy. A jogkivonatok alapján az alkalmazás [kulturális környezet](luis-language-support.md#tokenization).

## <a name="train"></a>Train

Képzési azt a folyamatot, LUIS oktatási bármely érintő változásokról a [aktív verzió](#active-version) az utolsó képzési óta.

## <a name="true-negative"></a>Igaz negatív (TN)

A [batch tesztelés](luis-interactive-test.md#batch-testing), az adatpontok képviselik, amelyben az alkalmazás megfelelően előrejelzett érhető el a leképezés/célentitás a kimondott szöveg.

## <a name="true-positive"></a>TRUE positive (TP)

A [batch tesztelés](luis-interactive-test.md#batch-testing), az adatok pontok olyan időpontokat jelölnek, kimondott szöveg, amelyben az alkalmazás megfelelően előre jelzett a célként megadott leképezés/entitás létezik-e.

## <a name="utterance"></a>Utterance (kifejezés)

Az utterance (kifejezés), például a "2 könyv jegyek következő" frissítő kedd "Seattle" természetes nyelvi kifejezést. Példa utterances kerülnek a célt.

## <a name="version"></a>Verzió

A LUIS [verzió](luis-how-to-manage-versions.md) egy adott adatmodell, a LUIS app ID és a közzétett végponthoz társított. Minden LUIS alkalmazás rendelkezik legalább egy verzió.
