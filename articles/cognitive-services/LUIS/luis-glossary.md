---
title: Szószedet – LUIS
titleSuffix: Azure Cognitive Services
description: A szószedet kifejezések magyarázatát tartalmazza, amelyek akkor fordulhatnak végzett munka során az intelligens HANGFELISMERÉSI API-szolgáltatást.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361131"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Nyelvi ismeretek a gyakori szószedetekről és fogalmakról
A Language Understanding (LUIS) szószedet ismerteti feltételeit, amelyek találkozhat végzett munka során az intelligens HANGFELISMERÉSI API-szolgáltatást.

## <a name="active-version"></a>Aktív verzió

Az aktív LUIS-verzió verziója, amely megkapja a modell változásait. Ha a [Luis](luis-reference-regions.md) -portálon olyan verziót szeretne módosítani, amely nem az aktív verzió, először be kell állítania az adott verziót aktívként.

## <a name="authoring"></a>Authoring

A szerző [a Luis-portál vagy](luis-reference-regions.md) a [szerzői API](https://go.microsoft.com/fwlink/?linkid=2092087)-k használatával hozhat létre, kezelhet és helyezhet üzembe egy [Luis-alkalmazást](#luis-app).

## <a name="authoring-key"></a>Szerzői kulcs

Korábbi nevén "Programmatic" kulcsot. Ahhoz, hogy az alkalmazás használja. Nem éles környezetre végpont lekérdezésekhez használatos. További információ: [Key Limits](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Batch-teszt JSON-fájl

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

A LUIS kontextusban a **tartomány** az ismeretek területe. A tartomány csak a Tudásbázis alkalmazás területéhez. Ez lehet például az ügynök utazási alkalmazás általános területét. Egy ügynök utazási alkalmazás is lehet adott csak az adott földrajzi helyek, nyelvek és szolgáltatások például a vállalati adatokat területéhez.

## <a name="endpoint"></a>Végpont

A [Luis-végpont](https://go.microsoft.com/fwlink/?linkid=2092356) URL-címe a Luis-lekérdezések elküldése a [Luis-alkalmazás](#luis-app) szerzője és közzététele után. A végpont URL-címe tartalmazza a régió, a közzétett alkalmazás, valamint az alkalmazás azonosítóját. A végpontot az alkalmazás **[kulcsok és végpontok](luis-how-to-azure-subscription.md)** lapján találja, vagy lekérheti a végpont URL-címét az [app info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API-ból.

## <a name="entity"></a>Entitás

Az [entitások](luis-concept-entity-types.md) fontos szavak a [hosszúságú kimondott szöveg](luis-concept-utterance.md) , amelyek a [szándékra](luis-concept-intent.md)vonatkozó információkat írják le, és esetenként elengedhetetlenek. Egy entitás lényegében egy adattípus, a LUIS.

## <a name="f-measure"></a>F-mérték

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során a teszt pontosságának mértékét.

## <a name="false-negative"></a>Hamis negatív (FN)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jelezte a célként megadott szándék/entitás hiányát.

## <a name="false-positive"></a>Hamis pozitív (FP)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg jelentenek, amelyekben az alkalmazás helytelenül jósolta meg a cél szándék/entitás létezését.

## <a name="features"></a>Szolgáltatások

A gépi tanulásban a [funkció](luis-concept-feature.md) a rendszer által megfigyelt adattípusok megkülönböztető tulajdonságai vagy adatattribútuma.

## <a name="intent"></a>Szándék

A [szándék](luis-concept-intent.md) azt a feladatot vagy műveletet jelöli, amelyet a felhasználó végre szeretne hajtani. Egy célra vagy egy felhasználói bevitelt, például a Foglalás repülőjegyet, és a egy számlázási vagy hír keresése kifejezett célja. A LUIS a szándék előrejelzési a teljes utterance (kifejezés) alapul. Entitások, ezzel is megtalálhatja az utterance (kifejezés).

## <a name="labeling"></a>Címkézés

A címkézés vagy a jelölés az a folyamat, amellyel egy szó vagy kifejezés társítható egy, az adott [entitáshoz](#entity) (adattípus [) való](#utterance) kiíráshoz.

## <a name="luis-app"></a>LUIS-alkalmazás

A LUIS-alkalmazás a természetes nyelvi feldolgozáshoz használható nyelvi modellek gyűjteménye, beleértve a [szándékokat](#intent), az [entitásokat](#entity)és a címkézett [hosszúságú kimondott szöveg](#utterance).

## <a name="owner"></a>Tulajdonosa

Minden alkalmazás rendelkezik egy olyan tulajdonost, aki az alkalmazást létrehozó személy. A tulajdonos hozzáadhat [közreműködőket](#collaborator).

## <a name="pattern"></a>Minták
Az előző minta funkciót a rendszer a [mintázatokkal](luis-concept-patterns.md)helyettesíti. Minták segítségével előrejelzési pontosság növeléséhez azáltal, hogy kevesebb példák.

## <a name="phrase-list"></a>Kifejezések listája

A [kifejezések listája](luis-concept-feature.md) olyan értékek (szavak vagy kifejezések) egy csoportját tartalmazza, amelyek ugyanahhoz az osztályhoz tartoznak, és hasonló módon kell kezelni őket (például városok vagy termékek neve). Egy cserélhető lista szinonimák számít.

## <a name="prebuilt-domains"></a>Előre elkészített tartomány

Az [előre elkészített tartomány](luis-how-to-use-prebuilt-domains.md) egy adott tartományhoz, például a Home automationhez (HomeAutomation) vagy az éttermi foglalásokhoz (RestaurantReservation) konfigurált Luis-alkalmazás. A leképezések utterances és entitások ebben a tartományban van konfigurálva.

## <a name="prebuilt-entity"></a>Előre elkészített entitás

Az [előre elkészített entitások](luis-prebuilt-entities.md) a Luis entitások olyan általános típusú információkat biztosítanak, mint például a szám, az URL-cím és az e-mail. Úgy dönt, hogy egy előre létrehozott entitás hozzáadása az alkalmazáshoz.

## <a name="precision"></a>Pontosság
A [kötegelt tesztelés](luis-interactive-test.md#batch-testing)során a pontosság (más néven pozitív prediktív érték) a megfelelő hosszúságú kimondott szöveg töredéke a beolvasott hosszúságú kimondott szöveg között.

## <a name="programmatic-key"></a>Programozott kulcs

Átnevezve [szerzői kulcsra](#authoring-key).

## <a name="publish"></a>Közzé

A közzététel azt jelenti, hogy a LUIS aktív verziója elérhető az átmeneti vagy a termelési [végponton](#endpoint).  

## <a name="quota"></a>Kvóta

A LUIS kvóta az [Azure-előfizetési szintek](https://aka.ms/luis-price-tier)korlátozása. A LUIS-kvóta mindkét kérelmek / másodperc (HTTP-állapot 429) és a egy hónapig (HTTP-állapot 403) teljes kéréseket is korlátozza.

## <a name="recall"></a>Visszahívás
A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során a visszahívás (más néven érzékenység) lehetőség a Luis általánosítása.

## <a name="semantic-dictionary"></a>Szemantikai szótár
A szemantikai szótárba áll rendelkezésre az a entitás-lista oldalára, valamint a kifejezés-lista oldalára. A szemantikai szótár nyújt segítséget az aktuális hatókör alapján szó.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Az érzelmek elemzése a [text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)által biztosított hosszúságú kimondott szöveg pozitív vagy negatív értékeit biztosítja.

## <a name="speech-priming"></a>Beszéd alapozó

Beszéd betanítási művelet lehetővé teszi a beszédfelismerési szolgáltatás lehet felkészül a LUIS-modell.

## <a name="spelling-correction"></a>Helyesírás-javítás

Engedélyezze a Bing helyesírás-ellenőrzőjének használatát, az előrejelzési előtt megcímkézzen kijavítsa a hibásan leírt szavakat.

## <a name="starter-key"></a>Alapszintű kulcs

A LUIS használatával való első indításkor használandó ingyenes kulcs.

## <a name="structure"></a>Szerkezet

Vegyen fel struktúrát egy géppel megtanult entitásba, amely alösszetevőket biztosít a descripters (funkciók) és a megkötések (reguláris kifejezés vagy lista entitások) számára.

## <a name="subscription-key"></a>Előfizetési kulcs

Az előfizetési kulcs az [Azure-ban létrehozott Luis-](luis-how-to-azure-subscription.md)szolgáltatáshoz társított **előrejelzési végpont** kulcsa. Ez a kulcs nem a [szerzői kulcs](#programmatic-key). Ha egy végpont kulccsal rendelkezik, azt az Authoring Tool kulcs helyett végpont kérelmeket használandó. A jelenlegi végponti kulcsot a végpont URL-címében tekintheti meg a [Luis](luis-reference-regions.md) webhely [ **kulcsok és végpontok** lapja](luis-how-to-azure-subscription.md) alján. Ez az **előfizetés-kulcs** neve/érték párok értéke.

## <a name="test"></a>Teszt

A LUIS-alkalmazás [tesztelése](luis-interactive-test.md#test-your-app) azt jelenti, hogy a rendszer kimond a Luis-nek, és megtekinti a JSON-eredményeket.

## <a name="timezoneoffset"></a>Időzóna eltolása

A végpont timezoneOffset tartalmaz. Ez az a percek alatt szeretné hozzáadni, vagy távolítsa el a datetimeV2 előre összeállított entitások. Például ha az utterance (kifejezés) "eldöntve található?", a visszaadott datetimeV2 az aktuális időpont az ügyfélkérelem. Ha az ügyfél kérelem érkezik egy robot vagy más alkalmazást, amely nem ugyanaz, mint a robot felhasználói, kell adja át az eltolás a robot és a felhasználó között.

Lásd: [előre elkészített datetimeV2 entitás időzóna módosítása](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Jogkivonat
A jogkivonat a legkisebb egység is kell-e jelölve, az entitásokban, hogy. A jogkivonatok létrehozása az alkalmazás [kulturális](luis-language-support.md#tokenization)környezetén alapul.

## <a name="train"></a>Vonat

A képzés során a LUIS tanítási folyamata az utolsó betanítás óta az aktív verzió változásairól szól.

## <a name="true-negative"></a>Igaz negatív (TN)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg képviselik, amelyekben az alkalmazás megfelelően megjósolta a cél szándék/entitás hiányát.

## <a name="true-positive"></a>Valódi pozitív (TP)

A [Batch-tesztelés](luis-interactive-test.md#batch-testing)során az adatpontok olyan hosszúságú kimondott szöveg képviselik, amelyekben az alkalmazás megfelelően megjósolta a cél szándékát vagy entitását.

## <a name="utterance"></a>Utterance

Az utterance (kifejezés), például a "2 könyv jegyek következő" frissítő kedd "Seattle" természetes nyelvi kifejezést. Példa utterances kerülnek a célt.

## <a name="version"></a>Verziója

A LUIS- [verzió](luis-how-to-manage-versions.md) a Luis-alkalmazás azonosítójában és a közzétett végponton társított adatmodell. Minden LUIS alkalmazás rendelkezik legalább egy verzió.
