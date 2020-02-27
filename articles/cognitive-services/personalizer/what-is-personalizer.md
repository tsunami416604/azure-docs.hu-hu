---
title: Mi a Personalizer?
titleSuffix: Azure Cognitive Services
description: A személyre szabás egy felhőalapú API-szolgáltatás, amely lehetővé teszi a felhasználók számára a legjobb felhasználói élmény kiválasztását, a valós idejű viselkedésük megismerését.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: bf0710ebef21226d8d8582a920d64027bb015d34
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622719"
---
# <a name="what-is-personalizer"></a>Mi a Personalizer?

Az Azure megszemélyesítő egy felhőalapú API-szolgáltatás, amely segít az ügyfélalkalmazás számára kiválasztani a legjobb, egyetlen _tartalmi_ elemet az egyes felhasználók megjelenítéséhez. A szolgáltatás kiválasztja a legjobb elemet, a tartalmi elemektől a tartalommal és környezettel kapcsolatban megadott kollektív valós idejű információk alapján.

Miután megjelentette a tartalmi elemet a felhasználó számára, a rendszer figyeli a felhasználói viselkedést, és visszaküldi a jutalom pontszámát a személyre, hogy javítsa a legjobb tartalmat a kapott környezeti információk alapján.

A **tartalom** bármely olyan információ lehet, például szöveg, kép, URL-cím vagy e-mail-cím, amelyet ki szeretne választani a felhasználó számára.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hogyan választja ki a személyre szabott tartalmi elemet?

A személyre szabás a **megerősítő tanulás** segítségével kiválasztja a legjobb elemet (_művelet_) a kollektív viselkedés és a jutalom pontszámok alapján az összes felhasználó számára. A műveletek a tartalmi elemek, például hírek, adott mozgóképek vagy termékek, amelyek közül választhatnak.

A **rangsorban** hívja meg a műveleti elemet, valamint a művelet funkcióit és a környezeti funkciókat, hogy kiválassza a felső műveleti elemet:

* **Funkciókkal rendelkező műveletek** – az egyes elemekhez tartozó funkciókat tartalmazó tartalmi elemek
* **Környezeti funkciók** – a felhasználók, a környezetük vagy a környezetük funkciói az alkalmazás használatakor

A rangsor hívása azt az azonosítót adja vissza, amely a felhasználó számára megjelenített tartalmi __elem, a__ **jutalmazási művelet azonosítója** mezőben található.
A felhasználó számára megjelenő __művelet__ gépi tanulási modellel van kiválasztva, és a teljes jutalom összegét az idő múlásával igyekszik maximalizálni.

Számos példa a következőkre:

|Tartalom típusa|**Műveletek (funkciókkal)**|**Környezeti funkciók**|Visszaadott jutalom műveleti azonosítója<br>(a tartalom megjelenítése)|
|--|--|--|--|
|Hírek listája|a. `The president...` (országos, politikai, [text])<br>b. `Premier League ...` (globális, sport, [szöveg, rendszerkép, videó])<br> c. `Hurricane in the ...` (regionális, időjárás, [szöveg, rendszerkép]|Az eszköz híreinek olvasása<br>Hónap vagy idény<br>|egy `The president...`|
|Filmek listája|1. `Star Wars` (1977, [művelet, kaland, fantázia], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentumfilm, sport], Steve James<br>3. `Casablanca` (1942, [Romance, dráma, War], Michael Kertész)|A rendszer figyeli az eszköz mozgóképét<br>képernyő mérete<br>Felhasználó típusa<br>|3. `Casablanca`|
|Termékek listája|i. `Product A` (3 kg, $ $ $ $, 24 órás kézbesítés)<br>ii. `Product B` (20 kg, $ $, 2 hetes szállítás vámmal)<br>iii. `Product C` (3 kg, $ $ $, kézbesítés 48 órán belül)|Az eszköz vásárlásának beolvasása<br>Felhasználói költségkeret<br>Hónap vagy idény|ii. `Product B`|

A személyre szabott megerősítő tanulás segítségével kiválaszthatja az egyetlen legjobb műveletet, amely a _jutalmazási művelet azonosítója_, a következők kombinációja alapján:
* Betanított modell – múltbeli információk a személyre szabott szolgáltatás fogadásáról
* Aktuális adatspecifikus műveletek a funkciókkal és a környezeti funkciókkal

## <a name="when-to-call-personalizer"></a>A személyre szabás meghívása

A személyre szabott **Range** [API](https://go.microsoft.com/fwlink/?linkid=2092082) -t _minden alkalommal, amikor_ valós időben jelennek meg a tartalomban. Ez az **esemény egy esemény-** _azonosítóval_megjegyezve.

A személyre szabott **jutalmazási** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) valós időben hívható meg, vagy késleltethető, hogy jobban illeszkedjen az infrastruktúrához. Az üzleti igények alapján határozza meg a jutalom pontszámát. Ez lehet egyetlen érték, például 1, jó, 0 vagy rossz, vagy egy szám, amelyet egy, az üzleti célok és mérőszámok alapján létrehozott algoritmus állít elő.

## <a name="personalizer-content-requirements"></a>Személyre szabott tartalomra vonatkozó követelmények

A személyre szabott tartalom használata:

* Korlátozott számú elemet tartalmaz (legfeljebb ~ 50), amelyből kiválaszthatja a következőt:. Ha nagyobb listával rendelkezik, az [ajánlási motor használatával](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) csökkentse a listát 50 elemre.
* A rangsorolni kívánt tartalmat leíró információkkal rendelkezik: a _funkciók és a_ _környezet funkcióival_kapcsolatos műveletek.
* Legalább ~ 1k/nap tartalommal kapcsolatos eseményt biztosít a személyre szabáshoz. Ha a személyre szabott nem kapja meg a minimálisan szükséges forgalmat, a szolgáltatás továbbra is megtarthatja az egyetlen legmegfelelőbb tartalmi elemet.

Mivel a személyre szabott, közel valós időben a személyre szabott adatokat használ, a szolgáltatás nem a következő:
* Felhasználói profil adatainak fenntartása és kezelése
* Egyéni felhasználói beállítások vagy előzmények naplózása
* Megtisztított és címkézett tartalom megkövetelése

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Az ügyfélalkalmazás személyre szabásának megtervezése és implementálása

1. [Tervezze](concepts-features.md) meg és tervezze meg a tartalmat, a **_műveleteket_** és a **_környezetet_** . Határozza meg **_a jutalmas pontszámhoz_** tartozó jutalmazási algoritmust.
1. Az Ön által létrehozott minden [személyre szabott erőforrás](how-to-settings.md) 1 tanulási ciklusnak minősül. A hurok az adott tartalomhoz vagy felhasználói élményhez tartozó rang és jutalmazási hívásokat is megkapja.
1. Személyre szabás hozzáadása a webhelyhez vagy a tartalmi rendszeren:
    1. Az alkalmazásban, a webhelyen vagy a rendszeren testreszabhatja a személyre szabási **hívást,** hogy meghatározza a legjobb, egyetlen _tartalmi_ elemet, mielőtt a tartalom megjelenik a felhasználó számára.
    1. Jelenítse meg a legjobb, egyetlen _tartalmi_ tételt, amely a visszaadott _jutalom műveleti azonosítója_a felhasználónak.
    1. Alkalmazzon _algoritmust_ a felhasználó működésével kapcsolatos adatok gyűjtésére a **jutalom** pontszámának megállapításához, például:

        |Viselkedés|Számított jutalom pontszáma|
        |--|--|
        |A felhasználó a legjobb, egyetlen _tartalmi_ elemet (jutalmazási művelet azonosítója) választotta|**1**|
        |A felhasználó által kiválasztott egyéb tartalom|**0**|
        |A felhasználó szüneteltetve, a nem határozott módon görgetve, mielőtt kiválasztja a legjobb, egyetlen _tartalmi_ elemet (jutalmazási művelet azonosítója)|**0,5**|

    1. **Jutalom-hívás küldése** 0 és 1 közötti jutalom pontszámának megadásához
        * A tartalom megjelenítése után azonnal
        * Vagy valamivel később egy offline rendszeren
    1. Egy használati időszak után [értékelje ki a hurkot](concepts-offline-evaluation.md) offline kiértékeléssel. Az offline kiértékelés lehetővé teszi a személyre szabott szolgáltatás hatékonyságának tesztelését és értékelését a kód módosítása vagy a felhasználói élmény befolyásolása nélkül.

## <a name="next-steps"></a>Következő lépések


* [A megszemélyesítő működése](how-personalizer-works.md)
* [Mi a megerősítő tanulás?](concepts-reinforcement-learning.md)
* [További információ a Rank kérelem szolgáltatásairól és műveleteiről](concepts-features.md)
* [Tudnivalók a jutalmazási kérelem pontszámának meghatározásáról](concept-rewards.md)
* [Rövid útmutatók](sdk-learning-loop.md)
* [Oktatóanyag](tutorial-use-azure-notebook-generate-loop-data.md)
* [Az interaktív bemutató használata](https://personalizationdemo.azurewebsites.net/)
