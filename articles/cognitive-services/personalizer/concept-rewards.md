---
title: Ellenszolgáltatás pontszám - Personalizer
titleSuffix: Azure Cognitive Services
description: A ellenszolgáltatás pontszám azt jelzi, hogy mennyire a személyre szabás tetszőleges RewardActionID, a felhasználó járt. A ellenszolgáltatás pontszám értéke határozza meg az üzleti logikát, és megfigyelik a felhasználói viselkedés alapján. Personalizer betanítja a gépi tanulási modellek által a felhőtechnológia kiértékelése.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 39db28cd7e11d77362a2aefcf4ad8d2748db59c2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722507"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Ellenszolgáltatás-hoz közeliek sikeres személyre szabása

A ellenszolgáltatás pontszám azt jelzi, hogy mennyire a személyre szabás választás [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), a felhasználó eszköznévre. A ellenszolgáltatás pontszám értéke határozza meg az üzleti logikát, és megfigyelik a felhasználói viselkedés alapján.

Personalizer betanítja a gépi tanulási modellek által a felhőtechnológia kiértékelése. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Ellenszolgáltatás pontszám küldendő Personalizer ellenszolgáltatás API használata

Jutalmakat Personalizer által küldött a [ellenszolgáltatás API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Egy fejében egy 1 és 1 közötti számot. Personalizer betanítja a modell idővel elérése érdekében a felhőtechnológia lehetséges legmagasabb összege.

Jutalmakat érkeznek, miután a felhasználó viselkedését történt, amely nappal később lehet. A legfeljebb ennyi idő Personalizer várakozik, amíg az esemény nem ellenszolgáltatás szerkezetűnek tekinthető, vagy egy alapértelmezett fejében van konfigurálva a [ellenszolgáltatás várakozási idő](#reward-wait-time) az Azure Portalon.

Ha az adott esemény ellenszolgáltatás pontszám még nem érkezett meg a **ellenszolgáltatás várakozási idő**, akkor a **alapértelmezett ellenszolgáltatás** lépnek érvénybe. Általában a **[alapértelmezett ellenszolgáltatás](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** nullának van konfigurálva.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Viselkedéseket és a felhőtechnológia fontolja meg az adatokat

Vegye figyelembe ezeket a jelek és viselkedések ellenszolgáltatás az eredmény a környezet:

* Ha a beállítás a felhasználói bevitel javaslatokért közvetlen ("erre gondolt X?").
* Munkamenetek hossza.
* Munkamenetek közötti ideje.
* Vélemények elemzése a felhasználói tevékenységeket.
* Kérdések és mini felmérések, ahol a robot megkérdezi a felhasználót, visszajelzést hasznosságát, pontosságának kapcsolatos közvetlen.
* Válasz a riasztásokat, vagy riasztás késleltetést.

## <a name="composing-reward-scores"></a>Ellenszolgáltatás pontszámok összeállítása

Az üzleti logika egy ellenszolgáltatás pontszám értékelődnek ki. A pontszámot is jeleníthető meg:

* Miután elküldött egyetlen szám 
* Azonnal (például 0,8) küldött pontszámot és a egy küldött később további pontszám (általában 0,2).

## <a name="default-rewards"></a>Alapértelmezett jutalmakat

Ha nincs ellenszolgáltatás belül nem érkezik a [ellenszolgáltatás várakozási idő](#reward-wait-time), az időtartam óta a rang hívja, Personalizer implicit módon alkalmazza a **alapértelmezett ellenszolgáltatás** a kapcsolódó rangsorolják eseményre.

## <a name="building-up-rewards-with-multiple-factors"></a>Több új tényezővel bővül jutalmakat kiépítése  

Hatékony megvalósításához, építhet fel a ellenszolgáltatás pontszám (minden 1 és 1 közötti számot) számos tényező alapján. 

Ha például alkalmazhat ezek a szabályok személyre szabásához videótartalmak listáját:

|Felhasználói viselkedés|Részleges érték pontszám|
|--|--|
|A felhasználó az első elemre kattint.|+0.5 ellenszolgáltatás|
|A felhasználó megnyitja az adott elem tényleges tartalmat.|+0.3 ellenszolgáltatás|
|A felhasználó kiváltása a tartalmat vagy 30 %-os 5 perc, a végéig.|+0.2 ellenszolgáltatás|
|||

Ezután elküldheti a teljes ellenszolgáltatás az API-hoz.

## <a name="calling-the-reward-api-multiple-times"></a>A ellenszolgáltatás API meghívására többször

Ön is meghívhatja a ellenszolgáltatás API-t küld különböző ellenszolgáltatás pontszámok azonos eseményazonosító. Personalizer ezeket jutalmakat kap, amikor meghatározza, hogy az esemény a végső ellenszolgáltatás Personalizer beállításaiban megadott összesítésével.

Összesítési beállítások:

*  **Első**: Az első fogadott esemény ellenszolgáltatás pontszám vesz igénybe, és elveti a többi.
* **Sum**: Az eseményazonosító gyűjtött összes ellenszolgáltatás pontszámok vesz igénybe, és hozzáadja őket együtt.

Egy esemény, amely után kapott összes jutalmakat a **ellenszolgáltatás várakozási idő**, elvesznek, és nincsenek hatással a képzés modellek.

Ellenszolgáltatás pontszámok mentése hozzáadásával a végső ellenszolgáltatás 1-nél magasabb vagy alacsonyabb, mint -1 lehet. Ez nem fog, hogy a szolgáltatás, sikertelen lesz.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Ajánlott eljárások a ellenszolgáltatás pontszám kiszámítása

* **Fontolja meg a sikeres személyre szabás valós kijelzőjét**: Egyszerű kattintással alkalmazásteljesítményre, de egy jó ellenszolgáltatás alapján mit szeretne a felhasználókat, hogy *elérése* ahelyett, hogy mit szeretne személyek *tegye*.  Például a kattintással követőink vezethet kiválasztása a tartalmat a clickbait rejt magában.

* **Használja a ellenszolgáltatás pontszámának milyen jól működött személyre szabása**: Személyre szabás movie javaslat remélhetőleg eredményez a felhasználó a film figyelése és a egy nagy minősítési adná. A film minősítése valószínűleg sok-sok dolog (működő, a felhasználó hangulatának minőségét) függ, mivel már nem jó ellenszolgáltatás jel, hogy hogyan jól *a személyre szabás* működött. A felhasználó az első néhány perc alatt a film nézi Előfordulhat azonban, a személyre szabás hatékonyság és a egy ellenszolgáltatás 1 elküldése után 5 perc lesz nagyobb jel nagyobb jel.

* **Csak vonatkoznak jutalmakat RewardActionID**: Personalizer megérteni a modellfelépítés hatékonyságának RewardActionID megadott művelet a felhőtechnológia vonatkozik. Ha más műveletek és a felhasználó kattint jelenítenek meg őket, a ellenszolgáltatás nullánál kell lennie.

* **Fontolja meg a nem kívánt következményekkel**: Vezető felelős kilátásainak ellenszolgáltatás függvényeket hozhat létre [etikai és felelős használatát](ethics-responsible-use.md).

* **Használja a növekményes jutalmakat**: A kisebb felhasználói viselkedés részleges jutalmakat hozzáadása segít Personalizer jobb jutalmakat elérésére. A növekményes ellenszolgáltatás lehetővé teszi, hogy az algoritmust, hogy tudja, a felhasználó a végső kívánt viselkedés szabályzatra hivatkozó közelebb van kap.
    * Ha meg van jelenítve, filmek listáját, ha a felhasználó rámutat egy ideig, további információkat az elsőt, meghatározhatja, hogy történt-e bizonyos felhasználói érdeklődést. A viselkedés 0,1 egy ellenszolgáltatás pontszámmal rendelkező is számítanak. 
    * Ha a felhasználó megnyitni a lapot, majd kilépett, a ellenszolgáltatás pontszám 0.2-es is lehet. 

## <a name="reward-wait-time"></a>Ellenszolgáltatás várakozási idő

Personalizer fog korrelációját, ha az adatokat egy Rank, hívja meg a felhőtechnológia ellenszolgáltatás hívások a modell betanításához küldi el. Ezek a különböző időpontokban is származhatnak. Personalizer a korlátozott ideig vár, amikor a rangsorolt hívás történt, még akkor is, ha a rangsorolt hívás volt inaktív eseményként készült, és később aktiválva.

Ha a **ellenszolgáltatás várakozási idő** lejár, és nem ellenszolgáltatás információk még nem, a képzési esemény alkalmazott egy alapértelmezett fejében. A maximális várakozási időtartam 6 nap.

## <a name="best-practices-for-setting-reward-wait-time"></a>Ajánlott eljárások a beállítás ellenszolgáltatás várakozási idő

Kövesse ezeket a javaslatokat, a jobb eredmények elérése érdekében.

* Győződjön meg a ellenszolgáltatás várakozási idő, amennyit csak lehet, elegendő időt a felhasználói visszajelzéseket kaphat az eszközeiről a legrövidebb. 

<!--@Edjez - storage quota? -->

* Ne adjon meg egy időtartamot, amely rövidebb, mint hogy visszajelzést szükséges időt. Például, ha a felhőtechnológia némelyike származnak után a felhasználó rendelkezik nézte 1 perces videó kísérlet hosszúságúnak kell lennie legalább két, amely.

## <a name="next-steps"></a>További lépések

* [Megerősítő tanulást](concepts-reinforcement-learning.md) 
* [A rang API kipróbálása](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Próbálja ki a ellenszolgáltatás API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
