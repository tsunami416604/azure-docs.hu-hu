---
title: Értékelje ki a gépi tanulási modell teljesítmény |} Microsoft Docs
description: Ismerteti, hogyan értékelje az Azure Machine Learning modell teljesítményét.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 5dc5348a-4488-4536-99eb-ff105be9b160
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: bb49fd2fe7f72e211fbbda7cffdd2308c2c36fba
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834233"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning"></a>A modellek teljesítményének kiértékelése az Azure Machine Learning rendszerben
Ez a cikk bemutatja, hogyan kell az Azure Machine Learning Studióban-modell teljesítményét értékeli, és a voltak elérhetők metrikák olvashat rövid magyarázatot biztosít ezt a feladatot. Felügyelt tanítás során három gyakori forgatókönyveket mutatnak be: 

* Regressziós
* bináris osztályozás 
* multiclass besorolás

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A teljesítmény-modell kiértékelése egyik alapvető szakaszában az adatok tudományos folyamatban. Azt jelzi, hogyan sikeres a DataSet adatkészlet pontozási (előrejelzés) lett egy betanított modell. 

Az Azure Machine Learning modell kiértékelése két a fő machine learning modulok keresztül támogatja: [modell kiértékelése] [ evaluate-model] és [kereszt modell][cross-validate-model]. Ezek a modulok lehetővé teszik, láthatja, hogyan hajtja végre a modell számos általánosan használt gépi tanulási és a statisztika metrikák tekintetében.

## <a name="evaluation-vs-cross-validation"></a>Kiértékelési vs. Kereszt-ellenőrzési
Kiértékelési és keresztellenőrzési a modell teljesítményétől mérésére szokásos módon. Vizsgálja meg, vagy más modellek az összehasonlításhoz értékelési metrikák mindkettő készítése.

[Értékelje ki a modell] [ evaluate-model] pontozott dataset várja bemeneti (vagy szeretné vetik össze a 2 különböző modellek esetében 2). Ez azt jelenti, hogy kell-e még betanítani a modellt használ a [Train Model] [ train-model] modul, és később előrejelzéseket néhány adathalmaz használatával a [Score Model] [ score-model] modul, mielőtt kiértékelheti az eredményeket. A kiértékelés együtt a true címkék pontozott címkék/valószínűség alapul, amelyek kerülnek a kimenetbe által a [Score Model] [ score-model] modul.

Azt is megteheti segítségével keresztellenőrzési vonat pontszám kiértékelése műveletek (10 modellrészt) többféle automatikusan a bemeneti adatok különböző részhalmaza. A bemeneti adatok 10 részeit, ahol egy fenntartott tesztelése, és az egyéb 9. lehetőséget a képzés oszlik. Ez a folyamat ismétlődik 10-szer, és a program átlagolja az értékelési-metrikák. Ez segít meghatározni, hogy mennyire lesz generalize modell olyan új adatkészletekre. A [kereszt modell] [ cross-validate-model] modul egy kellő modellben és címkézett adatkészlet egyes időt vesz igénybe, és kiírja az értékelés eredményét, az egyes a 10 modellrészt átlagolt eredmények mellett.

Az alábbi szakaszok a rendszer egyszerű regressziós és besorolási modellek létrehozása és értékelje ki a teljesítmény, mind a [modell kiértékelése] [ evaluate-model] és a [kereszt modell] [ cross-validate-model] modulok.

## <a name="evaluating-a-regression-model"></a>Egy regressziós modell kiértékelése
Tegyük fel, azt szeretnénk, hogy az egyes szolgáltatások, például a dimenziók, lóerő, motor specifikációk és így tovább egy autó árának előrejelzése céljából. A tipikus regressziós probléma, ha a célváltozót (*ár*) egy folyamatos numerikus érték. Egy egyszerű lineáris regressziós modellt, amely egy bizonyos autó szolgáltatás értékei is, hogy autó árának előrejelzése fér azt. A regressziós modell pontozása azt képezni a DataSet adatkészlethez használható. Amennyiben az összes az autók előre jelzett árak van, azt meg tudja határozni, a modell teljesítményétől mennyi az előrejelzés eltérnek a tényleges díjak átlagosan megtekintésével. Ennek szemléltetésére használjuk a *Automobile price data (Raw) dataset* érhető el a **mentett adatkészletek** szakaszban az Azure Machine Learning Studióban.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
A következő modulok hozzáadása az Azure Machine Learning Studio munkaterületének:

* Autó price data (Raw)
* [Lineáris regressziós][linear-regression]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakozás a portok alább az 1. ábrán látható módon, és állítsa be a címke oszlopa a [tanítási modell] [ train-model] modul *ár*.

![Egy regressziós modell kiértékelése](./media/evaluate-model-performance/1.png)

1. ábra. Egy regressziós modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>Az értékelés eredményét ellenőrzése
Miután a kísérletet, kattintson a kimeneti portjára a [modell kiértékelése] [ evaluate-model] modul, és válassza ki *Visualize* az értékelési eredmények megtekintése érdekében. A rendelkezésre álló regressziós modell kiértékelése adatok gyűjtése le van: *Mean Absolute Error*, *Root Mean Absolute Error*, *Relative Absolute Error*, *relatív négyzet hiba*, és a *Coefficient of Determination*.

A kifejezés "error" Itt az előre jelzett érték és a true érték közötti eltérést jelöli. A rendszer abszolút értéket vagy különbség négyzetes vannak általában számított rögzítheti a hiba teljes nagysága minden példányára, a true és előre jelzett érték közötti különbséget negatív bizonyos esetekben lehet. A hiba metrikák mérik a prediktív teljesítmény szempontjából az előrejelzés a valódi értékek középértékét eltérése regressziós modell. Alacsonyabb hibaértékek jelenti a modell pontosabb az előrejelzés során. Egy általános hiba a metrika a 0 azt jelenti, hogy a modell tökéletesen megfelel-e az adatokat.

Döntési együttható, amely más néven R-négyzet, mérési milyen mértékben a modell megfelel-e az adatok szabványos módja is. A módosítás a modell magyarázza arányában értelmezhetők. Egy nagyobb arányban célszerűbb ebben az esetben, ahol az 1 azt jelzi, tökéletes.

![Lineáris regressziós értékelési-metrikák](./media/evaluate-model-performance/2.png)

2. ábra Lineáris regressziós értékelési-metrikák.

### <a name="using-cross-validation"></a>Közötti érvényesítése
A korábban említett végezheti el ismételt betanítási, pontozási és értékelések használatával automatikusan a [kereszt modell] [ cross-validate-model] modul. Ebben az esetben szüksége a DataSet adatkészlet olyan kellő modellt, és egy [kereszt modell] [ cross-validate-model] modul (lásd az alábbi ábrát). Vegye figyelembe, hogy kell-e beállítva a címke oszlop *ár* a a [kereszt modell] [ cross-validate-model] modulhoz tartozó tulajdonságok.

![Egy regressziós modell kereszt-ellenőrzése](./media/evaluate-model-performance/3.png)

3. ábra. Kereszt-ellenőrzése egy regressziós modellt.

Miután a kísérletet, vizsgálhatja az értékelés eredményét kattintson a jobb oldali kimeneti portjára a [kereszt modell] [ cross-validate-model] modul. Ez biztosítja a metrikák részletes nézete minden egyes ismétlés (modellészből), és a metrikák (4. ábra) mindegyikének átlagolt eredményét.

![Kereszt-ellenőrzési eredmények regressziós modell](./media/evaluate-model-performance/4.png)

4. ábra. Kereszt-ellenőrzési eredmények regressziós modell.

## <a name="evaluating-a-binary-classification-model"></a>A bináris osztályozási modell kiértékelése
A bináris osztályozási forgatókönyvek a célváltozó rendelkezik csak két eredményekkel, például: {0, 1} vagy {hamis, igaz}, {negatív, pozitív}. Tegyük fel, lehetősége van a DataSet adatkészlet egyes felnőtt az alkalmazottak demográfiai és alkalmazási változók és, hogy a rendszer felkéri a bevétel szintjén, a bináris változó értékekkel előre jelezni {"< 50K =", "> 50-K"}. Más szóval a negatív osztály az alkalmazottak, akik legfeljebb 50 k évente, és a pozitív osztály jelenti minden alkalmazott. Mint a regressziós forgatókönyv esetén azt volna a modell betanításához, néhány adat pontozása és eredmények értékelése. A fő különbség itt az Azure Machine Learning kiszámítja metrikák és a kimeneti. A bevétel szintű előrejelzés forgatókönyvet mutatja be, azt fogja használni a [felnőtt](http://archive.ics.uci.edu/ml/datasets/Adult) adatkészlet Azure Machine Learning kísérleteket, és két osztályú logisztikai regresszió-modell, egy gyakran használt bináris osztályozó teljesítményét értékeli.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
A következő modulok hozzáadása az Azure Machine Learning Studio munkaterületének:

* Felnőtt nyilvántartásba bevétel bináris osztályozási adatkészlet
* [Két osztályú logisztikai regresszió][two-class-logistic-regression]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakozás a portokat, 5. ábra az alábbiak szerint, és állítsa be a címke oszlopa a [tanítási modell] [ train-model] modul *bevétel*.

![A bináris osztályozási modell kiértékelése](./media/evaluate-model-performance/5.png)

5. ábra. A bináris osztályozási modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>Az értékelés eredményét ellenőrzése
Miután a kísérletet, kattintson a kimeneti portjára a [modell kiértékelése] [ evaluate-model] modul, és válassza ki *Visualize* az értékelési eredmények (a 7. ábra) megtekintéséhez. A bináris besorolási modell érhető el értékelési adatok gyűjtése le van: *pontossága*, *pontosság*, *visszahívása*, *F1 pontszám*, és *AUC*. Emellett a modul kimenete egy zavart mátrix igaz pozitív, téves negatív eredmények, a vakriasztások és true negatív számát, valamint *ROC*, *pontosság/visszaírási*, és *növekedési* görbék.

Pontosság egyszerűen megfelelően minősített példányok arányának. Akkor általában az első metrika megnézzük besorolás kiértékelése során. Azonban, ha a Tesztadatok van tette (ahol a példányok a legtöbb tartozik az osztályok egyikét), vagy több érdekli az osztályok egyikét a teljesítmény, a pontosság valóban nem rögzíti a besorolás hatékonyságát. Bevétel szintű besorolást esetben azt feltételezik, néhány adat, ahol a példányok 99 % képviselő személyeket, akik legfeljebb 50 k évente tesztelni. Lehetséges, egy 0.99 pontosság által előrejelzésére az osztály eléréséhez "< = 50-K" minden példány esetében. A osztályozó ebben az esetben, aki egy jó általános feladat jelenik meg, de valójában, az nem megfelelően besorolása bármelyik a nagyjövedelmű személyeket (1 %).

Emiatt érdemes további metrikákat, a kiértékelés pontosabb aspektusainak rögzítő számítási. Mielőtt a felhasználó ilyen metrikák részleteinek, fontos megérteni a bináris osztályozási próbaverzióra zavart mátrix. Az osztály címkék a betanítási készlet csak 2, a lehetséges értékek, amelyek általában az a pozitív vagy negatív vehet igénybe. Besorolás előrejelzi megfelelően pozitív és negatív példányokat nevezzük igaz figyelmeztetéséket (TP), és IGAZ negatív (TN), illetve. Hasonlóképpen a helytelenül minősített példányok téves (pi) és a téves negatív eredmények (FN) nevezik. A félreértések mátrix egyszerűen minden ilyen 4 alá tartozó példányok megjelenítő táblázat. Az Azure Machine Learning automatikusan úgy dönt, a két osztály adatkészlet Ez az a pozitív osztály. Ha az osztály címkék logikai érték vagy egész számok, a "true" vagy "1" címkével ellátott példányok a pozitív osztály rendelt. Ha a feliratok karakterláncok, ahogy a gyorsítás esetében a bevétel adatkészlet, a címkék vannak elemei ábécésorrendben vannak, és az első szinten kell lennie a negatív osztály, míg a második szintű pozitív osztály van kiválasztva.

![Bináris osztályozási zavart mátrix](./media/evaluate-model-performance/6a.png)

6. ábra. Bináris osztályozási zavart mátrix.

Ha visszalép, a bevétel besorolás problémára, volna szeretnénk kérje meg a számos értékelési kérdést, hogy segítsen megérteni a használt besorolás teljesítményét. Egy nagyon természetes kérdés: "a személyeket, akiknek a modell előre jelezni kell megszerzéséhez a kívül > 50-K (TP + pi), hogy hány sorolták megfelelően (TP)?" Ez a kérdés is válaszolhatók megnézzük a **pontosság** a modell, amely megfelelő besorolású figyelmeztetéséket aránya: TP/(TP+FP). Egy másik gyakori kérdés az "a nagy kívül megszerzéséhez bevétel az alkalmazottak > 50-k (TP + FN), hány volt a osztályozó besorolása megfelelően (TP)". Ez valójában a **visszahívása**, vagy igaz pozitív aránya: az osztályozó TP/(TP+FN). Bizonyára észrevette, hogy, hogy van-e egy nyilvánvaló kompromisszum pontosság és a visszaírási között. Például egy viszonylag kiegyensúlyozott adatkészlet megadott, amelyek többnyire pozitív példányok előrejelzi besorolás magas visszaírási kellene lennie, de eredményezve számos téves negatív példánya annyi ahelyett, hogy alacsony pontossága volna misclassified. Hogyan két metrikákat eltérő ábrázolása megtekintéséhez rákattinthat a "PONTOSSÁG/VISSZAÍRÁSI" görbe a kiértékelési eredmények kimeneti oldalon (a 7. ábra részét bal felső).

![Bináris osztályozási kiértékelésének eredménye](./media/evaluate-model-performance/7.png)

7. ábra. Bináris osztályozási kiértékelésének eredménye.

Egy másik kapcsolatos gyakran használják a metrika a **F1 pontszám**, amely a pontosság és a visszaírási figyelembe veszi. Közepének 2 metrikákat, és mint ilyen számított: F1 = 2 (pontosság x visszaírási) / (pontosság + visszaírási). Az F1 pontszám egyetlen számos értékelési összesítésének módja, de ajánlott mindig a pontosság és a visszaírási együtt, amelyek segítenek megállapítani, hogyan viselkedik a besorolás közül.

Emellett egy vizsgálhatja meg a true pozitív sebesség és a téves pozitív sebessége a **fogadó működő jellemző (ROC)** görbe és a megfelelő **terület alatt a görbe (AUC)** érték. Minél közelebb a görbe a bal felső sarokban, annál pontosabb az osztályozó teljesítmény (, amely van maximalizálva igaz pozitív sebessége ugyanakkor minimalizálja a téves pozitív sebessége). Az ábra, véletlenszerű találgatás hamarosan előrejelzéseket készítsen az egyes osztályozó kapott eredmény átlós hamarosan görbék.

### <a name="using-cross-validation"></a>Közötti érvényesítése
Az regressziós példában látható módon végezzük is keresztellenőrzési ismételten képzése, pontszám és az adatok különböző részhalmazai automatikusan kiértékeléséhez. Ehhez hasonlóan is használhatók a [kereszt modell] [ cross-validate-model] modul, egy kellő logisztikai regresszió modell és a DataSet adatkészlet. A címke oszlop értékre kell állítani *bevétel* a a [kereszt modell] [ cross-validate-model] modulhoz tartozó tulajdonságok. A kísérlet fut, és kattintson a jobb oldali kimeneti portjára után a [kereszt modell] [ cross-validate-model] modul, azt is feltűnik a bináris osztályozási minden modellrészek metrika értékek és az egyes szórásnál. 

![A bináris osztályozási modellek közötti érvényesítése](./media/evaluate-model-performance/8.png)

8. ábra. A bináris osztályozási modellek közötti ellenőrzése.

![Egy bináris osztályozó kereszt-ellenőrzési eredményeit](./media/evaluate-model-performance/9.png)

9. ábra. Egy bináris osztályozó kereszt-ellenőrzési eredményeit.

## <a name="evaluating-a-multiclass-classification-model"></a>A Multiclass besorolási modell kiértékelése
A kísérletben használjuk a népszerű [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") 3 különböző típusú (osztály) a iris gépek példányait tartalmazó adathalmaz. 4 szolgáltatás értékek vannak (sepal hossza/szélesség és hosszúság/szélesség szirom) minden példány esetében. Az előző kísérletekben azt betanítása és tesztelt használja az ugyanazon adatkészletek modellek. Itt fogjuk használni a [Split Data] [ split] létrehozása a data 2 részhalmaza, az első betanítása és pontszám és a második kiértékelése modul. A Iris dataset nyilvánosan elérhető a [UCI Machine Learning tárház](http://archive.ics.uci.edu/ml/index.html), és használatával tölthető le egy [adatok importálása] [ import-data] modul.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
A következő modulok hozzáadása az Azure Machine Learning Studio munkaterületének:

* [Adatok importálása][import-data]
* [Multiclass döntési erdő][multiclass-decision-forest]
* [Adatok][split]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakoztassa a portokat, a 10. ábra az alább látható módon.

Állítsa be a címke oszlop indexe a [tanítási modell] [ train-model] 5 modul. A dataset adatkészletben nem fejlécsor, de tudjuk, hogy az osztály feliratai az ötödik oszlopban.

Kattintson a a [és adatokat importálhat] [ import-data] modul és a készlet a *adatforrás* tulajdonságot *webes URL-Címéhez HTTP Protokollon keresztül*, és a *URL* való http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Állítsa be, hogy a képzés használható hányada a [Split Data] [ split] modul (például 0,7).

![A Multiclass osztályozó kiértékelése](./media/evaluate-model-performance/10.png)

10. ábra. A Multiclass osztályozó kiértékelése

### <a name="inspecting-the-evaluation-results"></a>Az értékelés eredményét ellenőrzése
Futtassa a kísérletet, majd kattintson a kimeneti portjára a [modell kiértékelése][evaluate-model]. Az értékelési eredmények jelenjenek meg az űrlap egy zavart mátrix ebben az esetben. A mátrix a tényleges és előre jelzett összes 3 osztály példányainak jeleníti meg.

![Multiclass besorolási kiértékelésének eredménye](./media/evaluate-model-performance/11.png)

11. ábra. Multiclass besorolási kiértékelésének eredménye.

### <a name="using-cross-validation"></a>Közötti érvényesítése
A korábban említett végezheti el ismételt betanítási, pontozási és értékelések használatával automatikusan a [kereszt modell] [ cross-validate-model] modul. A DataSet adatkészlet olyan kellő modellt kell és egy [kereszt modell] [ cross-validate-model] modul (lásd az alábbi ábrát). Újra be kell állítani a címke oszlopa a [kereszt modell] [ cross-validate-model] modul (oszlopindex 5 ebben az esetben). Miután a kísérlet fut, és kattintson a jobb oldali kimeneti portjára a [kereszt modell][cross-validate-model], vizsgálhatja meg a metrika értékek egyes modellrészek, valamint a közepes és standard eltérés. Az itt látható adatok gyűjtése le a hasonló a bináris osztályozási eset leírtaknak megfelelően. Vegye figyelembe azonban, hogy multiclass besorolásban számítási a true pozitív vagy negatív, és a téves pozitív vagy negatív eredmények végezhető el egy osztály alapon számbavételi, nincs átfogó pozitív vagy negatív osztály. Például a pontosság vagy a "Iris-setosa" osztály visszaírási számításakor feltételezzük, hogy ez az a pozitív és az összes többit negatív.

![A Multiclass besorolási modell kereszt-ellenőrzése](./media/evaluate-model-performance/12.png)

12. ábra. Kereszt-ellenőrzése egy Multiclass besorolási modell.

![Kereszt-ellenőrzési eredmények Multiclass besorolási modell](./media/evaluate-model-performance/13.png)

13. ábra. Kereszt-ellenőrzési eredmények Multiclass besorolási modell.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

