---
title: Hibaelhárítás – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832182"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabás – hibaelhárítás

Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="transaction-errors"></a>Tranzakciós hibák

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>HTTP 429 (túl sok kérés) választ kapok a szolgáltatástól. Mit tegyek?

Ha a személyre szabott példány létrehozásakor kiválasztott egy ingyenes árat, a rendszer kvótát korlátoz az engedélyezett rangsorolt kérelmek számára. Tekintse át a Range API API-hívások arányát (a személyre szabott erőforráshoz tartozó Azure Portal mérőszámok ablaktábláján), és állítsa be az árképzési szintet (az árképzési szint ablaktáblán), ha a hívási kötet a kiválasztott árképzési szintnél nagyobb küszöbértéket meghaladóan nő.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>5xx-hibát kapok a Rank vagy a jutalmazási API-k esetében. Mit tegyek?

Ezeknek a problémáknak transzparenseknek kell lenniük. Ha folytatják, forduljon az ügyfélszolgálathoz az **új támogatási kérelem** kiválasztásával a **támogatás + hibaelhárítás** szakaszban, a személyre szabott erőforrás Azure Portal.


## <a name="learning-loop"></a>Tanulási hurok

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Úgy tűnik, hogy a tanulási hurok nem tanul. Hogyan javítsa ezt?

A tanulási huroknak néhány ezer jutalmazási hívásra van szüksége, mielőtt rangsorolja a rangsorolási hívásokat.

Ha nem biztos abban, hogy hogyan viselkedik a tanulási hurok jelenleg, futtasson egy [Offline értékelést](concepts-offline-evaluation.md), és alkalmazza a javított tanulási szabályzatot.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>A rangsorban az összes elemre vonatkozó összes valószínűségi eredményt megtartom. Hogyan ismeri a személyre szabott tanulást?

A személyre szabási API-eredményben ugyanazokat a valószínűségeket adja vissza, ha az éppen elindult, és _üres_ modellel rendelkezik, vagy ha alaphelyzetbe állítja a személyre szabott hurkot, és a modell még mindig a **modell frissítési gyakorisági** időszakán belül van.

Az új frissítési időszak megkezdése után a rendszer a frissített modellt használja, és megtekinti a valószínűségek változását.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>A tanulási ciklus tanulást folytatott, de úgy tűnik, hogy többé nem tanul, és a rangsor eredményének minősége nem jó. Mit tegyek?

* Győződjön meg arról, hogy végrehajtotta a kiértékelést, és alkalmazta az adott személyre szabott erőforrás (learning loop) Azure Portalban.
* Győződjön meg arról, hogy az összes jutalom el lesz küldve a jutalmazási API-n keresztül, és dolgozza fel.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Hogyan tudja, hogy a tanulási hurok rendszeresen frissül, és az adataik pontszámára szolgál?

Megtalálhatja a modell utolsó frissítésének időpontját a Azure Portal **modell-és tanulási beállítások** lapján. Ha egy régi időbélyeg jelenik meg, akkor valószínű, hogy nem küldi el a rangsort és a Jutalmas hívásokat. Ha a szolgáltatás nem rendelkezik bejövő adattal, nem frissíti a tanulást. Ha úgy látja, hogy a tanulási hurok nem frissül elég gyakran, akkor szerkesztheti a hurok **modell frissítési gyakoriságát**.


## <a name="offline-evaluations"></a>Offline értékelések

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>Az offline értékelés funkciójának fontossága egy hosszú listát ad vissza, amely több száz vagy több ezer elemet tartalmaz. Mi történt?

Ez általában az időbélyegek, a felhasználói azonosítók vagy a szolgáltatásban elküldhető egyéb részletes funkciók miatt fordul elő.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Létrehoztam egy offline kiértékelést, és szinte azonnal sikeres volt. Miért? Nem látok eredményt?

Az offline értékelés a betanított modell adatait használja az adott időszak eseményeiből. Ha a kiértékelés kezdési és befejezési időpontja közötti időszakban nem küldött semmilyen adatmennyiséget, akkor az eredmények nélkül fejeződik be. Küldjön be egy új offline értékelést egy olyan időtartomány kiválasztásával, amelyben a rendszer elküldte a személyre szabott eseményeket.

## <a name="learning-policy"></a>Tanulási szabályzat

### <a name="how-do-i-import-a-learning-policy"></a>Hogyan importálni egy képzési szabályzatot?

További információ a [tanulási szabályzatokkal kapcsolatos fogalmakról](concept-active-learning.md#understand-learning-policy-settings) , valamint az új képzési szabályzatok [alkalmazásáról](how-to-learning-policy.md) . Ha nem szeretne kiválasztani egy képzési szabályzatot, az [Offline értékelés](how-to-offline-evaluation.md) használatával javaslatot tehet a tanulási szabályzatra az aktuális események alapján.


## <a name="security"></a>Biztonság

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>A hurokhoz tartozó API-kulcsot feltörték. Mit tegyek?

Ha az ügyfeleket a másik kulcs használatára szeretné felcserélni, újra létrehozhat egy kulcsot. A két kulcs lehetővé teszi, hogy a kulcsot lusta módon propagálja anélkül, hogy állásidőt kellene leállítania. Azt javasoljuk, hogy ezt egy rendszeres ciklusban biztonsági intézkedésként hajtsa végre.


## <a name="next-steps"></a>Következő lépések

[A modell frissítési gyakoriságának konfigurálása](how-to-settings.md#model-update-frequency)