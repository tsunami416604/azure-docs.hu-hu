---
title: A nyelv (LUIS) ismertetése API szolgáltatás szószedet |} Microsoft Docs
description: A szószedet kifejezések magyarázatát a LUIS API szolgáltatás használatakor esetleg előforduló.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 7cfcc5b907b28a877bea57ea869e17f01aae00cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265387"
---
# <a name="glossary"></a>Szószedet

## <a name="active-version"></a>Aktív verzió

Az aktív LUIS verzió verziója, amely megkapja a modell módosításait. Az a [LUIS](luis-reference-regions.md) webhelyet, ha a kívánt módosításokat a rendszer a nem aktív verziója, be kell először verzió aktívnak. 

## <a name="authoring"></a>Szerzői

Szerzői azt a képességet létrehozására, kezelésére és telepítésére egy [LUIS app](#luis-app), vagy használja a [LUIS](luis-reference-regions.md) webhely vagy a [API-k szerzői](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>szerzői kulcs

Korábbi nevén a "Programmatic" kulcs. Ahhoz, hogy az alkalmazás használja. Nem használatos a termelési szint végpont lekérdezésekhez. További információkért lásd: [korlátok kulcs](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Kötegelt szöveg JSON-fájl

A köteg fájlja egy JSON-tömb. A tömb egyes elemei három tulajdonságokkal rendelkezik: `text`, `intent`, és `entities`. A `entities` tulajdonsága egy tömb. A tömb üres is lehet. Ha a `entities` tömb nem üres, így ellenőrzi, hogy pontosan meghatározhatja az entitásokat.

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

Közreműködő nincs a [tulajdonos](#owner) az alkalmazás, de hozzáadása, szerkesztése és törlése a leképezések, entitásokat, utterances ugyanazokkal az engedélyekkel rendelkezik.

## <a name="currently-editing"></a>A jelenleg szerkesztett

Ugyanaz, mint a [aktív verzió](#active-version)

## <a name="domain"></a>Tartomány

A LUIS a környezetben egy **tartomány** Tudásbázis területe. A tartomány csak a Tudásbázis alkalmazást területe az elő. Ez lehet például a utazás ügynök alkalmazás általános területét. Lehet, hogy egy utazás ügynök alkalmazást is vonatkozó információkat a vállalat például adott földrajzi helyeken, a nyelvet és a szolgáltatások azon területeit. 

## <a name="endpoint"></a>végpont

A [LUIS végpont](https://aka.ms/luis-endpoint-apis) URL-címe, ahol LUIS lekérdezések után küldje el a [LUIS app](#luis-app) létrehozott és közzétett. A végponti URL-cím tartalmazza a területet a közzétett alkalmazást, valamint az alkalmazás azonosítóját. A végpont található a **[közzététel](publishapp.md)** az alkalmazást, az erőforrások és a kulcsok tábla, vagy a oldalán kérheti le a végpont URL-CÍMÉT a [alkalmazásadatok lekérése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

Példa a végpont néz ki:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Lekérdezési karakterlánc paraméter|leírás|
|--|--|
|régió| [közzétett régió](luis-reference-regions.md#publishing-regions) |
|appID | LUIS alkalmazás azonosítója |
|Előfizetés-azonosító | Azure-portálon létrehozott LUIS előfizetés kulcsot |
|A Q | Utterance |
|timezoneOffset| minutes|

## <a name="entity"></a>Entitás

[Entitások](luis-concept-entity-types.md) a fontos szavak [utterances](luis-concept-utterance.md) vonatkozó információkat a [leképezés](luis-concept-intent.md), és egyes esetekben nélkülözhetetlenek rá. Egy entitás alapvetően a LUIS adattípust. 

## <a name="f-measure"></a>F-mérték

A [kötegelt tesztelés][batch-testing], a vizsgálat pontosságának biztosítása.

## <a name="false-negative"></a>Hamis negatív (TN)

A [kötegelt tesztelés][batch-testing], az adatok pontok olyan időpontokat jelölnek, amelyben az alkalmazás nem megfelelően előre jelezni a leképezés/célentitás hiányában utterances.

## <a name="false-positive"></a>Vakriasztás (TP)

A [kötegelt tesztelés][batch-testing], az adatok pontok olyan időpontokat jelölnek, amelyben az alkalmazás nem megfelelően előre jelezni létezik-e a cél/célentitás utterances.

## <a name="features"></a>Szolgáltatások

A machine learning szolgáltatásban egy [szolgáltatás](luis-concept-feature.md) megkülönböztető tulajdonságra vagy a rendszer betartja adatok attribútum.

## <a name="intent"></a>Kísérlet történt

Egy [leképezés](luis-concept-intent.md) egy feladat vagy a felhasználó szeretne végrehajtani a műveletet. Egy célra vagy egy felhasználói bevitelt, például a felhőszolgáltató közötti átviteléhez foglalás, egy számlázási fizető vagy egy hírek cikkben találja a kifejezett cél. A LUIS leképezési előrejelzését a teljes utterance alapul. Entitások, összehasonlítva, olyan utterance adatot.

## <a name="labeling"></a>címkézés

Társítása egy szót vagy kifejezést egy kísérlet történt a címkézés [utterance](#utterance) rendelkező egy [entitás](#entity) (adattípus). 

## <a name="luis-app"></a>LUIS alkalmazás

Egy LUIS alkalmazást egy olyan képzett adatok modell természetes nyelvű feldolgozási beleértve [leképezések](#intent), [entitások](#entity), és címkézett [utterances](#utterance).

## <a name="owner"></a>Tulajdonos

Minden alkalmazás rendelkezik egy olyan tulajdonost, aki a személy, aki az alkalmazás létrehozása. A tulajdonos adhat hozzá [közreműködők](#collaborator).

## <a name="pattern"></a>Minták
A korábbi minta szolgáltatást helyére [minták](luis-concept-patterns.md). Minták segítségével kevesebb példák megadásával növelve az előrejelzés pontosságát. 

## <a name="phrase-list"></a>Kifejezéslista

A [kifejezéslista](luis-concept-feature.md#what-is-a-phrase-list-feature) értékek (szavakat vagy kifejezéseket), amelyek ugyanahhoz az osztályhoz tartozik a tartoznak, hasonlóképpen (például az várost vagy termékek nevének) kell kezelni egy csoportot. Egy cserélhető lista szinonimák kell kezelni. 

## <a name="prebuilt-domains"></a>Előre elkészített tartomány

A [előre elkészített tartomány](luis-how-to-use-prebuilt-domains.md) egy LUIS alkalmazás, például otthoni automatizálási (HomeAutomation) vagy éttermi foglalások (RestaurantReservation) adott tartományon konfigurálva. A leképezések utterances és entitások ebben a tartományban van állítva. 

## <a name="prebuilt-entity"></a>Előre elkészített entitás

A [előre elkészített entitás](pre-builtentities.md) LUIS biztosít a száma, az URL-cím és az e-mailek általános típusú entitás. Úgy dönt, hogy egy előre elkészített entitás hozzá az alkalmazáshoz. 

## <a name="precision"></a>Pontosság
A [kötegelt tesztelés][batch-testing], a pontosság (más néven prediktív értékének pozitívnak) között a lekért utterances vonatkozó utterances hányada.

## <a name="programmatic-key"></a>Programozott kulcs

Az átnevezett [kulcs szerzői](#authoring-key). 

## <a name="publish"></a>Közzététele

Azt jelenti, hogy így egy LUIS közzétételi [aktív verzió](#active-version) átmeneti vagy üzemi [végpont](#endpoint).  

## <a name="quota"></a>kvóta

LUIS kvóta a korlátozás a [Azure-előfizetés réteg](https://aka.ms/luis-price-tier). Mindkét kérelmek / másodperc (HTTP-állapot 429) és a hónap (HTTP-állapot 403) kérelmek teljes száma a LUIS kvóta is korlátozza. 

## <a name="recall"></a>Visszahívása
A [kötegelt tesztelés][batch-testing], visszahívása (más néven érzékenységi), azt a képességet a LUIS általánosítására. 

## <a name="semantic-dictionary"></a>A szemantikai szótár
A lista entitás lapon, valamint a kifejezés lista lap szemantikai dictionary valósul meg. A szemantikai szótár megjelöl szavak alapján a jelenlegi hatókörben.

## <a name="sentiment-analysis"></a>Véleményeket elemzés
Véleményeket elemzés biztosít pozitív vagy negatív értékeket a utterances által biztosított [Szövegelemzések](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Beszéd betanítási művelet

Beszéd betanítási művelet lehetővé teszi a beszédfelismerés szolgáltatás a LUIS modellel fel kell. Lásd: [beszéd elsődleges engedélyezése ](publishapp.md#enable-speech-priming).

## <a name="spelling-correction"></a>Javításra

A közzététel lapon engedélyezze [Bing helyesírás-ellenőrző](publishapp.md#enable-bing-spell-checker) az előrejelzés előtt utterances elírt szavak. 

## <a name="starter-key"></a>Alapszintű kulcs

Ugyanaz, mint a [programozott kulcs](#programmatic-key), nevezték át, a szerzői műveletek kulcshoz.

## <a name="subscription-key"></a>Előfizetés kulcs

Az Előfizetés kulcs a kulcs LUIS-szolgáltatáshoz társított [az Azure-ban létrehozott](luis-how-to-azure-subscription.md). Ez a kulcs nincs a [kulcs szerzői](#programmatic-key). Ha rendelkezik előfizetéssel kulccsal, azt a szerzői műveletekhez kulcs helyett végpont kérelmeket használandó. A jelenlegi előfizetés kulcs belül a végponti URL-cím alján látható [ **alkalmazás közzététele** lap](publishapp.md) a [LUIS](luis-reference-regions.md) webhelyet. Az érték **előfizetés-kulcs** név-érték pár. 

## <a name="test"></a>Teszt

[Tesztelési](interactive-test.md#test-your-app) egy LUIS alkalmazást azt jelenti, hogy egy utterance átadni LUIS, és tekintse meg a JSON annak az eredménye.

## <a name="timezoneoffset"></a>Időzóna-eltolás

A végpont timezoneOffset tartalmazza. Ez az a percben hozzáadása vagy eltávolítása a datetimeV2 előre elkészített entitás. Például a utterance "mikor található?", a visszaadott datetimeV2 akkor az ügyfél kérés a jelenlegi időpontnál. Ha az ügyfél kérelem érkezik a bot vagy más alkalmazás nem ugyanaz, mint a botot felhasználói, kell adni az eltolás a botot és a felhasználó között. 

Lásd: [előre elkészített datetimeV2 entitás időzónájának módosítása](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
A lexikális elem a legkisebb egység, amely az entitás neve is. Az alkalmazás alapján lexikális elemekké alakításának [kulturális környezet](luis-supported-languages.md#tokenization).

## <a name="train"></a>Vonat

Képzési LUIS oktatási kapcsolatos módosításokat során a rendszer a [aktív verzió](#active-version) az utolsó képzési óta.

## <a name="true-negative"></a>Igaz negatív (TN)

A [kötegelt tesztelés][batch-testing], az adatok pontok olyan időpontokat jelölnek, amelyben az alkalmazás megfelelően előre jelezni a leképezés/célentitás hiányában utterances.

## <a name="true-positive"></a>Valódi pozitív (TP)

A [kötegelt tesztelés][batch-testing], az adatok pontok olyan időpontokat jelölnek, amelyben az alkalmazás megfelelően előre jelezni létezik-e a cél/célentitás utterances.

## <a name="utterance"></a>Utterance

Egy utterance például a "könyv 2 jegyek Budapest következő kedd" természetes nyelvű kifejezés. Példa utterances hozzáadódnak a célt. 

## <a name="version"></a>Verzió

Egy LUIS [verzió](luis-how-to-manage-versions.md) egy adott adatmodell egy LUIS app ID és a közzétett végponthoz társított. Minden LUIS alkalmazás legalább egy verziója van.

[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/interactive-test#batch-testing
