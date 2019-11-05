---
title: 'Kereszt-ellenőrzési modell: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban a modell átellenőrzése modult a besorolási vagy regressziós modellekre vonatkozó becslések átállításához az adatparticionálással.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512846"
---
# <a name="cross-validate-model"></a>Kereszt-ellenőrzési modell

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designerben (előzetes verzió) az **Cross validate Model** modul használata. A többértékű *ellenőrzés* a gépi tanulásban gyakran használt, az adatkészletek változékonyságának és az adatokat használó modellek megbízhatóságának felmérése.  

A **modell keresztbe** állítása modul a címkével ellátott adatkészlet bemenetét, valamint egy képzetlen besorolást vagy regressziós modellt használ. Az adatkészletet bizonyos számú részhalmazra osztja (*hajtogatás*), minden egyes foldon létrehoz egy modellt, majd a pontossági statisztikák készletét adja vissza az egyes hajtogatásoknak. Az összes hajtogatás pontossági statisztikájának összehasonlításával értelmezheti az adatkészletet, és megtudhatja, hogy a modell az adatok változásaira is alkalmas-e.  

A kereszthivatkozások az adatkészletek előre jelzett eredményeit és valószínűségeit is visszaadja, így az előrejelzések megbízhatóságát is kiértékelheti.  

### <a name="how-cross-validation-works"></a>A több ellenőrzés működése

1. A több érvényesítés véletlenszerűen osztja el a betanítási adatmennyiséget számos partícióra, más néven *hajtogatásra*. 

    + Az algoritmus alapértelmezett értéke 10, ha korábban még nem particionálta az adatkészletet. 
    + Ha az adatkészletet más számú bedobásra szeretné osztani, használhatja a [partíció és a minta](partition-and-sample.md) modult, és jelezheti, hogy hány hajtogatást használ.  

2.  A modul az 1. számú, az ellenőrzéshez használt adategységet (ezt néha *Holdout*is nevezik), és a fennmaradó ráncokat használja a modell betanításához. 

    Ha például öt betöltést hoz létre, akkor a modul öt modellt hoz létre a kereszt-ellenőrzés során, minden egyes modell 4/5-et használ, és tesztelte a fennmaradó 1/5.  

3.  Az egyes hajtogatási modell tesztelése során a rendszer kiértékeli a több pontossági statisztikát. A használt statisztikák az Ön által kiértékelt modell típusától függenek. A besorolási modellek és a regressziós modellek kiértékelése különböző statisztikákat használ.  

4.  Ha az épület és a kiértékelés folyamata befejeződött az összes betöltésnél, a **kereszt-ellenőrzési modell** teljesítmény-mérőszámokat generál, és az összes adathoz tartozó eredményeket jeleníti meg. Tekintse át ezeket a mérőszámokat, és ellenőrizze, hogy van-e különösen magas vagy alacsony pontosságú egyetlen fold 

### <a name="advantages-of-cross-validation"></a>A több ellenőrzés előnyei

A modell kiértékelésének egy másik és gyakori módja az, hogy az adatmennyiséget egy betanítási és tesztelési csoportba ossza [szét](split-data.md), majd ellenőrzi a modellt a betanítási adaton. A kereszt-ellenőrzés azonban néhány előnyt nyújt:  

-   Az ellenőrzés több tesztet használ.

     A többértékű ellenőrzés a modell teljesítményét a megadott paraméterekkel méri nagyobb adatterületen. Ez azt is megtörténik, hogy a teljes betanítási adatkészletet a képzéshez és az értékeléshez használja, nem pedig egy részét. Ezzel szemben, ha a modellt egy véletlenszerű felosztásból generált adatok használatával érvényesíti, általában a modell kiértékelése csak 30%-os vagy kevesebb rendelkezésre álló adatok alapján történik.  

     Mivel azonban a kereszt-ellenőrzési vonatok és a modell többszöri ellenőrzése egy nagyobb adatkészleten keresztül történik, sokkal nagyobb számítási igényű, és sokkal hosszabb időt vesz igénybe, mint az ellenőrzés véletlenszerű felosztáskor.  

-   A több érvényesítés kiértékeli az adatkészletet és a modellt is.

     Az átellenőrzés nem csupán egy modell pontosságát méri, hanem azt is, hogy az adatkészletnek milyen reprezentatívnak kell lennie, és hogy mennyire érzékeny a modell az adatok változásaira.  

## <a name="how-to-use-cross-validate-model"></a>A Cross validate Model használata

A több érvényesítés hosszú időt is igénybe vehet, ha az adatkészlet nagy méretű.  Ezért a modell kiépítésének és tesztelésének kezdeti fázisában **több ellenőrzési modellt** is használhat a modell paramétereinek kiértékeléséhez (feltéve, hogy a számítási idő tűrhető), majd betanítja és kiértékeli a modellt a a [kiépítési modellel](train-model.md) rendelkező paraméterek és a modell moduljainak [kiértékelése](evaluate-model.md) .

Ebben az esetben a modellt a **Cross Validation Model**használatával is betanítja és teszteli.

1. Adja hozzá a **Cross validate Model** modult a folyamathoz. Megtalálhatja Azure Machine Learning Designerben, a **modell pontozási & próbaverzió** kategóriában. 

2. Bármely **besorolási** vagy **regressziós** modell kimenetének összekötése. 

    Ha például **két osztályból álló Bayes-pontot** használ a besoroláshoz, konfigurálja a modellt a kívánt paraméterekkel, majd húzzon egy összekötőt az osztályozatlan **modell** portjáról a kereszt ellenőrzése megfelelő portra.  **Modell**. 

    > [!TIP] 
    > A modellt nem szükséges betanítani, mert a **kereszt-ellenőrzési modell** automatikusan betanítja a modellt a kiértékelés részeként.  
3.  A **kereszt-ellenőrzési modell** **adatkészletének** portjához csatlakoztasson bármely címkézett betanítási adatkészletet.  

4.  A **kereszt-ellenőrzési modell** **Tulajdonságok** paneljén kattintson az **oszlop kiválasztásának indítása** elemre, és válassza ki azt az egyetlen oszlopot, amely tartalmazza az osztály címkéjét vagy a kiszámítható értéket. 

5. Állítsa be a **véletlenszerű vetőmag** paraméter értékét, ha szeretné megismételni az egymást követő futtatások egymás utáni futtatásainak eredményeit ugyanazon az adategységen.  

6.  A folyamat futtatása.

7. A jelentések leírását a [Results (eredmények](#results) ) című szakaszban találja.

    A modell későbbi újrafelhasználásához kattintson a jobb gombbal az algoritmust tartalmazó modul kimenetére (például a **két osztály Bayes pontjának gépe**), majd kattintson a **Mentés betanított modellként**elemre.

## <a name="results"></a>Results (Eredmények)

Az összes iteráció befejezése után a **Cross validate Model** a teljes adatkészlet pontszámait hozza létre, valamint a modell minőségének értékeléséhez használható teljesítménymutatókat.

### <a name="scored-results"></a>Felhorzsolt eredmények

A modul első kimenete tartalmazza az egyes sorokhoz tartozó forrásadatokat, valamint az előre jelzett értékeket és a kapcsolódó valószínűségeket. 

Ha meg szeretné tekinteni ezeket az eredményeket, a folyamatban kattintson a jobb gombbal a **modell ellenőrzése** elemre, válassza a **pontozásos eredmények**lehetőséget, majd kattintson a **Megjelenítés**elemre.

| Új oszlop neve      | Leírás                              |
| -------------------- | ---------------------------------------- |
| Pontozásos Címkék        | Ezt az oszlopot a rendszer az adatkészlet végén adja hozzá, és az egyes sorok előre jelzett értékét tartalmazza. |
| Pontozásos valószínűségek | Ezt az oszlopot a rendszer az adatkészlet végén adja hozzá, és jelzi az érték becsült valószínűségét a **pontozásos címkékben**. |
| Fold száma          | Azt jelzi, hogy a fold 0 alapú indexe az egyes adatsorokhoz lett rendelve, a kereszt-ellenőrzés során. |

 ### <a name="evaluation-results"></a>A kiértékelés eredménye

A második jelentés hajtogatás szerint van csoportosítva. Ne feledje, hogy a végrehajtás során a **Cross validate Model** véletlenszerűen felosztja a betanítási adatait *n* -ba (alapértelmezés szerint 10). Az adatkészletben lévő minden egyes iteráció során a **Cross validate Model** egy behajtott ellenőrzési adatkészletként használja, és a fennmaradó *n-1* hajtogatást használja a modell betanításához. Az *n* -modellek mindegyike az összes többi karámban található adatra van tesztelve.

Ebben a jelentésben a kidobások index érték szerint, növekvő sorrendben vannak felsorolva.  Bármely más oszlop megrendeléséhez mentheti az eredményeket adatkészletként.

Ha meg szeretné tekinteni ezeket az eredményeket, a folyamatban kattintson a jobb gombbal a **modell ellenőrzése** elemre, válassza ki a **kiértékelési eredmények kidobásával**lehetőséget, majd kattintson a **Megjelenítés**elemre.


|Oszlop neve| Leírás|
|----|----|
|Fold száma| Az egyes összecsukható azonosítók. Ha 5 hajtogatást hozott létre, a 0 és 4 közötti számú adat 5 részhalmaza lesz.
|A kiszorzott példák száma|Az egyes hajtogatáshoz rendelt sorok száma Nagyjából egyenlőnek kell lenniük. |


Emellett a következő metrikák is szerepelnek a kiértékelt modell típusától függően. 

+ **Besorolási modellek**: precizitás, visszahívás, F-pontszám, AUC, pontosság  

+ **Regressziós modellek**: abszolút hiba, legfelső szintű négyzetes hiba, relatív abszolút hiba, relatív négyzetes hiba és a meghatározási együttható


## <a name="technical-notes"></a>Technikai megjegyzések  

+ Az adatkészletek normalizálása az ajánlott eljárás az adathalmazok átellenőrzése előtt. 

+ Mivel a **Cross validate Model** Trains és a modell többször is érvényesítve van, sokkal nagyobb számítási igényű, és hosszabb ideig tart, mint ha a modellt véletlenszerűen felosztott adatkészlet használatával érvényesítette. 

+ Az adatkészletet nem kell kiosztani képzésre és tesztelési csoportokra, ha a modell pontosságának méréséhez kereszt-érvényesítést használ. 


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

