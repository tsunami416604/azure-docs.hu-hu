---
title: Irányítópult - Nyelvi ismeretek - LUIS
titleSuffix: Azure Cognitive Services
description: A leképezések és entitások javítása a betanított alkalmazás irányítópultjával. Az irányítópult megjeleníti az alkalmazás általános adatait, a kiképezések kiemelésével, amelyeket ki kell javítani.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888203"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Az irányítópult használata az alkalmazás fejlesztéséhez

Keresse meg és javítsa ki a betanított alkalmazás szándékaival kapcsolatos problémákat, amikor példakimondott szövegeket használ. Az irányítópult megjeleníti az alkalmazás általános adatait, a kiképezések kiemelésével, amelyeket ki kell javítani. 

Tekintse át az irányítópult-elemzés egy iteratív folyamat, ismételje meg a modell módosítása és javítása során.

Ez az oldal nem rendelkezik releváns elemzés sel olyan alkalmazások, amelyek nem rendelkeznek példa utterances a szándékok, más néven _minta-csak_ alkalmazások. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Milyen problémákat lehet kijavítani az irányítópultról?

Az irányítópulton a következő három problémával foglalkozik:

|Probléma|Diagram színe|Magyarázat|
|--|--|--|
|Adategyensúly-hiánytalanság|-|Ez akkor fordul elő, ha a példa utterances mennyisége jelentősen eltér. Minden szándékok kell _nagyjából_ azonos számú példa utterances - kivéve a Nincs szándék. Az alkalmazás ban a kimondott szöveg teljes mennyiségének 10–15%-a csak 10%-15%-kal kell rendelkeznie.<br><br> Ha az adatok kiegyensúlyozatlan, de a szándék pontossága meghaladja a bizonyos küszöbértéket, ez a kiegyensúlyozatlanság nem jelent problémát.<br><br>**Kezdje ezzel a problémával - ez lehet a kiváltó oka a többi kérdésnek.**|
|Nem egyértelmű előrejelzések|Narancssárga|Ez akkor fordul elő, ha a felső szándék és a következő szándék pontszámok elég közel vannak ahhoz, hogy azok tükröza a következő betanítás, [negatív mintavételi](luis-how-to-train.md#train-with-all-data) vagy több példa utterances hozzá a szándékhoz adott. |
|Helytelen előrejelzések|Vörös|Ez akkor fordul elő, ha egy példa utterance nincs előre jelezve a címkézett szándék (a szándék, hogy van).|

A helyes előrejelzéseket a kék szín jelöli.

Az irányítópult megjeleníti ezeket a problémákat, és jelzi, hogy mely leképezéseket érinti, és javaslatot tesz arra, hogy mit kell tennie az alkalmazás fejlesztéséhez. 

## <a name="before-app-is-trained"></a>Az alkalmazás betanítása előtt 

Az alkalmazás betanítása előtt az irányítópult nem tartalmaz javaslatokat a javításokra vonatkozóan. Az alkalmazás betanítása a javaslatok megtekintéséhez.  

## <a name="check-your-publishing-status"></a>A közzétételi állapot ellenőrzése

A **Közzététel állapotkártya** az aktív verzió utolsó közzétételére vonatkozó információkat tartalmaz. 

Ellenőrizze, hogy az aktív verzió az a verzió-e, amelyet javítani szeretne. 

![Az irányítópult megjeleníti az alkalmazás külső szolgáltatásait, a közzétett régiókat és az összesített végpontlekéréseket.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Ez is mutatja a külső szolgáltatások, közzétett régiók és összesített végpont lekérések. 

## <a name="review-training-evaluation"></a>Képzésértékelés áttekintése

Az **Edzés kiértékelő** kártya az alkalmazás általános pontosságának összesített összegzését tartalmazza terület szerint. A pontszám a szándék minőségét jelzi. 

![Az Edzés értékelési kártya tartalmazza az alkalmazás általános pontosságára vonatkozó első információt.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

A diagram a helyesen előre jelzett leképezéseket és a különböző színű problémás területeket mutatja. Ahogy javítja az alkalmazást a javaslatokkal, ez a pontszám nő. 

A javasolt javításokat problématípus választja el egymástól, és ezek a legfontosabbak az alkalmazás számára. Ha szeretné áttekinteni és kijavítani a szándékosan i problémákat, használja a **[szándékok hibákkal](#intents-with-errors)** kártyát az oldal alján. 

Minden problémás területnek vannak kiképezései, amelyeket ki kell javítani. Amikor kiválasztja a szándék nevét, a **szándék** lap megnyílik egy szűrőt alkalmazott a kimondott szövegeket. Ez a szűrő lehetővé teszi, hogy összpontosítson a kimondott, amelyek a problémát okozó.

### <a name="compare-changes-across-versions"></a>Változások összehasonlítása a verziók között

Hozzon létre egy új verziót, mielőtt módosítana az alkalmazást. Az új verzióban hajtsa végre a szándék példautterances javasolt módosításait, majd újra betanítása. Az Irányítópult lap **Oktatópróbakártyáján** a **Betanított verzióról a módosítás megjelenítése** a módosítások összehasonlításához használható. 

![Változások összehasonlítása a verziók között](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Verzió javítása példakimondottszövegek hozzáadásával vagy szerkesztésével és átképzéssel

Az alkalmazás javításának elsődleges módja a példakimondott szöveg hozzáadása vagy szerkesztése és újratanítása. Az új vagy módosított kimondott szövegnek követnie kell a [különböző kimondott szövegre vonatkozó irányelveket.](luis-concept-utterance.md)

A példakimondott szövegeket olyan személynek kell elvégeznie, aki:

* nagyfokú megértése, hogy milyen kimondott szövegek vannak a különböző szándékok.
* tudja, hogyan utterances az egyik szándék lehet összetéveszteni egy másik szándékkal.
* el tudja dönteni, hogy két szándékot, amelyekgyakran összekeverednek egymással, egyetlen szándékba kell-e összecsukni. Ebben az esetben a különböző adatokat entitásokkal együtt kell kihúzni.

### <a name="patterns-and-phrase-lists"></a>Minták és kifejezéslisták

Az elemzési oldal nem jelzi, hogy mikor használjon [mintákat](luis-concept-patterns.md) vagy [kifejezéslistákat.](luis-concept-feature.md) Ha hozzáadja őket, segíthet a helytelen vagy nem egyértelmű előrejelzésekben, de nem segít az adatok kiegyensúlyozatlanságában. 

### <a name="review-data-imbalance"></a>Adategyensúly-hiánytalanság áttekintése

Kezdje ezzel a problémával - ez lehet a kiváltó oka a többi kérdésnek.

Az **adatkiegyezési** szándékok listája olyan leképezéseket jelenít meg, amelyek további kimondott szöveget igényelnek az adategyensúly-hiánytalanság kijavítása érdekében. 

**A probléma megoldása:**

* Adjon hozzá további kimondott szöveget a szándékhoz, majd képezze be újra. 

Ne adjon hozzá utterances a Nincs szándék, kivéve, ha ez javasolt az irányítópulton.

> [!Tip]
> Használja a harmadik szakasz az oldalon, **utterances egy szándékot** a **Utterances (szám)** beállítás, egy gyors vizuális útmutató, amely szándékok további utterances.  
    ![Használja a "Utterances (szám)" adategyensúly-kihaszkodások megkereséséhez.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Helytelen előrejelzések áttekintése

A **helytelen előrejelzési** leképezési lista olyan leképezéseket jelenít meg, amelyek kimondott szöveget tartalmaznak, amelyek példáként szolgálnak egy adott szándékhoz, de különböző szándékok előre jelzettek. 

**A probléma megoldása:**

* A kimondott szövegeket a szándékra vonatkozóan pontosabban szerkeszti, és újra betanítja.
* Szándékok egyesítése, ha a kimondott szöveg túl szorosan igazodik, és újra betanítása.

### <a name="review-unclear-predictions"></a>Nem egyértelmű előrejelzések áttekintése

A **nem egyértelmű előrejelzési** szándék lista olyan kimondott szándékokat jelenít meg, amelyek előrejelzési pontszámokkal nem elég messze vannak a legközelebbi riválistól, hogy az utterance (kifejezés) felső szándéka a [negatív mintavétel miatt](luis-how-to-train.md#train-with-all-data)változhat.

**A probléma megoldása**;

* A kimondott szövegeket a szándékra vonatkozóan pontosabban szerkeszti, és újra betanítja.
* Szándékok egyesítése, ha a kimondott szöveg túl szorosan igazodik, és újra betanítása.

## <a name="utterances-per-intent"></a>Kimondott szöveg szándékonként

Ez a kártya az alkalmazás általános állapotát jeleníti meg a leképezések között. A szándékok javítása és az újratanítás közben továbbra is olvassa el a kártya áttekintését a problémákmiatt.

A következő diagram egy kiegyensúlyozott alkalmazást mutat be, amelyszinte nem oldható meg problémákkal.

![A következő diagram egy kiegyensúlyozott alkalmazást mutat be, amelyszinte nem oldható meg problémákkal.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

A következő diagramon egy rosszul kiegyensúlyozott alkalmazás látható, amelyszámos problémát megold.

![A következő diagram egy kiegyensúlyozott alkalmazást mutat be, amelyszinte nem oldható meg problémákkal.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Vigye az egérmutatót az egyes szándékok sávjára a szándékkal kapcsolatos információk leképezéséhez. 

![A következő diagram egy kiegyensúlyozott alkalmazást mutat be, amelyszinte nem oldható meg problémákkal.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

A **Rendezés a szerint** funkcióval rendezheti a szándékokat problématípus szerint, így a probléma legproblémásabb leképezésére összpontosíthat. 

## <a name="intents-with-errors"></a>Leképezések hibás

Ez a kártya lehetővé teszi, hogy egy adott szándékkal kapcsolatos problémákat tekintse át. A kártya alapértelmezett nézete a legproblémásabb szándék, így tudja, hogy hova összpontosítsa erőfeszítéseit.

![A szándékok hibákkal kártya lehetővé teszi, hogy tekintse át a problémákat egy adott szándékkal. A kártya szűrve van a legproblémásabb szándékok, alapértelmezés szerint, így tudja, hol kell összpontosítani a erőfeszítéseket.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

A legfelső fánkdiagram a három problématípusban a szándékkal kapcsolatos problémákat mutatja. Ha problémák merülnek fel a három problématípusban, minden típusnak saját diagramja van az alábbiakban, a rivális szándékokkal együtt. 

### <a name="filter-intents-by-issue-and-percentage"></a>Leképezések szűrése probléma és százalék szerint

Ez a szakasz a kártya lehetővé teszi, hogy megtalálja a példa utterances, amelyek kívül esnek a hibaküszöbértéket. Ideális esetben azt szeretné, hogy a helyes előrejelzések jelentősek legyenek. Ez a százalék az üzleti és az ügyfél által vezérelt. 

Határozza meg a küszöbérték százalékokat, hogy ön kényelmes a vállalkozás. 

A szűrő lehetővé teszi, hogy konkrét problémával rendelkező szándékokat találjon:

|Szűrés|Javasolt százalék|Cél|
|--|--|--|
|A legproblematikusabb szándékok|-|**Kezdje itt** – A kimondott szöveg ek javítása ebben a szándékkal javítja az alkalmazást, mint más javítások.|
|Helyes előrejelzések az alábbi|60%|Ez a kijelölt szándékban lévő kimondott szövegek százalékos aránya, amelyek helyesek, de a küszöbérték alatti megbízhatósági pontszámmal rendelkeznek. |
|Nem egyértelmű előrejelzések felett|15%|Ez a kijelölt szándék ban a legközelebbi rivális szándékkal összetévesztett kimondott szöveg százaléka.|
|Helytelen előrejelzések felett|15%|Ez a kijelölt leképezésben helytelenül előre jelzett kimondott szöveg százaléka. |

### <a name="correct-prediction-threshold"></a>Helyes előrejelzési küszöbérték

Mi az a magabiztos előrejelzés bizalom pontszám az Ön számára? Az alkalmazásfejlesztés kezdetén 60% lehet a cél. Használja az **alábbi helyes előrejelzések** et a 60%-os százalékkal a kiválasztott szándékban lévő kimondott szövegek megkereséséhez, amelyeket ki kell javítani.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Nem egyértelmű vagy helytelen előrejelzési küszöbérték

Ez a két szűrő lehetővé teszi, hogy a kiválasztott szándéka a küszöbértéken túl imitátorokat keressen. Ezt a két százalékot hibaszázalékként is fellehet gondolni. Ha az előrejelzések 10-15%-os hibaarányával elégedett, állítsa a szűrő küszöbértékét 15%-ra, hogy megtalálja az összes kimondott szöveget ezen érték felett. 

## <a name="next-steps"></a>További lépések

* [Azure-erőforrások kezelése](luis-how-to-azure-subscription.md)
