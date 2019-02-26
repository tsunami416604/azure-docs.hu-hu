---
title: Modellek teljesítményének elemzése
titleSuffix: Azure Machine Learning Studio
description: Ez a cikk bemutatja, hogyan lehet egy modellt az Azure Machine Learning Studióban teljesítményét értékeli, és amely röviden elmagyarázza, a metrikák elérhető ez a feladat biztosít.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: f5e7a923ed5eb692f5fb05082c73b969081c739b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820994"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio"></a>Hogyan modellek teljesítményének kiértékelése az Azure Machine Learning Studióban

Ez a cikk bemutatja, hogyan lehet egy modellt az Azure Machine Learning Studióban teljesítményét értékeli, és amely röviden elmagyarázza, a metrikák elérhető ez a feladat biztosít. Három gyakori felügyelt tanítás forgatókönyvek jelennek meg: 

* Regresszió
* Bináris osztályozás 
* többosztályos osztályozási



Modellek teljesítményének kiértékelése az egyik fő szakaszai az adatelemzési folyamat. Ez azt jelzi, hogy hogyan sikeres adatkészlet pontozási (előrejelzés) lett egy betanított modell. 

Az Azure Machine Learning Studióban modell értékelése két a fő machine learning-modulok keresztül támogatja: [Modell értékelése] [ evaluate-model] és [Halmazokra modell][cross-validate-model]. Ezek a modulok lehetővé teszik, láthatja, hogyan hajtja végre a modell szempontjából a gépi tanulás és statisztikai gyakran használt mérőszámokat.

## <a name="evaluation-vs-cross-validation"></a>Értékelés vs. Keresztellenőrzés
Kiértékelési és a keresztellenőrzési a modell a teljesítmény méréséhez szokásos módon. Értékelési mérőszámok, vizsgálja meg, vagy más modellek az összehasonlításhoz mindkettő létre.

[Modell értékelése] [ evaluate-model] vár egy pontozott adatkészlet bemeneti (vagy 2 abban az esetben két különböző modellek teljesítményének összehasonlítását szeretné). Ez azt jelenti, hogy kell-e be a modell használatával a [Train Model] [ train-model] modul és a márka alapján történő néhány adatkészlet használatával a [Score Model] [ score-model]modult, mielőtt kiértékelheti az eredményeket. Az értékelés alapján a pontozott címkék/valószínűség együtt a valódi címkéket, amelyek mindegyike vannak kimenetét a [Score Model] [ score-model] modul.

Azt is megteheti használhatja keresztellenőrzési végrehajtásához egy train-pontszám kiértékelése műveletek (10 modellrész) száma automatikusan a bemeneti adatok különböző alkészleteiben. A bemeneti adatok 10 részei, ahol egy foglalt teszteléséhez, és a többi 9 képzéshez van felosztva. Ez a folyamat ismétlődik 10 alkalommal és átlagolja az értékelési mérőszámok. Ez segít arról, hogy a modell új adatkészletekhez szeretné generalize meghatározása. A [Halmazokra modell] [ cross-validate-model] modul egy kellő modell és néhány megcímkézett adatkészletet, és kiírja a 10 modellrész átlagolt eredmények mellett minden egyes kiértékelési eredményeit.

A következő szakaszokban lesz létrehozása egyszerű regressziós és besorolási modelleket, valamint kiértékelheti a teljesítményüket, mind a [Evaluate Model] [ evaluate-model] és a [Halmazokra modell ] [ cross-validate-model] modulok.

## <a name="evaluating-a-regression-model"></a>Egy regressziós modell értékelése
Tegyük fel, szeretnénk előre jelezni egy autó árát az egyes szolgáltatások, például a dimenziók, lóerő, motor adatait tartalmazza, és így tovább. A tipikus regressziós probléma, ahol a célváltozó (*ár*) egy folyamatos numerikus érték. Hogy elhelyezhessen egy egyszerű lineáris regressziós modellt, hogy egy bizonyos autó funkció értékeinek tudja jelezni, hogy autó árát. Itt látható regressziós modell pontozása ugyanahhoz az adatkészlethez, hogy tanított használható. Amint rendelkezésünkre áll, az előre jelzett árak az összes, az autó, mennyi az előrejelzés eltérnek a tényleges árak átlagosan megtekintésével is kiértékeljük a modell teljesítményét. Ennek szemléltetésére használjuk a *Automobile price data (Raw) adatkészlet* érhető el a **mentett adatkészletek** szakaszban az Azure Machine Learning Studióban.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az Azure Machine Learning Studio-munkaterülethez adja hozzá a következő modulok:

* Autó price data (Raw)
* [Lineáris regresszió][linear-regression]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell értékelése][evaluate-model]

Csatlakozás a portokat, 1. ábra az alábbiak szerint, és állítsa be a címke oszlopban található a [tanítási modell] [ train-model] modul *ár*.

![Egy regressziós modell értékelése](./media/evaluate-model-performance/1.png)

1. ábra Egy regressziós modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
Miután a kísérletet, kattintson a kimeneti portjára, a a [Evaluate Model] [ evaluate-model] modul, és válassza ki *Visualize* az értékelési eredmények megtekintéséhez. Az értékelési mérőszámok rendelkezésre regressziós modellek a következők: *Mean Absolute Error*, *Mean Absolute Error kiváltó*, *relatív abszolút hiba*, *relatív négyzet hiba*, és a *együttható Meghatározása*.

Az előfizetési időszak "error" Itt az előre jelzett érték és a true érték közötti eltérést jelöli. Az abszolút értékét, vagy ez a különbség négyzetes általában számított hiba teljes mértékű rögzítése minden példányára, lehet, hogy az előre jelzett és a true érték közötti különbség bizonyos esetekben negatív. A hiba metrikák mérik a prediktív teljesítmény szempontjából az előrejelzések a valódi értékek középértékét összehasonlítással regressziós modell. Alacsonyabb hibaértékek jelenti azt, hogy a modell a pontos előrejelzések készítése során. Egy általános hiba a mérőszám értéke nulla, az azt jelenti, hogy a modell tökéletesen megfelel-e az adatokat.

A relatív meghatározása, amely más néven az R-négyzet, is mérési arról, hogy a modell megfelel-e az adatok szabványos módon. Is kell értelmezni az időarány, amíg a módosítás a modell ismertetése. Magasabb szintű ebben az esetben jobb részét, ahol az 1 azt jelzi, hogy tökéletes választás.

![Lineáris regresszió értékelési mérőszámok](./media/evaluate-model-performance/2.png)

2. ábra Lineáris regresszió értékelési mérőszámok.

### <a name="using-cross-validation"></a>Közötti érvényesítése
Ahogy korábban említettük, végezhet ismételt betanítási, pontozási és értékelések használatával automatikusan a [Halmazokra modell] [ cross-validate-model] modul. Ebben az esetben szüksége egy adatkészletet, egy kellő modell, és a egy [Halmazokra modell] [ cross-validate-model] modul (lásd az alábbi ábrát). Állítsa a felirat oszlopban kell *ár* a a [Halmazokra modell] [ cross-validate-model] -modulhoz tartozó tulajdonságok.

![Egy regressziós modell közötti érvényesítése](./media/evaluate-model-performance/3.png)

3. ábra Kereszt-ellenőrzése egy regressziós modellt.

Miután a kísérletet, vizsgálhatja meg a kiértékelési eredményeket kattintson a jobb oldali kimeneti portjával a [Halmazokra modell] [ cross-validate-model] modul. A metrikák részletes képet biztosít minden egyes ismétléskor (modellrészek) és a metrikák (4. ábra) mindegyike átlagolt eredményeit.

![Kereszt-ellenőrzési eredmények a regressziós modell](./media/evaluate-model-performance/4.png)

4. ábra Kereszt-ellenőrzési eredmények a regressziós modell.

## <a name="evaluating-a-binary-classification-model"></a>Egy bináris osztályozási modell értékelése
A bináris osztályozási forgatókönyvekben a célváltozó rendelkezik csak két lehetséges kimenetek, például: {0, 1} vagy {FALSE (hamis), true}, {negatív, pozitív}. Tegyük fel, kapnak egy adatkészlet néhány felnőtt az alkalmazottak demográfiai és alkalmazási változók és, hogy a rendszer felkéri a bevétel szintjét, azokra az értékekre bináris változó előrejelzése {"< = 50 K", "> 50 K"}. Más szóval a negatív osztály jelöli a személyeket, akik győződjön meg, legfeljebb 50 k évenként, és a pozitív osztály minden alkalmazott jelöli. Hasonlóan a regressziós a forgatókönyvben azt lenne a modell betanítását, bizonyos adatok pontozása és eredmények értékelése. A fő különbség az, kiszámítja az Azure Machine Learning Studio metrikák és kimenetek kiválasztása. A bevétel szintű előrejelzési forgatókönyvet mutatja be, ezzel a [felnőtt](http://archive.ics.uci.edu/ml/datasets/Adult) adatkészlet Studio kísérlet létrehozása és a egy két osztályú logisztikai regressziós modell, egy gyakran használt bináris osztályozó teljesítményének értékeléséhez.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az Azure Machine Learning Studio-munkaterülethez adja hozzá a következő modulok:

* Felnőtt népszámlálási jövedelem bináris osztályozási adatkészlet
* [Kétosztályos logisztikai regresszió][two-class-logistic-regression]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell értékelése][evaluate-model]

Csatlakozás a portokat, 5. ábra az alábbiak szerint, és állítsa be a címke oszlopban található a [tanítási modell] [ train-model] modul *jövedelem*.

![Egy bináris osztályozási modell értékelése](./media/evaluate-model-performance/5.png)

5. ábra Egy bináris osztályozási modell kiértékelése.

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
Miután a kísérletet, kattintson a kimeneti portjára, a a [Evaluate Model] [ evaluate-model] modul, és válassza ki *Visualize* megtekintéséhez a kiértékelésének eredménye (7. ábra). A bináris osztályozási modell érhető el értékelési mérőszámok a következők: *Pontosság*, *pontosság*, *visszahívása*, *F1 pontszám*, és *AUC*. Emellett a modul adja vissza egy keveredési mátrixot azokról a valódi pozitívok, a téves negatív, a vakriasztások és a true negatív, valamint *ROC*, *pontosság/visszaírási*, és  *Átemelés* görbék.

Pontosság egyszerűen az időarány, amíg megfelelően osztályozott példány. Ez általában a megtekinti egy osztályozó kiértékelésekor a első metrikát. Azonban, ha a Tesztadatok van kiegyensúlyozatlan (ahol a példányok a legtöbb tartozik az osztályok egyikét), vagy több érdekli az osztályok egyikét a teljesítmény, a pontosság nem igazán rögzíti egy osztályozó hatékonyságát. Bevétel szintű besorolási esetben néhány adatot, ahol a 99 %-a-példányok legfeljebb 50 k évenként megszerzésére személyek képviselik a tesztelt feltételezik. Lehetséges egy 0.99 pontosság alapján az osztály eléréséhez "< = 50K" minden példány esetében. Az osztályozó által igénybe vett ebben az esetben lehet előkészítésétől általános jó feladat jelenik meg, de a valóságban ez nem sikerül besorolása bármelyik nagyjövedelmű felhasználók (1 %) megfelelően.

Éppen ezért hasznos lehet a további metrikákat, a kiértékelés pontosabb aspektusait rögzítő számítási. Való elhelyezés előtt ilyen metrikák részleteit, fontos tudni, hogy a keveredési mátrix egy bináris osztályozási kiértékelési. Az osztály a gyakorlókészlethez feliratokat csak 2 lehetséges értékeket, amelyek általában nevezzük hajthatja végre, pozitív vagy negatív. A pozitív és negatív példányok, amely besorolás képes megfelelően nevezzük valódi pozitívok (TP), és IGAZ negatív (TN), illetve. Hasonlóképpen a hibásan besorolt példányok vakriasztások (pi) és a téves negatív (FN) nevezzük. A keveredési mátrix egyszerűen, amelyek minden ilyen 4 példányok megjelenítő táblázat. Az Azure Machine Learning Studio automatikusan úgy dönt, hogy a két osztály az adatkészletben lévő Ez az a pozitív osztály. Ha az osztály címkék logikai érték, vagy az egész számok, majd a "true" vagy "1" címkével ellátott példányt rendel a pozitív osztály. Ha a címkék olyan karakterláncok, a bevétel adatkészlet is, a címkék betűrendben találhatók, és az első szinten van kiválasztva a negatív osztályt kell, míg a második szinthez a pozitív osztály.

![Bináris osztályozás keveredési mátrix](./media/evaluate-model-performance/6a.png)

6. ábra Bináris osztályozás keveredési mátrixot.

Visszatérve a bevételek besorolása problémát, akkor szeretnénk tehet fel és számos értékelési kérdést, hogy segítsen megérteni a használt besorolás teljesítményét. Nagyon természetes kérdése van: "Ki az egyéni felhasználók számára, akikkel a modell meghalad kell megszerzéséhez > 50 ezer (TP + pi), hány sorolták megfelelően (TP)?" Erre a kérdésre választ megnézzük a **pontosság** a modell, amely megfelelően besorolt pozitívok az időarány, amíg: TP/(TP+FP). Egy másik gyakori kérdés az "kívül az összes nagy megszerzéséhez jövedelem az alkalmazottak > 50 ezer (TP + FN), hány volt az osztályozó által igénybe vett besorolása megfelelően (TP)". Ez valójában a **visszahívása**, vagy a valódi pozitív gyakorisága: Az osztályozó TP/(TP+FN). Észreveheti, hogy nincs-e egy nyilvánvaló kompromisszum pontosság és a visszahívás közötti. Például adja meg egy viszonylag elosztott terhelésű adatkészletet, besorolás, amely előrejelzi a leginkább pozitív példányok kell magas visszaírási, de sok téves eredményez a negatív példányok annyi meglehetősen alacsony pontossága lenne misclassified. Egy diagram, hogyan változnak a két metrikák megtekintéséhez rákattinthat a a **PONTOSSÁG/VISSZAÍRÁSI** görbe az értékelés eredmény kimeneti oldalon (7. ábra bal felső része).

![Bináris osztályozás kiértékelésének eredménye](./media/evaluate-model-performance/7.png)

7. ábra Bináris osztályozás kiértékelésének eredménye.

Egy másik kapcsolatos gyakran használják a metrika a **F1 pontszám**, amely a pontosság és a visszaírási figyelembe veszi. 2 metrikák közepének, és mint ilyen számított: F1 = 2 (pontosság x visszaírási) / (pontosság + visszahívása). Az F1 pontszám összefoglalva az értékelés egyetlen szám a jó módszer, de a rendszer mindig jó megoldás a pontosság és a visszaírási együtt jobban megismerheti, hogyan viselkedik a besorolás, tekintse meg.

Emellett egy vizsgálhatja meg az IGAZ riasztási aránnyal, és a téves riasztási aránnyal a **fogadó működő jellemző (ROC)** görbe és a megfelelő **terület alatt a görbe – AUC** értéket. Minél közelebb a görbe a bal felső sarokban, annál jobb az osztályozó által igénybe vett teljesítmény (, amely a lehető legnagyobb valódi pozitív sebessége ugyanakkor minimalizálja a téves riasztási aránnyal). A diagram, osztályozó eszközökkel, amelyek általában a közelében véletlenszerű találgatás előrejelzéseket eredményét, átlós közelében lévő görbék.

### <a name="using-cross-validation"></a>Közötti érvényesítése
A regressziós példához hasonlóan a keresztellenőrzési ismételten betanításához pontszám és az adatok különböző alkészleteiben automatikusan kiértékeléséhez is végrehajthatjuk. Ehhez hasonlóan használhatók a [Halmazokra modell] [ cross-validate-model] modul egy kellő logisztikai regressziós modellt és egy adatkészletet. A címke oszlop értékre kell állítani *jövedelem* a a [Halmazokra modell] [ cross-validate-model] -modulhoz tartozó tulajdonságok. A kísérlet futtatásával, és kattintson a jobb oldali kimeneti portjára, miután a [Halmazokra modell] [ cross-validate-model] modult, látható minden egyes modellrészek bináris osztályozási metrikaértékek emellett az átlag, és az egyes szórás. 

![Egy bináris osztályozási modell közötti érvényesítése](./media/evaluate-model-performance/8.png)

8. ábra Kereszt-ellenőrzése egy bináris osztályozási modell.

![Kereszt-ellenőrzési eredmények egy bináris osztályozó](./media/evaluate-model-performance/9.png)

9. ábra Bináris osztályozó kereszt-ellenőrzési eredményeit.

## <a name="evaluating-a-multiclass-classification-model"></a>Többosztályos osztályozási modell értékelése
Az ezzel a kísérlettel azt fogja használni a népszerű [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") 3 különböző típusú (osztály) az iris üzem példányait tartalmazó adatkészletet. 4 funkció értékek vannak (csészelevél hossza/szélességét és szirom hossza/szélessége) minden példány esetében. Az előző kísérletek hozunk betanított és a modellek az azonos adatkészleteket használó tesztelése. Itt, ezzel a [Split Data] [ split] létrehozása a data 2 részhalmazainak, az első, betanítását és pontozását és kiértékelheti a második modult. Az Írisz adatkészletet a rendszer a [UCI Machine Learning-tárház](http://archive.ics.uci.edu/ml/index.html), és segítségével lehet letölteni egy [adatok importálása] [ import-data] modul.

### <a name="creating-the-experiment"></a>A kísérlet létrehozása
Az Azure Machine Learning Studio-munkaterülethez adja hozzá a következő modulok:

* [Adatok importálása][import-data]
* [Osztályú döntési erdő][multiclass-decision-forest]
* [Adatok felosztása][split]
* [Tanítási modell][train-model]
* [Pontszám modell][score-model]
* [Modell értékelése][evaluate-model]

Csatlakozás a portok, a 10. ábra alább látható módon.

A Label oszlopindex, állítsa be a [tanítási modell] [ train-model] modul az 5. Az adatkészlet nem fejlécsor rendelkezik, de tudjuk, hogy osztály címkéket, az ötödik oszlop.

Kattintson a a [adatok importálása] [ import-data] modul, és állítsa be a *adatforrás* tulajdonságot *webes URL-cím a HTTP Protokollon keresztül*, és a *URL* való http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Példányok képzési használandó értékének beállítása a [Split Data] [ split] modul (például 0,7).

![A Multiclass osztályozó kiértékelése](./media/evaluate-model-performance/10.png)

10. ábra A Multiclass osztályozó kiértékelése

### <a name="inspecting-the-evaluation-results"></a>A kiértékelés eredményeinek vizsgálata
Futtassa a kísérletet, majd kattintson a kimeneti portjával [Evaluate Model][evaluate-model]. Az értékelési eredmények ebben az esetben egy keveredési mátrixot formájában mutatnak. A mátrix tartalmazza a tényleges és az összes 3 osztályok példányok előre jelzett száma.

![Többosztályos osztályozási kiértékelésének eredménye](./media/evaluate-model-performance/11.png)

11. ábra Többosztályos osztályozási kiértékelésének eredménye.

### <a name="using-cross-validation"></a>Közötti érvényesítése
Ahogy korábban említettük, végezhet ismételt betanítási, pontozási és értékelések használatával automatikusan a [Halmazokra modell] [ cross-validate-model] modul. Kell egy adatkészletet, egy kellő modell, és a egy [Halmazokra modell] [ cross-validate-model] modul (lásd az alábbi ábrát). Újra be kell állítania a címke oszlopban található a [Halmazokra modell] [ cross-validate-model] modul (oszlopindex 5 ebben az esetben). A kísérlet futtatásával, és kattintson a jobb oldalon kimeneti portjára, miután a [Halmazokra modell][cross-validate-model], vizsgálhatja meg az egyes modellrészek, valamint a mean- és standard szórása a metrikaértékek. Az itt látható metrikák hasonlóak a bináris osztályozási esetben tárgyalt azokat. Vegye azonban figyelembe, hogy a többosztályos osztályozási számítási a valódi pozitív vagy negatív és a hamis pozitív vagy negatív végzi el leltározási osztály alapon, nincs átfogó pozitív vagy negatív osztály nem. Például a pontosság vagy az "Iris-setosa" osztály visszaírási számítási feladatokat, ha feltételezzük, hogy ez az a pozitív és minden más negatív.

![Többosztályos osztályozási modell közötti érvényesítése](./media/evaluate-model-performance/12.png)

12. ábra. Kereszt-ellenőrzése egy Többosztályos osztályozási modell.

![Kereszt-ellenőrzési eredmények Többosztályos osztályozási modell](./media/evaluate-model-performance/13.png)

13. ábra. Kereszt-ellenőrzési eredmények Többosztályos osztályozási modell.

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

