---
title: Hibaelhárítás – személyre szabás
description: Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 432b33243bdb38cf359d4fea1a336500eb244464
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650520"
---
# <a name="personalizer-troubleshooting"></a>Személyre szabás – hibaelhárítás

Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.

## <a name="transaction-errors"></a>Tranzakciós hibák

<details>
<summary><b>HTTP 429 (túl sok kérés) választ kapok a szolgáltatástól. Mit tehetek?</b></summary>

**Válasz**: Ha a személyre szabott példány létrehozásakor ingyenes árat adott meg, a rendszer kvótát korlátoz az engedélyezett rangsorolt kérelmek számára. Tekintse át a Range API API-hívások arányát (a személyre szabott erőforráshoz tartozó Azure Portal mérőszámok ablaktábláján), és állítsa be az árképzési szintet (az árképzési szint ablaktáblán), ha a hívási kötet a kiválasztott árképzési szintnél nagyobb küszöbértéket meghaladóan nő.

</details>

<details>
<summary><b>5xx-hibát kapok a Rank vagy a jutalmazási API-k esetében. Mit tegyek?</b></summary>

**Válasz**: ezeknek a problémáknak transzparensnek kell lenniük. Ha folytatják, forduljon az ügyfélszolgálathoz az **új támogatási kérelem** kiválasztásával a **támogatás + hibaelhárítás** szakaszban, a személyre szabott erőforrás Azure Portal.

</details>

## <a name="learning-loop"></a>Tanulási hurok

<details>
<summary>
<b>A tanulási ciklus nem éri el a rendszer 100%-os egyeztetését személyre szabás nélkül. Hogyan javítsa ezt?</b></summary>

**Válasz**: az az oka, hogy nem éri el a célt a tanulási ciklusban:
* Nincs elég funkció a Rank API-hívással
* Az elküldött funkciók hibái – például a nem összesített funkciók adatainak, például az időbélyegek az API-ba való küldésének
* Hurkok feldolgozásával kapcsolatos hibák – például a jutalmazási adatok küldése az események jutalmazására

A javításhoz módosítania kell a feldolgozást a hurokra küldött funkciók módosításával, vagy győződjön meg arról, hogy a jutalom a rangsor válaszának megfelelő kiértékelése.

</details>

<details>
<summary>
<b>Úgy tűnik, hogy a tanulási hurok nem tanul. Hogyan javítsa ezt?</b></summary>

**Válasz**: a tanulási huroknak néhány ezer jutalmazási hívásra van szüksége, mielőtt rangsorolja a rangsorolási hívásokat.

Ha nem biztos abban, hogy hogyan viselkedik a tanulási hurok jelenleg, futtasson egy [Offline értékelést](concepts-offline-evaluation.md), és alkalmazza a javított tanulási szabályzatot.

</details>

<details>
<summary><b>A rangsorban az összes elemre vonatkozó összes valószínűségi eredményt megtartom. Hogyan ismeri a személyre szabott tanulást?</b></summary>

**Válasz**: a személyre szabott eredmény ugyanazokat a valószínűségeket adja vissza a rangsor API-eredményekben, amikor az éppen elindult, és _üres_ modellel rendelkezik, vagy ha alaphelyzetbe állítja a személyre szabott hurkot, és a modell még mindig a **modell frissítési gyakorisági** időszakán belül van.

Az új frissítési időszak megkezdése után a rendszer a frissített modellt használja, és megtekinti a valószínűségek változását.

</details>

<details>
<summary><b>A tanulási ciklus tanulást folytatott, de úgy tűnik, hogy többé nem tanul, és a rangsor eredményének minősége nem jó. Mit tegyek?</b></summary>

**Válasz**:
* Győződjön meg arról, hogy végrehajtotta a kiértékelést, és alkalmazta az adott személyre szabott erőforrás (learning loop) Azure Portalban.
* Győződjön meg arról, hogy az összes jutalom el lesz küldve a jutalmazási API-n keresztül, és dolgozza fel.

</details>


<details>
<summary><b>Hogyan tudja, hogy a tanulási hurok rendszeresen frissül, és az adataik pontszámára szolgál?</b></summary>

**Válasz**: megtalálhatja a modell utolsó frissítésének időpontját a Azure Portal **modell-és tanulási beállítások** lapján. Ha egy régi időbélyeg jelenik meg, akkor valószínű, hogy nem küldi el a rangsort és a Jutalmas hívásokat. Ha a szolgáltatás nem rendelkezik bejövő adattal, nem frissíti a tanulást. Ha úgy látja, hogy a tanulási hurok nem frissül elég gyakran, akkor szerkesztheti a hurok **modell frissítési gyakoriságát**.

</details>

## <a name="offline-evaluations"></a>Offline értékelések

<details>
<summary><b>Az offline értékelés funkciójának fontossága egy hosszú listát ad vissza, amely több száz vagy több ezer elemet tartalmaz. mi történt?</b></summary>

**Válasz**: ez általában az időbélyegek, a felhasználói azonosítók vagy a szolgáltatásban elküldhető egyéb részletes funkciók miatt fordul elő.

</details>

<details>
<summary><b>Létrehoztam egy offline kiértékelést, és szinte azonnal sikeres volt. Miért van ez? Nem látok eredményt?</b></summary>

**Válasz**: az offline értékelés a betanított modell adatait használja az adott időszak eseményeiből. Ha a kiértékelés kezdési és befejezési időpontja közötti időszakban nem küldött semmilyen adatmennyiséget, akkor az eredmények nélkül fejeződik be. Küldjön be egy új offline értékelést egy olyan időtartomány kiválasztásával, amelyben a rendszer elküldte a személyre szabott eseményeket.

</details>


## <a name="learning-policy"></a>Tanulási szabályzat

<details>
<summary><b>Hogyan importálni egy képzési szabályzatot?</b></summary>

**Válasz**: További információ a [tanulási szabályzatokkal kapcsolatos fogalmakról](concept-active-learning.md#understand-learning-policy-settings) , valamint az új képzési szabályzatok [alkalmazásáról](how-to-manage-model.md) . Ha nem szeretne kiválasztani egy képzési szabályzatot, az [Offline értékelés](how-to-offline-evaluation.md) használatával javaslatot tehet a tanulási szabályzatra az aktuális események alapján.

</details>

## <a name="security"></a>Biztonság

<details>
<summary><b>A hurokhoz tartozó API-kulcsot feltörték. Mit tehetek?</b></summary>

**Válasz**: Ha az ügyfeleket a másik kulcs használatára szeretné felcserélni, újra létrehozhat egy kulcsot. A két kulcs lehetővé teszi, hogy a kulcsot lusta módon propagálja anélkül, hogy állásidőt kellene leállítania. Azt javasoljuk, hogy ezt egy rendszeres ciklusban biztonsági intézkedésként hajtsa végre.

</details>

## <a name="next-steps"></a>Következő lépések

[A modell frissítési gyakoriságának konfigurálása](how-to-settings.md#model-update-frequency)