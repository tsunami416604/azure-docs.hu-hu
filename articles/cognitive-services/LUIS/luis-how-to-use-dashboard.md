---
title: Irányítópult – beszédfelismerés
titleSuffix: Azure Cognitive Services
description: Javítsa ki a leképezések az analytics irányítópultot, egy teszi jelentéskészítési eszköz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236941"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Az irányítópult használata az alkalmazás fejlesztéséhez

Megkeresheti és elháríthatja a problémákat a betanított alkalmazásszándékkal rendelkező példa utterances használata esetén. Az irányítópult teljes alkalmazás adatai, a szándék fog vonatkozni, amely javítani kell a legfontosabb jeleníti meg. 

Tekintse át az irányítópult elemzésre többször is módosíthatja, és javíthatja a modellt, iteratív folyamat.

Ezen a lapon nem lesz megfelelő elemző alkalmazások, amelyek nem rendelkeznek példa megszólalásokat a szándék fog vonatkozni, más néven a _csak minta_ alkalmazásokat. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Milyen problémák is rögzíteni kell az irányítópultról?

Az irányítópult a három problémák vannak:

|Probléma|Diagram színe|Magyarázat|
|--|--|--|
|Adatok egyenetlenségének|-|Ez akkor fordul elő, ha a példa kimondott szöveg mennyiségét jelentősen eltér. Rendelkeznie kell az összes leképezések _nagyjából_ példa utterances – kivéve a nincs szándék azonos számú. 10 – 15 % utterances teljes mennyiségét, az alkalmazás csak azt kell rendelkeznie.<br><br> Ha az adatok imbalanced, de a szándék pontossága meghatározott küszöbérték fölött van, ez egyenetlenségének nem verziószámú problémát.<br><br>**Start - probléma az lehet a más problémák eredendő okát.**|
|Nem egyértelmű előrejelzések|Narancssárga|Ez akkor fordul elő, amikor a felső célt és a következő leképezés pontszámok kellően közel, hogy azok előfordulhat, hogy tükrözés a következő képzési miatt [negatív mintavételi](luis-how-to-train.md#train-with-all-data) vagy beszédszándék hozzá további példa kimondott szöveg. |
|Helytelen előrejelzések|Piros|Ez akkor fordul elő, amikor egy példa utterance (kifejezés) nem előre jelzett a címkézett célja (a leképezés van).|

A szín kék megfelelő előrejelzéseket jelöli.

Az irányítópult bemutatja ezeket a problémákat, és jelzi, hogy melyik leképezések érintett, és javasolja, hogy mit kell tennie az alkalmazás javítására. 

## <a name="before-app-is-trained"></a>Mielőtt az alkalmazás be van tanítva 

Mielőtt az alkalmazás betanításához az irányítópult nem tartalmaz javaslatai vannak a javítások. Ezek a javaslatok megtekintéséhez az alkalmazás betanításához.  

## <a name="check-your-publishing-status"></a>A közzétételi állapotának ellenőrzése

A **közzétételi állapota** kártya tudnivalókkal szolgál az aktív verziója a legutóbbi közzétenni. 

Ellenőrizze, hogy az aktív verzió a rögzíteni kívánt verziót. 

![Irányítópult látható alkalmazások külső szolgáltatásokhoz, régiók és közzétett végpont találatok összesítve.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Ez is bármilyen külső szolgáltatásokkal, a közzétett régiók jelennek meg, és összesítve végpont találatok. 

## <a name="review-training-evaluation"></a>Tekintse át a betanítási kiértékelése

A **képzési értékelési** kártya az alkalmazás általános pontossága terület szerint összesített összefoglalását tartalmazza. A pontszám szándék minőségi jelzi. 

![A képzési értékelési kártya tartalmazza az alkalmazás általános pontossága információ első területét.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

A diagram azt jelzi, a megfelelő előre jelzett leképezések és a problémás területek különböző színű. Mivel, javíthatja az alkalmazást, a javaslatokat, a pontszám növekszik. 

A javasolt javítások vannak elválasztva a probléma típusa és az alkalmazás számára a legfontosabb. Ha inkább tekintse át, és kijavíthatja a problémák / szándékkal, használja a **[hibákkal leképezések](#intents-with-errors)** kártya a lap alján. 

Minden egyes probléma terület rendelkezik szándék fog vonatkozni, amelyet javítani kell. A leképezés neve kiválasztásakor a **szándékot** lap, melyen az megcímkézzen alkalmazott szűrőkkel. Ez a szűrő lehetővé teszi, hogy a problémát okozó megcímkézzen összpontosíthat.

### <a name="compare-changes-across-versions"></a>Hasonlítsa össze a különböző verziók

Mielőtt módosítaná az alkalmazás, hozzon létre egy új verziója. Az új verziót, a javasolt a módosításokat a leképezés példa utterances, majd újra be. Az irányítópult-oldalon **képzési értékelési** kártya, használja a **Show módosítás betanított verzióról** összehasonlítani a módosításokat. 

![Hasonlítsa össze a különböző verziók](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Javítsa ki a verzió hozzáadásával vagy példa kimondott szöveg szerkesztését és átképezési

A módszer az alkalmazás kompatibilitását hozzáadása vagy szerkesztése példa kimondott szöveg és újratanítása lesz. Új vagy módosított megcímkézzen kell követniük irányelvek [utterances maximalizálásában](luis-concept-utterance.md).

Példa beszédmódok hozzáadása kell elvégezni valaki ki:

* beszédmódok Mik a különböző leképezések a ismeretekkel magas fokú rendelkezik
* tudni fogja, hogyan utterances egyik célja a lehetséges, hogy tévesztendő össze egy másik leképezés
* Döntse el, ha a két szándék fog vonatkozni, amelyek gyakran egymással összetéveszteni kell összecsukva, az egyetlen célja az entitások kinyerési a különböző adatokat képes

### <a name="patterns-and-phrase-lists"></a>Minták és kifejezések listája

Az elemzési lap nem jelezze, mikor érdemes használni [minták](luis-concept-patterns.md) vagy [listák kifejezés](luis-concept-feature.md). Ha hozzáadja őket, segítségére lehetnek a nem megfelelő vagy nem egyértelmű előrejelzéseket, de nem sokat segít az adatok egyensúlyhiány. 

### <a name="review-data-imbalance"></a>Tekintse át adatokat egyenetlenségének

Start - probléma az lehet a más problémák eredendő okát.

A **adatok egyenetlenségének** szándék lista mutatja azokat, amelyek további utterances kell ahhoz, hogy javítsa ki az adatok egyenetlenségének leképezések. 

**A probléma megoldásához**:

* További beszédmódok hozzáadása a célt, majd újra be. 

Beszédmódok a nincs szándék csak hozzá az irányítópulton, amely ajánlott.

> [!Tip]
> A harmadik szakasz ezen a lapon **kimondott szöveg / szándékot** az a **beszédmódok (szám)** beállítás, gyors vizuális útmutatóként, amelyek leképezések kell további kimondott szöveg.  
    ![Az adatok egyenetlenségének leképezések kereséséhez használja a "Utterances (szám)".](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Tekintse át a helytelen előrejelzések

A **helytelen előrejelzési** szándék lista mutatja azokat, amelyek rendelkeznek a kimondott szöveg, amely egy adott szándékot példaként szolgálnak, de különböző leképezések a rendszer előre meghatározott leképezések. 

**A probléma megoldásához**:

* A leképezés és train pontosabb lehet újra kimondott szöveg szerkesztése.
* Kombinálhatja a szándék fog vonatkozni, ha utterances szorosan túl vannak elhelyezve, és újra be.

### <a name="review-unclear-predictions"></a>Tekintse át a nem egyértelmű előrejelzések

A **egyértelmű előrejelzési** szándék lista mutatja azokat az előrejelzési eredmények, amelyek nem sokkal elegendő módja az, hogy a felső leképezésének az utterance (kifejezés) módosítsa a következő képzési oka az, hogy a legközelebbi rivális utterances-leképezések [ negatív mintavételi](luis-how-to-train.md#train-with-all-data).

**A probléma megoldásához**;

* A leképezés és train pontosabb lehet újra kimondott szöveg szerkesztése.
* Kombinálhatja a szándék fog vonatkozni, ha utterances szorosan túl vannak elhelyezve, és újra be.

## <a name="utterances-per-intent"></a>Leképezés kiszolgálónként kimondott szöveg

Ez a kártya a szándék között alkalmazás általános állapotát mutatja. Leképezések és teljesített kapcsolat-újraépítési javításához, továbbra is, ez a kártya problémák áttekintése.

A következő diagram mutatja egy jól kiegyensúlyozott alkalmazás, és majdnem nincsenek problémák megoldásához.

![A következő diagram mutatja egy jól kiegyensúlyozott alkalmazás, és majdnem nincsenek problémák megoldásához.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

A következő diagram mutatja egy rosszul elosztott terhelésű alkalmazás számos probléma megoldásához és.

![A következő diagram mutatja egy jól kiegyensúlyozott alkalmazás, és majdnem nincsenek problémák megoldásához.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Minden egyes szándékot sáv a leképezés információt szeretne kapni a kurzort. 

![A következő diagram mutatja egy jól kiegyensúlyozott alkalmazás, és majdnem nincsenek problémák megoldásához.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Használja a **rendezés** szolgáltatást rendezze a szándék szerint probléma típusa, így Ön az, hogy a probléma a legtöbb problémát leképezések a. 

## <a name="intents-with-errors"></a>Leképezések hibákkal

Ez a kártya lehetővé teszi, hogy olvassa el a problémákat az egy adott szándékot. Ez a kártya alapértelmezett nézete a legtöbb problémát leképezések, így megtudhatja, hol összpontosítsa figyelmét.

![A hibák kártyával leképezések lehetővé teszi, hogy egy adott szándékot problémái tekintse át. A kártya a szűrt a legtöbb problémát szándék fog vonatkozni, alapértelmezés szerint, hogy tudja, hol összpontosítsa figyelmét.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Az első fánkdiagramot a problémákat azzal a szándékkal, a három a probléma típusában jeleníti meg. Ha problémák vannak a három a probléma típusok, minden típusban a saját diagram alá, és bármely versenytárs leképezések. 

### <a name="filter-intents-by-issue-and-percentage"></a>Probléma és százalékos leképezések szűrése

Ez a szakasz a kártya lehetővé teszi, hogy a hiba küszöbértéken kívüli csökken példa megcímkézzen található. Ideális esetben érdemes jelentőségű tényező lehet, hogy megfelelő ismeretekkel. A százalékos értéke, üzleti és ügyfél-alapú. 

Határozza meg a küszöbérték százalékos, amelynek Ön a magabiztosan a vállalkozása számára. 

A szűrő lehetővé teszi az adott probléma leképezések található:

|Szűrés|Javasolt százalékos aránya|Cél|
|--|--|--|
|Legtöbb problémát leképezések|-|**Kezdje itt** -megcímkézzen értesítenünk a leképezés javítja az alkalmazás több, mint más javításokat.|
|Az alábbi megfelelő előrejelzések|60%|Ez az, hogy helyes-e, de egy megbízhatósági pontszám a küszöbérték alatt van a kiválasztott SZÁNDÉKTÓL megcímkézzen aránya. |
|A fenti egyértelmű előrejelzések|15%|Ez az, hogy a rendszer a legközelebbi versenytárs célt összetéveszteni megcímkézzen a kiválasztott SZÁNDÉKTÓL aránya.|
|A fenti helytelen előrejelzések|15%|Ez az, hogy helytelenül vannak előre jelzett a kiválasztott SZÁNDÉKTÓL megcímkézzen aránya. |

### <a name="correct-prediction-threshold"></a>Megfelelő előrejelzési küszöbérték

Mi az a benne előrejelzési magabiztossági pontszámot is? Alkalmazások fejlesztése az elején, 60 %-os lehet, hogy a cél. Használja a **javítsa ki az alábbi előrejelzéseket** százalékkal 60 %-os megszólalásokat található a kiválasztott célt, amelyet javítani kell.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Nem egyértelmű vagy hibás előrejelzési küszöbérték

E két naplófájlbejegyzések kimondott szöveg található a kiválasztott leképezés meghaladja a küszöbértéket. E két százalékos hiba százalékként is felfoghatók. Ha Ön egy 10 – 15 %-os Hibaarány ismeretekkel ügyfélszolgáltatást, állítsa be a szűrő küszöbértéket 15 % található összes utterances meghaladja ezt az értéket. 

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások kezelése](luis-how-to-azure-subscription.md)
