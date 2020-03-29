---
title: Szószedet - LUIS
titleSuffix: Azure Cognitive Services
description: A szószedet ismerteti azokkal a kifejezésekkel, amelyekkel a LUIS API-szolgáltatással való munka során találkozhat.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220951"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>A közös szókincs és fogalmak nyelvi megértési szószedete
A Language Understanding (LUIS) szószedet ismerteti azokkal a kifejezésekkel, amelyekkel a LUIS API-szolgáltatással való munka során találkozhat.

## <a name="active-version"></a><a name="active-version"></a>Aktív verzió

Az aktív LUIS-verzió az a verzió, amely megkapja a modell módosításait. A [LUIS](luis-reference-regions.md) portálon, ha módosítani szeretné egy olyan verziót, amely nem az aktív verzió, először be kell állítania azt a verziót aktívként.

## <a name="authoring"></a><a name="authoring"></a>Tartalomkészítés

A szerzői jog lehetővé teszi a [LUIS-alkalmazások](#luis-app)létrehozását, kezelését és üzembe helyezését a [LUIS-portál](luis-reference-regions.md) vagy a [szerzői API-k](https://go.microsoft.com/fwlink/?linkid=2092087)használatával.

## <a name="authoring-key"></a><a name="authoring-key"></a>Szerzői kulcs

Korábban "Programmatic" kulcsnak nevezték el. Az alkalmazás megszerzőizéséhez használható. Éles szintű végpont-lekérdezésekhez nem használható. További információ: [Key limits](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Kötegelt teszt JSON-fájl

Batch-tesztelés az a képesség, hogy érvényesítse az aktuális LUIS-alkalmazás modellje egy konzisztens és ismert tesztkészlet a felhasználói utterances. A kötegelt tesztet [JSON formátumú fájl](luis-concept-batch-test.md#batch-file-format)határozza meg.

Lásd még:
* [Fogalmak](luis-concept-batch-test.md)
* [Útmutató](luis-how-to-batch-test.md)
* [Bemutató](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Közreműködő

A közreműködő/közreműködő, nem az alkalmazás [tulajdonosa,](#owner) de ugyanazokkal az engedélyekkel rendelkezik a szándékok, entitások, kimondott szöveg hozzáadása, szerkesztése és törlése.

## <a name="contributor"></a><a name="contributor"></a>Közreműködő

A közreműködő ugyanaz, mint egy [kollaboráns](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Leíró

A leíró a modellre a betanítás időpontjában alkalmazott [szolgáltatás,](#features) beleértve [a kifejezéslistákat](#phrase-list) és [az entitásokat.](#entity) 

## <a name="domain"></a><a name="domain"></a>Tartomány

A LUIS környezetben a **tartomány** a tudás területe. A tartomány a tudás alkalmazásterületére jellemző. Ez lehet egy általános terület, például az utazási iroda alkalmazás. Az utazási iroda alkalmazás is lehet jellemző, hogy csak a területek az információk a cég, mint például a konkrét földrajzi helyek, nyelvek és szolgáltatások.

## <a name="endpoint"></a><a name="endpoint"></a>Végpont

A [LUIS-végpont](https://go.microsoft.com/fwlink/?linkid=2092356) URL-címe az a hely, ahol a [LUIS-alkalmazás](#luis-app) szerzői és közzététele után küldluis-lekérdezéseket. A végpont URL-címe tartalmazza a közzétett alkalmazás régióját, valamint az alkalmazás azonosítóját. A végpontot megtalálhatja az alkalmazás **[Kulcsok és végpontok](luis-how-to-azure-subscription.md)** lapján, vagy lekaphatja a végpont URL-címét az [Alkalmazásinformáció begetése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API-ból.

## <a name="entity"></a><a name="entity"></a>Entitás

[Entitások](luis-concept-entity-types.md) fontos szavak [at utterances,](luis-concept-utterance.md) amelyek leírják a [szándékkal](luis-concept-intent.md)kapcsolatos információkat, és néha elengedhetetlenek hozzá. Egy entitás lényegében egy adattípus a LUIS-ban.

## <a name="f-measure"></a><a name="f-measure"></a>F-mérték

A [tételvizsgálat](luis-interactive-test.md#batch-testing)során a vizsgálat pontosságát mérik.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Hamis negatív (FN)

A [kötegelt tesztelés,](luis-interactive-test.md#batch-testing)az adatpontok olyan kimondott szövegeket jelölnek, amelyekben az alkalmazás helytelenül jósolta meg a célszándék/entitás hiányát.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Hamis pozitív (FP)

A [kötegelt tesztelés,](luis-interactive-test.md#batch-testing)az adatpontok olyan kimondott szövegeket jelölnek, amelyekben az alkalmazás helytelenül előre jelezte a célszándék/entitás létezését.

## <a name="features"></a><a name="features"></a>Szolgáltatások

A gépi tanulásban a [funkció](luis-concept-feature.md) a rendszer által megfigyelt adatok megkülönböztető tulajdonsága vagy attribútuma.

## <a name="intent"></a><a name="intent"></a>Szándék

A [szándék](luis-concept-intent.md) egy olyan feladatot vagy műveletet jelöl, amelyet a felhasználó végre kíván hajtani. Ez a cél vagy cél a felhasználó hozzájárulásában kifejezve, például repülőjegy foglalása, számlafizetés vagy újságcikk keresése. A LUIS a szándék előrejelzési alapul a teljes utterance (kifejezés) alapul. Entitások, összehasonlításképpen, egy utterance (kifejezés) részei.

## <a name="labeling"></a><a name="labeling"></a>Címkézés

A címkézés vagy jelölés egy szó vagy kifejezés társításának folyamata egy szándék [kimondott szövegében](#utterance) egy [entitással](#entity) (adattípus).

## <a name="luis-app"></a><a name="luis-app"></a>LUIS alkalmazás

A LUIS-alkalmazás nyelvi modellek gyűjteménye a természetes nyelvi feldolgozáshoz, beleértve a [szándékokat,](#intent) [entitásokat](#entity)és a címkézett [utterances -t.](#utterance)

## <a name="owner"></a><a name="owner"></a>Tulajdonos

Minden alkalmazásnak van egy tulajdonosa, aki az alkalmazást létrehozó személy. A tulajdonos [közreműködőket](#collaborator)vehet fel.

## <a name="patterns"></a><a name="pattern"></a>Minták
Az előző Minta funkciót a [Minták](luis-concept-patterns.md)lépteti. A minták használatával javíthatja az előrejelzés pontosságát azáltal, hogy kevesebb betanítási példát biztosít.

## <a name="phrase-list"></a><a name="phrase-list"></a>Kifejezéslista

A [kifejezéslista](luis-concept-feature.md) azonos osztályba tartozó értékek (szavak vagy kifejezések) csoportját tartalmazza, amelyeket hasonlóan kell kezelni (például városok vagy termékek nevei). A felcserélhető listát szinonimákként kezeli a lista.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Előre összeállított tartomány

Az [előre összeállított tartomány](luis-how-to-use-prebuilt-domains.md) egy adott tartományhoz konfigurált LUIS-alkalmazás, például az otthoni automatizáláshoz (HomeAutomation) vagy az éttermi foglalásokhoz (RestaurantReservation). A leképezések, utterances és entitások vannak konfigurálva ehhez a tartományhoz.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Előre összeállított entitás

Az [előre összeállított entitás](luis-prebuilt-entities.md) olyan entitás, amelyet a LUIS a szám, az URL-cím és az e-mail gyakori típusaihoz biztosít. Úgy dönt, hogy egy előre összeállított entitást ad hozzá az alkalmazáshoz.

## <a name="precision"></a><a name="precision"></a>Precíziós
[Kötegelt teszteléskor](luis-interactive-test.md#batch-testing)a pontosság (más néven pozitív prediktív érték) a beolvasott kimondott szövegek közötti releváns kimondott szövegek töredéke.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Automatizált billentyű

Átnevezve [szerzői kulcsra.](#authoring-key)

## <a name="publish"></a><a name="publish"></a>Közzététele

A közzététel azt jelenti, hogy a LUIS aktív verzióját elérhetővé teteszi az átmeneti vagy az éles [végponton.](#endpoint)  

## <a name="quota"></a><a name="quota"></a>Kvóta

A LUIS-kvóta az [Azure előfizetési szint korlátozása.](https://aka.ms/luis-price-tier) A LUIS-kvótát a másodpercenkénti kérelmek (HTTP 429) és az összes kérelem egy hónapban (HTTP 403) korlátozhatja.

## <a name="recall"></a><a name="recall"></a>Visszahív
[Kötegelt tesztelés](luis-interactive-test.md#batch-testing), visszahívás (más néven érzékenység), az a képesség, luis általánosítani.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Szemantikus szótár
A Lista entitás lapon és a Kifejezés listalapon egy szemantikai szótár is található. A szemantikai szótár az aktuális hatókör alapján tartalmaz szójavaslatokat.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Hangulatelemzés
A hangulatelemzés a Text Analytics által biztosított kimondott szöveg pozitív vagy negatív értékeit [tartalmazza.](https://azure.microsoft.com/services/cognitive-services/text-analytics/)

## <a name="speech-priming"></a><a name="speech-priming"></a>Beszéd alapozása

A beszédfelismerés imitálása lehetővé teszi, hogy a beszédszolgáltatás a LUIS-modellel legyen alapozva.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Helyesírás-javítás

Engedélyezze a Bing helyesírás-ellenőrzőjének, hogy kijavítsa az elgépelt szavakat az előrejelzés előtt.

## <a name="starter-key"></a><a name="starter-key"></a>Kezdő gomb

Egy ingyenes kulcs használata, amikor először kezdi el a LUIS használatával.

## <a name="structure"></a><a name="structure"></a>Struktúra

Struktúra hozzáadása a gép által megtanult entitáshoz, hogy az alösszetevők et leírókkal (jellemzőkkel) és megkötésekkel (reguláris kifejezés vagy listaentitások) biztosítsa.

## <a name="subscription-key"></a><a name="subscription-key"></a>Előfizetői azonosító

Az előfizetési kulcs az [Azure-ban létrehozott](luis-how-to-azure-subscription.md)LUIS-szolgáltatáshoz társított **előrejelzési végpontkulcs.** Ez a kulcs nem a [szerzői kulcs](#programmatic-key). Ha rendelkezik egy végpontkulcs, azt kell használni a végpont kérelmek helyett a szerzői kulcs. Az aktuális végpontkulcs a végpont URL-címében található a [LUIS-webhely](luis-reference-regions.md) [ **Keys és végpontok** lapjának](luis-how-to-azure-subscription.md) alján. Ez az **előfizetési kulcs** név/értékpár értéke.

## <a name="test"></a><a name="test"></a>Test

A LUIS-alkalmazás [tesztelése](luis-interactive-test.md#test-your-app) azt jelenti, hogy egy utterance (kifejezés) a LUIS és a JSON-eredmények megtekintése.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Időzóna eltolása

A végpont tartalmazza az időzónaEltolást. Ez az a szám percben, amelyet hozzá szeretne adni vagy el szeretne távolítani a datetimeV2 előre összeállított entitásból. Például ha az utterance (kifejezés) "mikor van most?", a datetimeV2 vissza adott az aktuális idő az ügyfél kérelem. Ha az ügyfélkérelem egy olyan robotból vagy más alkalmazásból érkezik, amely nem ugyanaz, mint a robot felhasználója, akkor a robot és a felhasználó közötti eltolásban kell átadnia.

Lásd: [Előre összeállított datetimeV2 entitás időzónájának módosítása.](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)

## <a name="token"></a><a name="token"></a>Token
A jogkivonat a legkisebb egység, amely egy entitásban címkézhető. A tokenizálás az alkalmazás [kultúráján](luis-language-support.md#tokenization)alapul.

## <a name="train"></a><a name="train"></a>Betanítás

A képzés a LUIS tanításának folyamata az aktív verzió legutóbbi betanítása óta végrehajtott bármely változásról.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Valódi negatív (TN)

A [kötegelt tesztelés,](luis-interactive-test.md#batch-testing)az adatpontok olyan kimondott szövegeket jelölnek, amelyekben az alkalmazás helyesen jósolta meg a célszándék/entitás hiányát.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Valódi pozitív (TP)

A [kötegelt tesztelés,](luis-interactive-test.md#batch-testing)az adatpontok olyan kimondott szövegeket jelölnek, amelyekben az alkalmazás helyesen jósolta meg a célszándék/entitás létezését.

## <a name="utterance"></a><a name="utterance"></a>Kimondott szöveg

Az utterance (kifejezés) egy természetes nyelvű kifejezés, mint például a "könyv 2 jegyseattle jövő kedden". Példa utterances a minta hozzáadódik a szándékhoz.

## <a name="version"></a><a name="version"></a>Változat

A [LUIS-verzió](luis-how-to-manage-versions.md) egy adott adatmodell egy LUIS-alkalmazásazonosítóhoz és a közzétett végponthoz társítva. Minden LUIS-alkalmazás rendelkezik legalább egy verzióval.
