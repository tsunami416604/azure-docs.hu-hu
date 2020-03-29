---
title: Jutalom pontszám - Personalizer
description: A jutalom pontszám azt jelzi, hogy milyen jól a személyre szabott választás, RewardActionID, eredményezett a felhasználó számára. A jutalompontszám értékét az üzleti logika határozza meg, a felhasználói viselkedés megfigyelései alapján. A Personalizer a jutalmak kiértékelésével képzi ki gépi tanulási modelljeit.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219367"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>A jutalompontszámok a személyre szabás sikerét jelzik

A jutalom pontszám azt jelzi, hogy milyen jól a személyre szabott választás, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), eredményezett a felhasználó számára. A jutalompontszám értékét az üzleti logika határozza meg, a felhasználói viselkedés megfigyelései alapján.

A Personalizer a jutalmak kiértékelésével képzi ki gépi tanulási modelljeit.

Ismerje [meg, hogyan](how-to-settings.md#configure-rewards-for-the-feedback-loop) konfigurálhatja az alapértelmezett jutalompontszámot az Azure Portalon a Personalizer erőforráshoz.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Jutalom API használata jutalompontszám küldéséhez a Personalizer-nek

A jutalmakat a Jutalom API küldi el a [Personalizernek.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) A jutalom általában 0 és 1 között van. A negatív jutalom, az érték -1, lehetséges bizonyos forgatókönyvek, és csak akkor kell használni, ha tapasztalt megerősítése tanulás (RL). A personalizer beképzi a modellt, hogy idővel a lehető legmagasabb jutalomösszeget érje el.

A jutalmakat a felhasználói viselkedés után küldik el, ami napokkal később is megtörténhet. A Personalizer maximális anamtalánk megvárja, amíg egy esemény nem minősül jutalomnak, vagy az alapértelmezett jutalom az Azure Portalon a [jutalom várakozási ideje](#reward-wait-time) beállításával van konfigurálva.

Ha egy esemény jutalompontszáma nem érkezett meg a **jutalom várakozási idején**belül, akkor az **alapértelmezett jutalom** kerül alkalmazásra. Az alapértelmezett **[jutalom](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** általában nullára van konfigurálva.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>A jutalmakhoz figyelembe vejt viselkedések és adatok

Tekintsük ezeket a jeleket és viselkedést a jutalompontszám kontextusában:

* Közvetlen felhasználói bevitel a javaslatokhoz, ha opciókról van szó ("Úgy érted, X?").
* A munkamenet hossza.
* A foglalkozások között töltött idő.
* A felhasználó interakcióinak hangulatelemzése.
* Közvetlen kérdések és mini felmérések, ahol a bot kéri a felhasználó visszajelzést hasznosságát, pontosságát.
* Válasz a riasztásokra, vagy késleltetheti a riasztásokra adott válaszokat.

## <a name="composing-reward-scores"></a>Jutalompontszámok összeállítása

A jutalom pontszámot az üzleti logikában kell kiszámítani. A pontszám a következőképpen ábrázolható:

* Egyetlen szám, amelyet egyszer küldtek el
* A pontszám azonnal elküldve (például 0,8) és egy további pontszám később (általában 0,2).

## <a name="default-rewards"></a>Alapértelmezett jutalmak

Ha a [jutalom várakozási idején](#reward-wait-time)belül nem érkezik jutalom , a Ranghívás óta eltelt időtartam, a Personalizer implicit módon az **alapértelmezett jutalmat** alkalmazza az adott Rang eseményre.

## <a name="building-up-rewards-with-multiple-factors"></a>Jutalmak kiépítése több tényezővel

A hatékony személyre szabás érdekében a jutalompontszámot több tényező alapján is felépítheti.

Alkalmazhatja például ezeket a szabályokat a videotartalmak listájának személyre szabására:

|A felhasználó viselkedése|Részleges pontszám értéke|
|--|--|
|A felhasználó a legfelső elemre kattintott.|+0,5 jutalom|
|A felhasználó megnyitotta az elem tényleges tartalmát.|+0,3 jutalom|
|A felhasználó 5 percet vagy 30%-ot nézett a tartalomból, attól függően, hogy melyik a hosszabb.|+0,2 jutalom|
|||

Ezután elküldheti a teljes jutalmat az API-nak.

## <a name="calling-the-reward-api-multiple-times"></a>A Jutalom API többszöri felhívása

A Jutalom API-t ugyanazzal az eseményazonosítóval is meghívhatja, és különböző jutalompontszámokat küldhet. Amikor a Personalizer megkapja ezeket a jutalmakat, meghatározza az esemény végső jutalmát a Personalizer konfigurációban megadott aggregálással.

Összesítési értékek:

*  **Először is**: Az eseményhez kapott első jutalompontszámot veszi, a többit pedig elveti.
* **Összeg**: Az eseményazonosítóhoz összegyűjtött összes jutalompontszámot összeadja, és összeadja őket.

A **jutalom várakozási ideje**után kapott összes jutalom ellesz vetve, és nem befolyásolja a modellek képzését.

A jutalompontszámok összeadásával a végső jutalom a várt pontszámtartományon kívül esik. Ez nem fogja megbukni a szolgáltatást.

## <a name="best-practices-for-calculating-reward-score"></a>Ajánlott eljárások a jutalompontszám kiszámításához

* **Tekintsük a sikeres személyre szabás valódi mutatóit**: Könnyű a kattintások tekintetében gondolkodni, de a jó jutalom azon alapul, hogy mit szeretne a *felhasználóknak elérni,* ahelyett, hogy azt szeretné, hogy az emberek *mit tegyenek.*  Például a kattintások jutalmazása olyan tartalom kiválasztásához vezethet, amely kattintásra hajlamos.

* **Használja a jutalom pontszám, hogy milyen jó a személyre szabott működött:** Személyre szabott film javaslatot remélhetőleg eredményeza felhasználó nézi a filmet, és így ez a magas értékelés. Mivel a film értékelése valószínűleg sok mindentől függ (a színészet minősége, a felhasználó hangulata), ez nem jó jutalomjel arra, hogy milyen jól működött *a személyre szabás.* A felhasználó figyeli az első néhány perc a film, azonban lehet egy jobb jel a személyre szabott hatékonyság és a küldő jutalom 1 5 perc után lesz a jobb jel.

* **A jutalmak csak a RewardActionID-re vonatkoznak:** A Personalizer a jutalmakat alkalmazza a RewardActionID-ben megadott művelet hatékonyságának megértéséhez. Ha úgy dönt, hogy más műveleteket jelenít meg, és a felhasználó rájuk kattint, a jutalomnak nullának kell lennie.

* **Fontolja meg a nem kívánt következményeket**: Olyan jutalomfunkciók létrehozása, amelyek felelős eredményekhez vezetnek [az etikával és a felelős használattal.](ethics-responsible-use.md)

* **Növekményes jutalmak használata:** Részleges jutalmak hozzáadása a kisebb felhasználói viselkedéshez segít a Personalizernek a jobb jutalmak elérésében. Ez a növekményes jutalom lehetővé teszi az algoritmus számára, hogy tudja, hogy egyre közelebb kerül a felhasználó végső kívánt viselkedésének bevonásához.
    * Ha a filmek listáját jeleníti meg, ha a felhasználó egy ideig az első fölé viszi az egérmutatót, hogy további információkat lásson, megállapíthatja, hogy bizonyos felhasználói aktivitás történt-e. A viselkedés 0,1-es jutalompontszámmal számolhat.
    * Ha a felhasználó megnyitotta az oldalt, majd kilépett, a jutalom pontszám 0.2 lehet.

## <a name="reward-wait-time"></a>Jutalom várakozási idő

A Personalizer korrelálja a Ranghívás adatait a Jutalomhívásokban küldött jutalmakkal a modell betanításához. Ezek különböző időpontokban érkezhetnek. A Personalizer korlátozott ideig vár, kezdve a Rang hívás sal, még akkor is, ha a Rang hívás inaktív eseményként történt, és később aktiválódik.

Ha a **jutalom várakozási ideje** lejár, és nem volt jutalominformáció, az adott eseményre alapértelmezett jutalom vonatkozik az edzésre. A maximális várakozási idő 6 nap.

## <a name="best-practices-for-reward-wait-time"></a>Ajánlott eljárások a jutalom várakozási idejéhez

Kövesse ezeket az ajánlásokat a jobb eredmények érdekében.

* Legyen a jutalom várakozási idő olyan rövid, mint akkor, miközben elég időt, hogy a felhasználói visszajelzést.

* Ne válasszon rövidebb időtartamot, mint a visszajelzéshez szükséges idő. Ha például a jutalmak egy része azt követően érkezik meg, hogy a felhasználó 1 percnyi videót nézett meg, a kísérlet hosszának legalább a duplája kell lennie.

## <a name="next-steps"></a>További lépések

* [Megerősítő tanulás](concepts-reinforcement-learning.md)
* [Próbálja ki a Rank API-t](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Próbálja ki a Jutalom API-t](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
