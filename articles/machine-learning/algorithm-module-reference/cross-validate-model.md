---
title: 'Keresztérvényesítési modell: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a több-érvényesítési modell modul az Azure Machine Learning az adatok particionálásával kereszt-érvényesítési paraméter becslések besorolási vagy regressziós modellek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477646"
---
# <a name="cross-validate-model"></a>Modell keresztellenőrzése

Ez a cikk ismerteti, hogyan használhatja a többmintaérvényesítési modul az Azure Machine Learning designer (előzetes verzió). *A keresztérvényesítés* olyan módszer, amelyet gyakran használnak a gépi tanulásban egy adatkészlet variabilitásának és az adatokon keresztül betanított modellek megbízhatóságának felmérésére.  

A Több modell érvényesítése modul egy címkézett adatkészletbemenetként, egy nem képzetlen besorolási vagy regressziós modell. Az adatkészletet néhány részhalmazra *(hajtásra)* osztja fel, minden egyes hajtásra létrehoz egy modellt, majd minden egyes hajtáshoz pontossági statisztikát ad vissza. Az összes redő pontossági statisztikájának összehasonlításával értelmezheti az adatkészlet minőségét. Ezután megismerheti, hogy a modell érzékeny-e az adatok változásaira.  

A Keresztérvényesítési modell is visszaadja az adatkészlet előre jelzett eredményeit és valószínűségjeit, így felmérheti az előrejelzések megbízhatóságát.  

### <a name="how-cross-validation-works"></a>A keresztérvényesítés működése

1. A keresztérvényesítés véletlenszerűen osztja a betanítási adatokat hajtásokra. 

   Az algoritmus alapértelmezés szerint 10-szeres, ha korábban még nem particionált az adatkészletet. Az adatkészlet felosztásához különböző számú redők, használhatja a [Partíció és minta](partition-and-sample.md) modult, és adja meg, hogy hány redők használata.  

2.  A modul az 1-es hajtásadatait az érvényesítéshez használja. (Ez néha a *holdout fold*.) A modul a fennmaradó hajtásokat használja a modell betanításához. 

    Ha például öt hajtást hoz létre, a modul öt modellt hoz létre a keresztérvényesítés során. A modul az adatok négyötödének használatával képzi be az egyes modelleket. Minden modellt a fennmaradó egyötödön tesztel.  

3.  A modell tesztelése során minden hajtás, a modul kiértékeli a több pontossági statisztikák. A modul által használt statisztikák a kiértékelt modell típusától függnek. Különböző statisztikák osztályozási modellek és regressziós modellek kiértékelésére szolgálnak.  

4.  Amikor az építési és értékelési folyamat befejeződött az összes redők, Cross Validate Model generál egy sor teljesítmény mérőszámok és a pontozott eredmények az összes adatot. Tekintse át ezeket a mutatókat, és állapítsa meg, hogy egyetlen hajtás nagy vagy alacsony pontosságú-e. 

### <a name="advantages-of-cross-validation"></a>A keresztérvényesítés előnyei

Egy másik és gyakori módja a modell kiértékelése az adatok felosztása egy betanítási és tesztelési készlet segítségével [Split Data,](split-data.md)majd érvényesítse a modell a betanítási adatok. De a kereszt-érvényesítés kínál néhány előnye van:  

-   A keresztellenőrzés több tesztadatot használ.

    Kereszt-érvényesítés méri a modell teljesítményét a megadott paramétereket egy nagyobb adattérben. Ez azt, hogy a kereszt-ellenőrzés a teljes betanítási adatkészletet használja betanítási és kiértékeléshez egy rész helyett. Ezzel szemben, ha egy modell tvéletlenszerű felosztásból létrehozott adatok alapján érvényesíti a modellt, általában csak a rendelkezésre álló adatok 30 százalékán vagy annál kevesebben értékeli ki a modellt.  

    Mivel azonban a keresztérvényesítés vonatok és érvényesíti a modell többször egy nagyobb adatkészleten keresztül, ez sokkal több számításilag intenzívebb. Sokkal tovább tart, mint egy véletlenszerű felosztás érvényesítése.  

-   Kereszt-ellenőrzés kiértékeli mind az adatkészletet, és a modell.

    A keresztérvényesítés nem egyszerűen méri a modell pontosságát. Azt is ad néhány ötletet, hogy mennyire reprezentatív az adatkészlet, és milyen érzékeny lehet a modell az adatok változásait.  

## <a name="how-to-use-cross-validate-model"></a>A Keresztérvényesítési modell használata

A keresztellenőrzés futtatása hosszú időt vehet igénybe, ha az adatkészlet nagy.  Így előfordulhat, hogy a keresztérvényesítési modell a modell létrehozásának és tesztelésének kezdeti szakaszában. Ebben a fázisban kiértékelheti a modell paraméterek jóságát (feltételezve, hogy a számítási idő elviselhető). Ezután betaníthatja és kiértékelheti a modellt a megadott paraméterek használatával a [Betanítási modell](train-model.md) és [a Modell kiértékelése](evaluate-model.md) modulokkal.

Ebben a forgatókönyvben a modell betanítása és tesztelése a Keresztérvényesítési modell használatával.

1. Adja hozzá a Keresztérvényesítési modell modult a folyamathoz. Megtalálhatja az Azure Machine Learning tervező, a **modell pontozási & kiértékelés** kategóriában. 

2. Csatlakoztassa bármely besorolási vagy regressziós modell kimenetét. 

    Ha például **két osztálykiemelt döntési fát** használ a besoroláshoz, konfigurálja a modellt a kívánt paraméterekkel. Ezután húzza az összekötőt az osztályozó **Képzetlen** modellportjából a Keresztérvényesítési modell megfelelő portjára. 

    > [!TIP] 
    > Nem kell betanítani a modellt, mert keresztérvényesítési modell automatikusan betanítja a modellt a kiértékelés részeként.  
3.  A keresztérvényesítési modell **adatkészletportján** csatlakoztassa a címkézett betanítási adatkészletet.  

4.  A Keresztérvényesítési modell jobb oldali paneljén kattintson az **Oszlop szerkesztése gombra.** Jelölje ki az osztálycímkét tartalmazó egyetlen oszlopot vagy a kiszámítható értéket. 

5. Állítson be egy értéket a **Véletlen mag** paraméterhez, ha meg szeretné ismételni a keresztérvényesítés eredményeit ugyanazon az adat on egymást követő futtatások során.  

6. Küldje el a folyamatot.

7. A jelentések leírását az [Eredmények](#results) szakaszban található.

## <a name="results"></a>Results (Eredmények)

Miután az összes iteráció befejeződött, a Keresztérvényesítési modell pontszámokat hoz létre a teljes adatkészlethez. Emellett teljesítmény-mutatókat is létrehoz, amelyek segítségével felmérheti a modell minőségét.

### <a name="scored-results"></a>Pontozott eredmények

A modul első kimenete biztosítja az egyes sorok forrásadatait, néhány előre jelzett értékkel és a kapcsolódó valószínűségekkel együtt. 

Az eredmények megtekintéséhez a folyamatban kattintson a jobb gombbal a Több minta érvényesítése modulra. Válassza a **Pontozott eredmények megjelenítése**lehetőséget.

| Új oszlop neve      | Leírás                              |
| -------------------- | ---------------------------------------- |
| Pontozott címkék        | Ez az oszlop az adatkészlet végén jelenik meg. Az egyes sorok előre jelzett értékét tartalmazza. |
| Pontozott valószínűségek | Ez az oszlop az adatkészlet végén jelenik meg. A **pontozott címkékben**lévő érték becsült valószínűségét jelzi. |
| Szám hajtása          | Annak a hajtásnak a nulla alapú indexét jelzi, amelyhez az egyes adatsorokat a keresztellenőrzés során hozzárendelték. |

 ### <a name="evaluation-results"></a>A kiértékelés eredménye

A második jelentés hajtások szerint van csoportosítva. Ne feledje, hogy a végrehajtás során a Keresztérvényesítési modell véletlenszerűen osztja fel a betanítási adatokat *n* redőkre (alapértelmezés szerint 10). Az adatkészlet minden egyes iterációjában a Keresztérvényesítési modell egy hajtást használ érvényesítési adatkészletként. A fennmaradó *n-1* redők segítségével tanítja be a modellt. Az *n* modellek mindegyikét az összes többi hajtás adataival összehasonlítva tesztelik.

Ebben a jelentésben a ráncok indexérték szerint, növekvő sorrendben vannak felsorolva.  Ha bármely más oszlopban szeretne rendelni, az eredményeket adatkészletként mentheti.

Az eredmények megtekintéséhez a folyamatban kattintson a jobb gombbal a Több minta érvényesítése modulra. Válassza a **Kiértékelés eredményeinek megjelenítése a behajtással lehetőséget.**


|Oszlop neve| Leírás|
|----|----|
|Hajtási szám| Minden hajtásazonosító. Ha öt hajtást hozna létre, akkor öt adathalmaz lenne, 0-tól 4-ig számozott.
|Példák száma a hajtásban|Az egyes hajtásokhoz rendelt sorok száma. Nagyjából egyenlőnek kell lenniük. |


A modul a következő mutatókat is tartalmazza az egyes hajtásokhoz, a kiértékelendő modell típusától függően: 

+ **Osztályozási modellek**: Pontosság, visszahívás, F-pontszám, AUC, pontosság  

+ **Regressziós modellek:** Átlagos abszolút hiba, gyökalakú négyzetes hiba, relatív abszolút hiba, relatív négyzetes hiba és meghatározási együttható


## <a name="technical-notes"></a>Technikai megjegyzések  

+ Ajánlott rendszer, ha az adatkészleteket a keresztellenőrzéshez való felhasználás előtt normalizálja. 

+ A keresztérvényesítési modell sokkal nagyobb számítási lag- és hosszabb időt vesz igénybe, mint ha véletlenszerűen megosztott adatkészlet használatával érvényesítette volna a modellt. Ennek az az oka, hogy a keresztérvényesítési modell vonatok és érvényesíti a modell többször.

+ Nincs szükség az adatkészlet betanítási és tesztelési készletekre való felosztására, ha keresztellenőrzéssel méri a modell pontosságát. 


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 

