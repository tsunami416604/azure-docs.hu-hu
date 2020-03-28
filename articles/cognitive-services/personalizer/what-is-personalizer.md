---
title: Mi a Personalizer?
titleSuffix: Azure Cognitive Services
description: A Personalizer egy felhőalapú API-szolgáltatás, amely lehetővé teszi, hogy a legjobb élményt nyújtsa a felhasználóknak, tanulva a valós idejű viselkedésükből.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: 850ab0ee89ee167886d8747a0c721bb643529e14
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052072"
---
# <a name="what-is-personalizer"></a>Mi a Personalizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Personalizer egy felhőalapú API-szolgáltatás, amely segít az ügyfélalkalmazásnak kiválasztani az egyes felhasználók számára a legjobb, egyetlen _tartalomelemet._ A szolgáltatás a tartalomelemek közül a legjobb elemet választja ki a tartalommal és a környezetről megadott kollektív valós idejű információk alapján.

Miután bemutatja a tartalomelemet a felhasználónak, a rendszer figyeli a felhasználók viselkedését, és visszaküldi a jutalompontszámot a Personalizer-nek, hogy javítsa a legjobb tartalom kiválasztását a kapott környezeti információk alapján.

**A tartalom** bármilyen információegység lehet, például szöveg, képek, URL-ek vagy e-mailek, amelyek közül választani szeretne, hogy megjelenjen a felhasználónak.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hogyan választja ki a Personalizer a legjobb tartalomelemet?

Personalizer használ **megerősítése tanulás** kiválasztani a legjobb elem (_akció_) alapján a kollektív viselkedés és a jutalom pontszámok minden felhasználó számára. A műveletek azok a tartalomelemek, például hírcikkek, konkrét filmek vagy termékek, amelyek közül választhat.

A **Rang** hívás veszi a művelet elem, valamint a funkciók a művelet, és a környezeti funkciók, hogy válassza ki a felső művelet elem:

* **Funkciókkal rendelkező műveletek** – az egyes elemekre jellemző funkciókkal rendelkező tartalomelemek
* **Környezetfunkciók** – a felhasználók, a környezetük vagy a környezetük jellemzői az alkalmazás használata során

A Ranghívás azt az azonosítót adja vissza, amelynek a __tartalomelem, művelet__, amelyet meg jelenít a felhasználónak a **Jutalomművelet azonosítója** mezőben.
A felhasználó nak megjelenített __művelet__ gépi tanulási modellekkel kerül kiválasztásra, és megpróbálja maximalizálni a jutalmak teljes összegét az idő múlásával.

Több példa forgatókönyvek a következők:

|Tartalomtípus|**Műveletek (funkciókkal)**|**Környezet jellemzői**|Visszaadott jutalomművelet azonosítója<br>(a tartalom megjelenítése)|
|--|--|--|--|
|Hírek listája|a. `The president...`(nemzeti, politikai, [szöveg])<br>b. `Premier League ...`(globális, sport, [szöveg, kép, videó])<br> c. `Hurricane in the ...`(regionális, időjárás, [szöveg,kép]|Az eszközhírek a következőből olvashatók:<br>Hónap vagy szezon<br>|A`The president...`|
|Filmek listája|1. `Star Wars` (1977, [akció, kaland, fantázia], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentumfilm, sport], Steve James<br>3. `Casablanca` (1942, [romantika, dráma, háború], Michael Curtiz)|Az eszközfilmet a<br>képernyő mérete<br>A felhasználó típusa<br>|3.`Casablanca`|
|Termékek listája|i. `Product A`(3 kg, $$$$, szállít 24 órán belül)<br>ii. `Product B`(20 kg, $$, 2 hetes szállítás vámhatósággal)<br>iii. `Product C`(3 kg, $$$, szállítás 48 óra alatt)|Az eszközvásárlás ta-<br>A felhasználó kiadási szintje<br>Hónap vagy szezon|ii. `Product B`|

Personalizer használt megerősítése tanulás válassza ki az egyetlen legjobb intézkedés, az úgynevezett _jutalom action ID_, kombinációja alapján:
* Betanított modell – a Personalizer szolgáltatás által kapott múltbeli információk
* Aktuális adatok – Egyedi műveletek funkciókkal és környezetfunkciókkal

## <a name="when-to-call-personalizer"></a>Mikor hívja a Personalizer-t?

A **Personalizer's Rank** [API-t](https://go.microsoft.com/fwlink/?linkid=2092082) _minden alkalommal, amikor_ tartalmat mutat be, valós időben. Ezt **eseménynek**nevezzük , _amelyet eseményazonosítóval jegyeznek_fel.

A Personalizer **Reward** [API-ja](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) valós időben vagy késleltetett módon hívható, hogy jobban illeszkedjen az infrastruktúrához. A jutalom pontszámot az üzleti igényeid alapján határozhatod meg. Ez lehet egyetlen érték, például 1 a jó, és 0 a rossz, vagy egy számot hozott létre egy algoritmust hoz létre figyelembe véve az üzleti célok és metrikák.

## <a name="personalizer-content-requirements"></a>A személyre szabásra vonatkozó tartalomra vonatkozó követelmények

A Personalizer használata a tartalom esetén:

* Korlátozott számú elem (max. ~50) közül választhat. Ha nagyobb listával rendelkezik, [használjon javaslati motort](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) a lista 50 elemre való csökkentéséhez.
* A kívánt tartalmat leíró információkat tartalmazza: a funkciókkal és a _környezettel rendelkező_ _műveleteket._
* Legalább ~1k/nap tartalommal kapcsolatos eseményeket tartalmaz ahhoz, hogy a Personalizer hatékony legyen. Ha a Personalizer nem kapja meg a szükséges minimális forgalmat, a szolgáltatás hosszabb időt vesz igénybe az egyetlen legjobb tartalomelem meghatározásához.

Mivel a Personalizer a kollektív információkat közel valós időben használja fel a legjobb tartalomtartalom-tétel visszaküldésére, a szolgáltatás nem:
* Felhasználói profiladatainak megőrzése és kezelése
* Az egyes felhasználók preferenciáinak vagy előzményeinek naplózása
* Tisztított és címkézett tartalom megkövetelése

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Hogyan tervezzünk és valósítsunk meg Personalizer-t az ügyfélalkalmazáshoz?

1. [Tartalom,](concepts-features.md) **_műveletek_** és **_környezet_** tervezése és tervezése. Határozza meg a jutalom algoritmus a **_jutalom_** pontszámot.
1. Minden [létrehozott személyre szabó-erőforrás](how-to-settings.md) 1 tanulási huroknak minősül. A hurok megkapja mind a Rang és jutalom hívások, hogy a tartalom vagy a felhasználói élményt.
1. Add Personalizer a webhely vagy a tartalomrendszer:
    1. Adjon hozzá **egy Rangsorolási** hívást a Personalizer-hoz az alkalmazásban, a webhelyben vagy a rendszerben, hogy meghatározza a legjobb, egyetlen _tartalomelemet,_ mielőtt a tartalom megjelenik a felhasználónak.
    1. A legjobb, egyetlen _tartalomelem_ megjelenítése, amely a visszaadott _jutalomművelet-azonosító_a felhasználó számára.
    1. Az _algoritmus_ alkalmazása a felhasználó működésével kapcsolatos összegyűjtött információkra a **jutalompontszám** meghatározásához, például:

        |Viselkedés|Számított jutalompontszám|
        |--|--|
        |A felhasználó a legjobb, egyetlen _tartalomelemet_ választotta ki (jutalomművelet-azonosító)|**1**|
        |A felhasználó más tartalmat választott ki|**0**|
        |Felhasználó szüneteltetve, határozatlanul görgetve, mielőtt kiválasztja a legjobb, egyetlen _tartalom_ elemet (jutalomművelet-azonosító)|**0.5**|

    1. **Jutalomhívás** hozzáadása 0 és 1 közötti jutalompontszámot küldve
        * Közvetlenül a tartalom megjelenítése után
        * Vagy valamikor később egy offline rendszerben
    1. [Értékelje ki a ciklust](concepts-offline-evaluation.md) egy offline kiértékeléssel egy használati időszak után. Az offline kiértékelés lehetővé teszi a Personalizer szolgáltatás hatékonyságának tesztelését és értékelését anélkül, hogy megváltoztatná a kódot, vagy befolyásolná a felhasználói élményt.

## <a name="next-steps"></a>További lépések


* [A Personalizer működése](how-personalizer-works.md)
* [Mi a megerősítés tanulás?](concepts-reinforcement-learning.md)
* [További információ a Rangsorolás kérés funkcióiról és műveleteiről](concepts-features.md)
* [További információ a jutalomkérelem pontszámának meghatározásáról](concept-rewards.md)
* [Rövid útmutatók](sdk-learning-loop.md)
* [Bemutató](tutorial-use-azure-notebook-generate-loop-data.md)
* [Az interaktív bemutató használata](https://personalizationdemo.azurewebsites.net/)
