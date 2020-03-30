---
title: Modellteljesítmény értékelése
titleSuffix: ML Studio (classic) - Azure
description: Ismerje meg, hogyan értékelheti ki a modell teljesítményét az Azure Machine Learning Studio (klasszikus) és a feladathoz elérhető metrikák.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3c041834b9ad191817cdf1380b0a75efc7639bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218145"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>A modellteljesítmény kiértékelése az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez a cikk bemutatja, hogyan értékelheti ki egy modell teljesítményét az Azure Machine Learning Studio (klasszikus) és rövid magyarázatot nyújt a feladathoz rendelkezésre álló metrikák. Három közös felügyelt tanulási forgatókönyv kerül bemutatásra: 

* Regresszió
* bináris osztályozás 
* többosztályos osztályozás



Egy modell teljesítményének kiértékelése az adatelemzési folyamat egyik alapvető szakasza. Azt jelzi, hogy egy adatkészlet pontozási (előrejelzések) egy betanított modell sikeres. 

Az Azure Machine Learning Studio (klasszikus) két fő gépi tanulási moduljával támogatja a modellkiértékelést: [a Modell kiértékelése][evaluate-model] és [a keresztérvényesítési modell.][cross-validate-model] Ezek a modulok lehetővé teszik, hogy a modell hogyan teljesít a gépi tanulásban és a statisztikákban gyakran használt metrikák tekintetében.

## <a name="evaluation-vs-cross-validation"></a>Értékelés és keresztérvényesítés
Az értékelés és a keresztérvényesítés a modell teljesítményének mérésére szolgál. Mindkettő olyan értékelési mutatókat hoz létre, amelyeket megvizsgálhat vagy összehasonlíthat más modellekével.

[A Model kiértékelése][evaluate-model] egy pontozott adatkészletet vár bemenetként (vagy kettőt arra az esetre, ha két különböző modell teljesítményét szeretné összehasonlítani). Ezért be kell tanítania a modellt a [Betanítási modell][train-model] modul használatával, és előrejelzéseket kell készítenie néhány adatkészleten a [Score Model][score-model] modul használatával, mielőtt kiértékelheti az eredményeket. A kiértékelés a pontozott címkéken/valószínűségeken és a valódi címkéken alapul, amelyek mindegyike a [Score Model][score-model] modul által kimenet.

Azt is megteheti, keresztérvényesítés végrehajtásához számos vonat-score-kiértékelése műveletek (10-szeres) automatikusan a bemeneti adatok különböző részhalmazain. A bemeneti adatok 10 részre vannak felosztva, ahol az egyik tesztelésre van fenntartva, a másik 9 pedig a betanításra. Ez a folyamat ismétlődik 10-szer, és a kiértékelési mutatók átlaga. Ez segít annak meghatározásában, hogy egy modell mennyire általánosítaná az új adatkészleteket. A [kereszt-validate modell][cross-validate-model] modul vesz egy képzetlen modell és néhány címkézett adatkészlet et, és az átlagos eredmények mellett a 10-szeresek kiértékelési eredményeit adja ki.

A következő szakaszokban egyszerű regressziós és besorolási modelleket hozunk létre, és kiértékeljük azok teljesítményét a [Modell kiértékelése][evaluate-model] és a [Kereszt-érvényesítési modell][cross-validate-model] modulok használatával.

## <a name="evaluating-a-regression-model"></a>Regressziós modell kiértékelése
Tegyük fel, hogy meg akarjuk jósolni egy autó árát olyan funkciókkal, mint a méretek, a lóerő, a motor specifikációi és így tovább. Ez egy tipikus regressziós probléma, ahol a célváltozó (*ár*) egy folyamatos numerikus érték. Elfér egy lineáris regressziós modell, amely egy bizonyos autó jellemzőértékei miatt meg tudja jósolni az autó árát. Ez a regressziós modell használható pontszám ugyanazt az adatkészletet, amelybe betanított. Amint megvannak az előre jelzett autóárak, értékelhetjük a modell teljesítményét, ha megnézzük, hogy az előrejelzések átlagosan mennyire térnek el a tényleges áraktól. Ennek illusztrálására a Machine Learning Studio (klasszikus) **Mentett adatkészletek** szakaszában elérhető Automobile *áradat-adatkészleteket (Raw) használjuk.*

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterülethez az Azure Machine Learning Studio -ban (klasszikus):

* Gépjármű áradatai (Nyers)
* [Lineáris regresszió][linear-regression]
* [Vonat modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakoztassa a portokat az *1.* [Train Model][train-model]

![Regressziós modell kiértékelése](./media/evaluate-model-performance/1.png)

1. ábra Regressziós modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>Az értékelési eredmények vizsgálata
A kísérlet futtatása után kattintson a [modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a *Visualize* lehetőséget a kiértékelési eredmények megtekintéséhez. A regressziós modellekhez rendelkezésre álló értékelési mutatók a következők: *átlagos abszolút hiba*, legfelső szintű átlagos abszolút *hiba*, relatív *abszolút hiba*, *relatív négyzethiba*és *meghatározási együttható*.

A "hiba" kifejezés itt az előre jelzett érték és a valós érték közötti különbséget jelöli. Ennek a különbségnek az abszolút értékét vagy négyzetét általában úgy számítják ki, hogy az összes példányban a hiba teljes nagyságát rögzítsék, mivel az előre jelzett és a valós érték közötti különbség bizonyos esetekben negatív lehet. A hibametrikák a regressziós modell prediktív teljesítményét mérik az előrejelzések nek a valós értékektől való átlagos eltérése alapján. Az alacsonyabb hibaértékek azt jelentik, hogy a modell pontosabb az előrejelzések készítésében. A nulla általános hibamérő azt jelenti, hogy a modell tökéletesen illeszkedik az adatokhoz.

A meghatározási együttható, amelyet R négyzetnek is neveznek, szintén szabványos módszer annak mérésére, hogy a modell mennyire illeszkedik az adatokhoz. Úgy értelmezhető, mint a modell által magyarázott variációs arány. Ebben az esetben a nagyobb arány jobb, ha az 1 tökéletes illeszkedést jelez.

![Lineáris regressziós kiértékelési mutatók](./media/evaluate-model-performance/2.png)

2. ábra Lineáris regressziós kiértékelési mutatók.

### <a name="using-cross-validation"></a>Keresztellenőrzés használata
Mint korábban említettük, a [Kereszt-érvényesítési modell][cross-validate-model] modul automatikusan végezhet ismételt betanítás, pontozás és értékelések. Ebben az esetben mindössze egy adatkészletre, egy képzetlen modellre és egy [keresztérvényesítési modell][cross-validate-model] modulra van szüksége (lásd az alábbi ábrát). Be kell állítania a címke oszlop *ára* a [Kereszt-modell modul][cross-validate-model] tulajdonságai.

![Regressziós modell keresztérvényesítése](./media/evaluate-model-performance/3.png)

3. ábra Regressziós modell keresztérvényesítése.

A kísérlet futtatása után a kiértékelési eredményeket a [Kereszt-Modell][cross-validate-model] modul jobb kimeneti portjára kattintva vizsgálhatja meg. Ez részletes képet ad az egyes iterációk (hajtás) metrikáiról és az egyes mutatók átlagértékéről (4. ábra).

![Regressziós modell keresztérvényesítési eredményei](./media/evaluate-model-performance/4.png)

4. ábra Regressziós modell keresztérvényesítési eredményei.

## <a name="evaluating-a-binary-classification-model"></a>Bináris osztályozási modell kiértékelése
Bináris besorolási forgatókönyv esetén a célváltozónak csak két lehetséges kimenetele van: {0, 1} vagy {false, true}, {negative, positive}. Tegyük fel, hogy a felnőtt alkalmazottak adatkészletét kapja néhány demográfiai és foglalkoztatási változóval, és meg kell adnia a jövedelmi szint előrejelzését, egy bináris változót, amelynek értéke {"<=50 K", ">50 K"}. Más szóval a negatív osztály azokat a munkavállalókat jelöli, akik évente legfeljebb 50 K-t, a pozitív osztály pedig az összes többi alkalmazottat jelöli. A regressziós forgatókönyvhöz, mi is betanítunk egy modellt, néhány adatot pontolunk, és kiértékeljük az eredményeket. A fő különbség itt a metrikák azure Machine Learning Studio (klasszikus) számítási és kimeneti metrikák kiválasztása. A bevételi szint előrejelzési forgatókönyvének szemléltetéséhez a [felnőtt](https://archive.ics.uci.edu/ml/datasets/Adult) adatkészletet használjuk egy Studio (klasszikus) kísérlet létrehozásához, és kiértékeljük egy kétosztályos logisztikai regressziós modell, egy általánosan használt bináris osztályozó teljesítményét.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterülethez az Azure Machine Learning Studio -ban (klasszikus):

* Felnőtt census income bináris osztályozási adatkészlet
* [Kétosztályos logisztikai regresszió][two-class-logistic-regression]
* [Vonat modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakoztassa a portokat az *5.* [Train Model][train-model]

![Bináris osztályozási modell kiértékelése](./media/evaluate-model-performance/5.png)

5. ábra Bináris osztályozási modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>Az értékelési eredmények vizsgálata
A kísérlet futtatása után kattintson a [Modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a *Visualize* lehetőséget a kiértékelési eredmények megtekintéséhez (7. ábra). A bináris osztályozási modellekhez rendelkezésre álló értékelési mutatók a következők: *Pontosság*, *Pontosság*, *Visszahívás*, *F1 pontszám*és *AUC*. Ezenkívül a modul egy tévesztési mátrixot ad ki, amely a valódi pozitív, hamis negatív, hamis pozitív és valódi negatívok számát, valamint a *ROC,* *a Precision/Recall*és a *Lift* görbék számát mutatja.

A pontosság egyszerűen a helyesen osztályozott példányok aránya. Ez általában az első mérőszám, amelyet az osztályozó kiértékelésekor megvizsgál. Ha azonban a tesztadatok kiegyensúlyozatlanok (ahol a legtöbb példány az egyik osztályhoz tartozik), vagy ha jobban érdekli valamelyik osztály teljesítménye, a pontosság nem igazán rögzíti az osztályozó hatékonyságát. A jövedelmi szint besorolási forgatókönyv, tegyük fel, hogy teszteli néhány adatot, ahol 99%-a példányok képviselik az emberek, akik kevesebbet keresnek, mint 50K évente. 0,99-es pontosság érhető el a "<=50K" osztály előrejelzésével az összes példányra vonatkozóan. Az osztályozó ebben az esetben úgy tűnik, hogy jó munkát végez összességében, de a valóságban, nem osztályozza a magas jövedelmű egyének (a 1%) Helyesen.

Ebből az okból hasznos, hogy további metrikák, amelyek rögzítik az értékelés konkrétabb szempontjait. Mielőtt az ilyen metrikák részleteit, fontos, hogy megértsék a zavart mátrix egy bináris osztályozási kiértékelés. A képzési készlet osztálycímkéi csak két lehetséges értéket vehetnek fel, amelyeket általában pozitívnak vagy negatívnak nevezünk. Az osztályozó által helyesen előrejelzett pozitív és negatív példányokat valódi pozitívnak (TP) és valódi negatívnak (TN) nevezzük. Hasonlóképpen a helytelenül minősített példányokat hamis pozitív (FP) és hamis negatív (FN) minősítésnek nevezzük. A zűrzavaros mátrix egyszerűen egy táblázat, amely bemutatja a négy kategóriába tartozó példányok számát. Az Azure Machine Learning Studio (klasszikus) automatikusan eldönti, hogy az adatkészlet két osztálya közül melyik a pozitív osztály. Ha az osztálycímkék logikai vagy egész számok, akkor az "igaz" vagy "1" címkével ellátott példányok a pozitív osztályhoz vannak rendelve. Ha a címkék karakterláncok, például a jövedelem adatkészlet, a címkék vannak rendezve betűrendben, és az első szint van kiválasztva, hogy a negatív osztály, míg a második szint a pozitív osztály.

![Bináris osztályozási tévesztési mátrix](./media/evaluate-model-performance/6a.png)

6. ábra Bináris osztályozási zavartmátrix.

Visszatérve a jövedelembesorolás problémájára, szeretnénk feltenni néhány értékelési kérdést, amelyek segítenek megérteni az alkalmazott osztályozó teljesítményét. Természetes kérdés: "Azok közül az egyének közül, akiket a modell 50 K>(TP+FP) keresett, hányat minősítettek helyesen (TP)?". Erre a kérdésre a modell **pontossága,** a helyesen osztályozott pozitívumok aránya: TP/(TP+FP) választ kaphat. Egy másik gyakori kérdés: "Az összes magas keresetű alkalmazottak jövedelme >50k (TP + FN), hány nem az osztályozó osztályozni helyesen (TP)". Ez valójában a **Visszahívás**, vagy a valódi pozitív arány: TP /(TP +FN) az osztályozó. Lehet, hogy észre, hogy van egy nyilvánvaló kompromisszum a pontosság és a visszahívás. Például egy viszonylag kiegyensúlyozott adatkészlet, egy osztályozó, amely előrejelzi többnyire pozitív példányok, volna egy magas visszahívás, de egy meglehetősen alacsony pontosságú, mivel sok a negatív példányok lenne tévesen minősítve ami nagyszámú hamis pozitív. Ha meg szeretné tekinteni, hogy ez a két mutató hogyan változik, kattintson a **PONTOSSÁG/VISSZAHÍVÁS** görbére a kiértékelési eredmény kimeneti oldalán (a 7. ábra bal felső részén).

![Bináris osztályozás kiértékelési eredményei](./media/evaluate-model-performance/7.png)

7. ábra Bináris osztályozás imátleértékelés eredményei.

Egy másik kapcsolódó metrika, amelyet gyakran használnak, az **F1 pontszám,** amely mind a pontosságot, mind a visszahívást figyelembe veszi. Ez a két mutató harmonikus középformája, és így számítják ki: F1 = 2 (precíziós x visszahívás) / (pontosság + visszahívás). Az F1 pontszám egy jó módja annak, hogy összefoglalja az értékelés egy számot, de ez mindig egy jó gyakorlat, hogy nézd meg mind a pontosság és a visszahívás együtt, hogy jobban megértsék, hogyan viselkedik az osztályozó.

Ezenkívül megvizsgálható a valódi pozitív arány és a **vevő működési jellemző (ROC)** görbéjének hamis pozitív árfolyama és a megfelelő **terület a görbe alatt (AUC)** érték. Minél közelebb van ez a görbe a bal felső sarokhoz, annál jobb az osztályozó teljesítménye (ez maximalizálja a valódi pozitív arányt, miközben minimalizálja a hamis pozitív arányt). Görbék, amelyek közel vannak az átlós a telek, eredménye osztályozók, hogy általában, hogy előrejelzéseket, amelyek közel véletlenszerű találgatás.

### <a name="using-cross-validation"></a>Keresztellenőrzés használata
A regressziós példához hasonlóan keresztellenőrzést is végrehajthatunk az adatok különböző részhalmazainak automatikus betanításához, pontozásához és kiértékeléséhez. Hasonlóképpen használhatjuk a [kereszt-érvényesítési modell][cross-validate-model] modul, egy képzetlen logisztikai regressziós modell, és egy adatkészlet. A címkeoszlopot *a* [Kereszt-modell][cross-validate-model] modul tulajdonságaiban kell bevételre állítani. A kísérlet futtatása után, és kattintson a jobb kimeneti port a [Kereszt-Validate modell][cross-validate-model] modul, láthatjuk a bináris osztályozásmetriai értékek minden hajtás, amellett, hogy az átlagos és szórása az egyes. 

![Bináris osztályozási modell keresztérvényesítése](./media/evaluate-model-performance/8.png)

8. ábra Bináris osztályozási modell keresztérvényesítése.

![Bináris osztályozó keresztérvényesítési eredményei](./media/evaluate-model-performance/9.png)

9. ábra. Bináris osztályozó keresztérvényesítési eredményei.

## <a name="evaluating-a-multiclass-classification-model"></a>Többosztályos osztályozási modell kiértékelése
Ebben a kísérletben a népszerű [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") adatkészletet fogjuk használni, amely az írisznövény három különböző típusát (osztályait) tartalmazza. Minden példányhoz négy jellemzőérték (sepal hossz/szélesség és sziromhossz/szélesség) tartoznak. Az előző kísérletekben betanítottuk és teszteltük a modelleket ugyanazon adatkészletek használatával. Itt a Split [Data][split] modult használjuk az adatok két részhalmazának létrehozásához, az első betanításához, és a másodikon a pontozáshoz és a kiértékeléshez. Az Iris adatkészlet nyilvánosan elérhető az [UCI Machine Learning Repository,](https://archive.ics.uci.edu/ml/index.html)és letölthető egy [Import Data][import-data] modul.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Adja hozzá a következő modulokat a munkaterülethez az Azure Machine Learning Studio -ban (klasszikus):

* [Adatok importálása][import-data]
* [Többosztályos döntési erdő][multiclass-decision-forest]
* [Adatok felosztása][split]
* [Vonat modell][train-model]
* [Pontszám modell][score-model]
* [Modell kiértékelése][evaluate-model]

Csatlakoztassa a portokat a 10.

Állítsa a Címke oszlop indexét a [Betanítási modell][train-model] modul 5-re. Az adatkészletnek nincs fejlécsora, de tudjuk, hogy az osztályfeliratok az ötödik oszlopban vannak.

Kattintson az [Adatok importálása][import-data] modulra, és állítsa az *Adatforrás* http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.datatulajdonságot webes *URL-címre HTTP-n keresztül,* és az *URL-címet* a parancsra.

Állítsa be a [felosztási adatok][split] modulban a betanításhoz használandó példányok törtét (például 0,7).

![Többosztályos osztályozó kiértékelése](./media/evaluate-model-performance/10.png)

10. ábra. Többosztályos osztályozó kiértékelése

### <a name="inspecting-the-evaluation-results"></a>Az értékelési eredmények vizsgálata
Futtassa a kísérletet, és kattintson a [kiértékelés modell][evaluate-model]kimeneti portjára. Az értékelés eredményeit ebben az esetben egy zűrzavaros mátrix formájában mutatják be. A mátrix mindhárom osztály tényleges és előre jelzett példányait jeleníti meg.

![Többosztályos besorolás értékelési eredményei](./media/evaluate-model-performance/11.png)

11. ábra. Többosztályos besorolás értékelési eredményei.

### <a name="using-cross-validation"></a>Keresztellenőrzés használata
Mint korábban említettük, a [Kereszt-érvényesítési modell][cross-validate-model] modul automatikusan végezhet ismételt betanítás, pontozás és értékelések. Szüksége lenne egy adatkészletre, egy képzetlen modellre és egy [kereszt-érvényesítési modell][cross-validate-model] modulra (lásd az alábbi ábrát). Ismét be kell állítania a [Kereszt-Modell][cross-validate-model] modul címkeoszlopát (ebben az esetben az 5.oszlopindex). A kísérlet futtatása után, és kattintson a jobb kimeneti port a [kereszt-érvényesítési modell,][cross-validate-model]megvizsgálhatja a metrika értékek minden hajtás, valamint a közép-és szórás. Az itt megjelenített metrikák hasonlóak a bináris osztályozási esetben tárgyalt mutatókhoz. A többosztályos osztályozásban azonban a valódi pozitív/negatív és hamis pozitív/negatív értékek számítása osztályonkénti számítással történik, mivel nincs általános pozitív vagy negatív osztály. Például az "Iris-setosa" osztály pontosságának vagy visszahívásának kiszámításakor feltételezzük, hogy ez a pozitív osztály, és az összes többi negatív.

![Többosztályos osztályozási modell keresztérvényesítése](./media/evaluate-model-performance/12.png)

12. ábra. Többosztályos osztályozási modell keresztérvényesítése.

![Többosztályos osztályozási modell keresztérvényesítési eredményei](./media/evaluate-model-performance/13.png)

13. ábra. Többosztályos osztályozási modell keresztérvényesítési eredményei.

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
