---
title: Szószedet – LUIS
titleSuffix: Azure Cognitive Services
description: A Szószedet a LUIS API szolgáltatással való munka során felmerülő feltételeket ismerteti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 22e8fa8fb6999828076ea5f8f34b1f601b920013
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499573"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Nyelvi ismeretek a gyakori szószedetekről és fogalmakról
A Language Understanding (LUIS) Szószedet ismerteti azokat a feltételeket, amelyek a LUIS API szolgáltatással való együttműködés során merülhetnek fel.

## <a name="active-version"></a>Aktív verzió

Az aktív LUIS-verzió az a verzió, amely a modell változásait fogadja. Ha a [Luis](luis-reference-regions.md) -portálon olyan verziót szeretne módosítani, amely nem az aktív verzió, először be kell állítania az adott verziót aktívként.

## <a name="authoring"></a>Authoring

A szerző [a Luis-portál vagy](luis-reference-regions.md) a [szerzői API](https://go.microsoft.com/fwlink/?linkid=2092087)-k használatával hozhat létre, kezelhet és helyezhet üzembe egy [Luis-alkalmazást](#luis-app).

## <a name="authoring-key"></a>Szerzői kulcs

Korábban "programozott" kulcs lett elnevezve. Az alkalmazás létrehozásához használatos. Üzemi szintű végponti lekérdezésekhez nem használatos. További információ: [Key Limits](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Batch-szöveg JSON-fájlja

A Batch-tesztelés lehetővé teszi, hogy a jelenlegi LUIS-alkalmazás modelljét érvényesítse a felhasználói hosszúságú kimondott szöveg konzisztens és ismert tesztelési készletével. A Batch-teszt egy JSON- [formátumú fájlban](luis-concept-batch-test.md#batch-file-format)van definiálva.

Lásd még: 
* [Alapelvek](luis-concept-batch-test.md)
* [Útmutató](luis-how-to-batch-test.md)
* [Oktatóanyag](luis-tutorial-batch-testing.md)


## <a name="collaborator"></a>Munkatársa

Egy közreműködő vagy közreműködő nem [tulajdonosa](#owner) az alkalmazásnak, de ugyanazokkal az engedélyekkel rendelkezik a leképezések, az entitások és a hosszúságú kimondott szöveg hozzáadásához, szerkesztéséhez és törléséhez.

## <a name="contributor"></a>Közreműködői

A közreműködő ugyanaz, mint egy [közreműködő.](#collaborator)

## <a name="descriptor"></a>Leíró

A leíró egy olyan [szolgáltatás](#features) , amely a modellre vonatkozik a betanítási idő, beleértve a [kifejezések listáját](#phrase-list) és az [entitásokat](#entity). 

## <a name="domain"></a>Tartományi

A LUIS kontextusban a **tartomány** az ismeretek területe. Az Ön tartománya kifejezetten az alkalmazási terület ismerete. Ez lehet egy általános felület, például az utazási ügynök alkalmazás. Az utazási ügynök alkalmazása a vállalatra vonatkozó információk (például adott földrajzi helyszínek, nyelvek és szolgáltatások) területére is jellemző lehet.

## <a name="endpoint"></a>Végpont

A [Luis-végpont](https://go.microsoft.com/fwlink/?linkid=2092356) URL-címe a Luis-lekérdezések elküldése a [Luis-alkalmazás](#luis-app) szerzője és közzététele után. A végpont URL-címe tartalmazza a közzétett alkalmazás régióját, valamint az alkalmazás AZONOSÍTÓját. A végpontot az alkalmazás **[kulcsok és végpontok](luis-how-to-azure-subscription.md)** lapján találja, vagy lekérheti a végpont URL-címét az [app info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API-ból.

## <a name="entity"></a>Entitás

Az [entitások](luis-concept-entity-types.md) fontos szavak a [hosszúságú kimondott szöveg](luis-concept-utterance.md) , amelyek a [szándékra](luis-concept-intent.md)vonatkozó információkat írják le, és esetenként elengedhetetlenek. Az entitás lényegében a LUIS adattípusa.

## <a name="f-measure"></a>F-mérték

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során a teszt pontosságának mértékét.

## <a name="false-negative"></a>Hamis negatív (FN)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jelezte a célként megadott szándék/entitás hiányát.

## <a name="false-positive"></a>Hamis pozitív (FP)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jósolta meg a cél szándék/entitás létezését.

## <a name="features"></a>Szolgáltatások

A gépi tanulásban a [funkció](luis-concept-feature.md) a rendszer által megfigyelt adattípusok megkülönböztető tulajdonságai vagy adatattribútuma.

## <a name="intent"></a>Szándék

A [szándék](luis-concept-intent.md) azt a feladatot vagy műveletet jelöli, amelyet a felhasználó végre szeretne hajtani. Ez a felhasználó bemenetében kifejezett cél vagy cél, például egy repülés foglalása, számla kifizetése vagy újságcikk megkeresése. A LUIS-ben a szándék előrejelzése a teljes Kimondás alapján történik. Az entitások összehasonlítva megnyilatkozzák az elemeket.

## <a name="labeling"></a>Címkézés

A címkézés vagy a jelölés az a folyamat, amellyel egy szó vagy kifejezés társítható egy, az adott [entitáshoz](#entity) (adattípus [) való](#utterance) kiíráshoz.

## <a name="luis-app"></a>LUIS-alkalmazás

A LUIS-alkalmazás a természetes nyelvi feldolgozáshoz használható nyelvi modellek gyűjteménye, beleértve a [szándékokat](#intent), az [entitásokat](#entity)és a címkézett [hosszúságú kimondott szöveg](#utterance).

## <a name="owner"></a>Tulajdonosa

Minden alkalmazás rendelkezik egy tulajdonossal, aki az alkalmazást létrehozó személy. A tulajdonos hozzáadhat [közreműködőket](#collaborator).

## <a name="pattern"></a>Minták
Az előző minta funkciót a rendszer a [mintázatokkal](luis-concept-patterns.md)helyettesíti. Használjon mintázatokat az előrejelzési pontosság növeléséhez azáltal, hogy kevesebb betanítási példát biztosít.

## <a name="phrase-list"></a>Kifejezések listája

A [kifejezések listája](luis-concept-feature.md) olyan értékek (szavak vagy kifejezések) egy csoportját tartalmazza, amelyek ugyanahhoz az osztályhoz tartoznak, és hasonló módon kell kezelni őket (például városok vagy termékek neve). Egy felcserélhető lista szinonimának minősül.

## <a name="prebuilt-domains"></a>Előre elkészített tartomány

Az [előre elkészített tartomány](luis-how-to-use-prebuilt-domains.md) egy adott tartományhoz, például a Home automationhez (HomeAutomation) vagy az éttermi foglalásokhoz (RestaurantReservation) konfigurált Luis-alkalmazás. A szándékok, a hosszúságú kimondott szöveg és az entitások konfigurálva vannak ehhez a tartományhoz.

## <a name="prebuilt-entity"></a>Előre elkészített entitás

Az [előre elkészített entitások](luis-prebuilt-entities.md) a Luis entitások olyan általános típusú információkat biztosítanak, mint például a szám, az URL-cím és az e-mail. Úgy dönt, hogy hozzáad egy előre elkészített entitást az alkalmazáshoz.

## <a name="precision"></a>Pontosság
A [kötegelt tesztelés](luis-interactive-test.md#batch-testing)során a pontosság (más néven pozitív prediktív érték) a megfelelő hosszúságú kimondott szöveg töredéke a beolvasott hosszúságú kimondott szöveg között.

## <a name="programmatic-key"></a>Programozott kulcs

Átnevezve [szerzői kulcsra](#authoring-key).

## <a name="publish"></a>Közzé

A közzététel azt jelenti, hogy a LUIS aktív verziója elérhető az átmeneti vagy a termelési [végponton](#endpoint).  

## <a name="quota"></a>Kvóta

A LUIS kvóta az [Azure-előfizetési szintek](https://aka.ms/luis-price-tier)korlátozása. A LUIS-kvótát a másodpercenkénti kérések (429-es HTTP-állapot) és a havi kérelmek teljes száma korlátozhatja (HTTP-állapot: 403).

## <a name="recall"></a>Visszahívás
A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során a visszahívás (más néven érzékenység) lehetőség a Luis általánosítása.

## <a name="semantic-dictionary"></a>Szemantikai szótár
A szemantikai szótár megjelenik az entitás listázása lapon, valamint a kifejezések listája oldalon. A szemantikai szótár az aktuális hatókör alapján javaslatokat tesz a szavakra.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Az érzelmek elemzése a [text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)által biztosított hosszúságú kimondott szöveg pozitív vagy negatív értékeit biztosítja.

## <a name="speech-priming"></a>Beszéd alapozó

A beszédfelismerési funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás a LUIS-modellel legyen kitöltve.

## <a name="spelling-correction"></a>Helyesírás-javítás

A Bing spell-ellenőrző engedélyezése a helytelenül beírt szavak kijavításához a hosszúságú kimondott szöveg az előrejelzés előtt.

## <a name="starter-key"></a>Alapszintű kulcs

A LUIS használatával való első indításkor használandó ingyenes kulcs.

## <a name="structure"></a>Szerkezet

Vegyen fel struktúrát egy géppel megtanult entitásba, amely alösszetevőket biztosít a descripters (funkciók) és a megkötések (reguláris kifejezés vagy lista entitások) számára.

## <a name="subscription-key"></a>Előfizetési kulcs

Az előfizetési kulcs az [Azure-ban létrehozott Luis-](luis-how-to-azure-subscription.md)szolgáltatáshoz társított **előrejelzési végpont** kulcsa. Ez a kulcs nem a [szerzői kulcs](#programmatic-key). Ha van végponti kulcsa, azt a szerzői kulcs helyett a végponti kérelmekhez kell használni. A jelenlegi végponti kulcsot a végpont URL-címében tekintheti meg a [Luis](luis-reference-regions.md) webhely [ **kulcsok és végpontok** lapja](luis-how-to-azure-subscription.md) alján. Ez az **előfizetés-kulcs** neve/érték párok értéke.

## <a name="test"></a>Teszt

A LUIS-alkalmazás [tesztelése](luis-interactive-test.md#test-your-app) azt jelenti, hogy a rendszer kimond a Luis-nek, és megtekinti a JSON-eredményeket.

## <a name="timezoneoffset"></a>Időzóna eltolása

A végpont tartalmazza a timezoneOffset. A datetimeV2 előre összeépített entitásból felvenni vagy eltávolítani kívánt percek száma. Ha például a Kimondás a "mi idő most?", a visszaadott datetimeV2 az ügyfél-kérelem aktuális ideje. Ha az ügyfél kérése egy olyan robotból vagy más alkalmazásból származik, amely nem egyezik meg a robot felhasználójának, akkor a bot és a felhasználó közötti eltolást kell átadnia.

Lásd: [előre elkészített datetimeV2 entitás időzóna módosítása](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Jogkivonat
A token az entitásokban címkézhető legkisebb egység. A jogkivonatok létrehozása az alkalmazás [kulturális](luis-language-support.md#tokenization)környezetén alapul.

## <a name="train"></a>Vonat

A képzés során a LUIS tanítási folyamata az utolsó betanítás óta az aktív verzió változásairól szól.

## <a name="true-negative"></a>Igaz negatív (TN)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg képviselik, amelyekben az alkalmazás megfelelően megjósolta a cél szándék/entitás hiányát.

## <a name="true-positive"></a>Valódi pozitív (TP)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg képviselik, amelyekben az alkalmazás megfelelően megjósolta a cél szándékát vagy entitását.

## <a name="utterance"></a>Utterance

A Kimondás természetes nyelvi kifejezés, mint például a "2. könyv jegyek a Seattle következő kedden". Példaként adja hozzá a hosszúságú kimondott szöveg a szándékhoz.

## <a name="version"></a>Verziója

A LUIS- [verzió](luis-how-to-manage-versions.md) a Luis-alkalmazás azonosítójában és a közzétett végponton társított adatmodell. Minden LUIS-alkalmazásnak legalább egy verziója van.
