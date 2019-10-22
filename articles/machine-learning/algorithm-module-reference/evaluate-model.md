---
title: 'Modell kiértékelése: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a modell kiértékelése modult Azure Machine Learning szolgáltatásban a betanított modell pontosságának méréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 0ad4ceedf9c1d65339c9e4aabebc0a47475ed568
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693800"
---
# <a name="evaluate-model-module"></a>Modell modul kiértékelése

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal mérhető a betanított modell pontossága. Olyan adatkészletet ad meg, amely egy modellből generált pontszámokat tartalmaz, és a **modell kiértékelése** modul az iparági szabványnak megfelelő értékelési metrikákat számítja ki.
  
 A **kiértékelési modell** által visszaadott mérőszámok az Ön által kiértékelt modell típusától függenek:  
  
-   **Besorolási modellek**    
-   **Regressziós modellek**    



> [!TIP]
> Ha még nem ismeri a modell értékelését, javasoljuk, hogy Dr. Stephen Elston a videó sorozatot a EdX [Machine learning tanfolyamának](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) részeként. 


Háromféle módon használhatja a **modell kiértékelése** modult:

+ Pontszámok előállítása a betanítási adataival, és a modell kiértékelése a pontszámok alapján
+ Pontszámok létrehozása a modellhez, de a pontszámok összevetése egy foglalt tesztelési csoport pontszámai alapján
+ A pontszámok összehasonlítása két különböző, de kapcsolódó modell esetében ugyanazon adathalmaz használatával

## <a name="use-the-training-data"></a>A betanítási adatkészletek használata

A modellek kiértékeléséhez össze kell kapcsolni egy adatkészletet, amely tartalmazza a bemeneti oszlopokat és a pontszámokat.  Ha nem áll rendelkezésre más adat, használhatja az eredeti adatkészletet.

1. Kapcsolja össze a [pontszám modell](./score-model.md) **pontszám** -kimenetét a **modell kiértékelésének**bemenetével. 
2. Kattintson a **modell kiértékelése** elemre, és futtassa a folyamatot a kiértékelési pontszámok létrehozásához.

## <a name="use-testing-data"></a>Tesztelési célú adatfeldolgozás

A gépi tanulás gyakori forgatókönyve, hogy elkülöníti az eredeti adatkészletet képzési és tesztelési adatkészletekben, [a felosztott modul vagy](./split-data.md) a [partíció és a minta](./partition-and-sample.md) modul használatával. 

1. Kapcsolja össze a [pontszám modell](score-model.md) **pontszám** -kimenetét a **modell kiértékelésének**bemenetével. 
2. Kösse össze a **kiértékelési modell**jobb oldali bemenetének tesztelési adatokat tartalmazó felosztott adat-modul kimenetét.
2. Kattintson a **modell kiértékelése** elemre, és válassza a **kijelölt futtatása** lehetőséget a próbaverziók létrehozásához.

## <a name="compare-scores-from-two-models"></a>Pontszámok összehasonlítása két modellből

A **modell kiértékeléséhez**a pontszámok második készletét is összekapcsolhatjuk.  Előfordulhat, hogy a pontszámok olyan közös értékelési készletek, amelyek ismert eredményekkel rendelkeznek, vagy egy másik modellből származó eredmények egy részét ugyanarra az adatokra vonatkozóan.

Ez a funkció azért hasznos, mert egyszerűen össze lehet hasonlítani két különböző modell eredményeit ugyanazon adatokon. Másik lehetőségként összehasonlíthatja azokat a két különböző futtatásból származó pontszámokat, amelyek különböző paraméterekkel rendelkeznek.

1. Kapcsolja össze a [pontszám modell](score-model.md) **pontszám** -kimenetét a **modell kiértékelésének**bemenetével. 
2. Kapcsolja össze a pontszám modell modul kimenetét a második modellhez a **modell kiértékelése**jobb oldali bemenetével.
3. Kattintson a jobb gombbal a **modell kiértékelése**lehetőségre, és válassza a **kijelölt futtatása** lehetőséget a próbaverziók létrehozásához.

## <a name="results"></a>Eredmények

A **kiértékelési modell**futtatása után kattintson a jobb gombbal a modulra, és válassza a **kiértékelési eredmények** lehetőséget az eredmények megtekintéséhez. Előnyök:

+ Az eredmények mentése adatkészletként, így könnyebben elemezhető más eszközökkel
+ Vizualizáció létrehozása a felületen

Ha adatkészleteket hoz létre a **kiértékelési modell**mindkét bemenetéhez, akkor az eredmények mindkét adathalmazra, vagy mindkét modellre vonatkozó metrikákat tartalmaznak.
A bal oldali porthoz csatolt modellnek vagy adatoknak először a jelentésben kell megjelenni, amelyet a DataSet adatkészlethez tartozó metrikák, illetve a jobb oldali porthoz csatolt modell mutat.  

Az alábbi ábrán például az azonos adatokra épülő két fürtözött modell eredményeinek összehasonlítása, de különböző paraméterekkel.  

![PÉNZMOSÁS&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Mivel ez egy fürtözési modell, a kiértékelési eredmények eltérnek, mint ha két regressziós modellből származó pontszámokat hasonlítanak össze, vagy két besorolási modellel hasonlították össze. A teljes bemutató azonban ugyanaz. 

## <a name="metrics"></a>Metrikák

Ez a szakasz a **kiértékelési modellel**használható, adott típusú modellek által visszaadott mérőszámokat ismerteti:

+ [besorolási modellek](#bkmk_classification)
+ [regressziós modellek](#bkmk_regression)

###  <a name="bkmk_classification"></a>Besorolási modellek metrikái

A következő metrikákat kell jelenteni a besorolási modellek kiértékelése során. Ha összehasonlítja a modelleket, azokat a kiértékeléshez kiválasztott metrika rangsorolja.  
  
-   A **pontosság** a besorolási modell jóságát méri az igaz eredményeknek az összes esethez viszonyított arányában.  
  
-   A **pontosság** a valódi eredmények aránya az összes pozitív eredménynél.  
  
-   A **visszahívás** a modell által visszaadott összes helyes eredmény hányada.  
  
-   Az **f-score** kiszámítása a pontosság súlyozott átlagát és a 0 és 1 közötti visszahívást, ahol az ideális F-score értéke 1.  
  
-   A **AUC** méri a görbe alatti terület kirajzolását az y tengelyen található igaz pozitív értékkel, az x tengelyen pedig téves pozitív értéket. Ez a metrika azért hasznos, mert egyetlen számot biztosít, amely lehetővé teszi különböző típusú modellek összehasonlítását.  
  
- Az **átlagos napló elvesztése** egy olyan pontszám, amely a helytelen eredményekre vonatkozó büntetés kiértékelésére szolgál. A számítás a két valószínűségi eloszlás közötti különbség – igaz, és a modellben szereplő egyik.  
  
- A **betanítási naplók elvesztésének** egyetlen pontszáma, amely az osztályozó előnyeit jelöli véletlenszerű előrejelzéssel. A napló elvesztése a modell bizonytalanságát méri, ha összehasonlítja az általa az ismert értékekre (a talajjal igaz) vonatkozó valószínűségeket a címkékben. A modell egészére vonatkozóan szeretné csökkenteni a napló elvesztését.

##  <a name="bkmk_regression"></a>Regressziós modellek metrikái
 
A regressziós modellekhez visszaadott metrikák általában a hibák mennyiségének becslésére lettek kialakítva.  A modell akkor tekinthető megfelelőnek, ha a megfigyelt és az előre jelzett értékek közötti különbség kicsi. A maradékok mintázatának (az egy előre jelzett pont és a hozzá tartozó tényleges érték közötti különbség) megvizsgálása azonban sokat jelenthet a modell lehetséges torzításával kapcsolatban.  
  
 A regressziós modellek kiértékeléséhez a következő metrikákat kell jelenteni. A modellek összehasonlításakor a rendszer a kiértékeléshez kiválasztott mérőszám alapján rangsorolja azokat.  
  
- Az **átlagos abszolút hiba (Mae)** méri, hogy az előrejelzések hogyan zárulnak le a tényleges eredményekhez képest. így jobb az alacsonyabb pontszám.  
  
- A **legfelső szintű középre állított hiba (gyökátlagos)** egyetlen értéket hoz létre, amely összegzi a modellben található hibát. A különbség négyszögesítése a metrika figyelmen kívül hagyja a túlzott előrejelzés és az előrejelzés közötti különbséget.  
  
- **Relatív abszolút hiba (Rae)** a várt és a tényleges értékek közötti relatív abszolút különbség; relatív, mert az átlagos különbség a számtani középérték alapján oszlik meg.  
  
- A **relatív négyzetes hiba (RSE)** hasonló módon normalizálja az előre jelzett értékek teljes négyzetes hibáját azáltal, hogy a tényleges értékek teljes négyzetes hibáját választja.  
  
- **Nulla egy hiba (MZOE)** azt jelzi, hogy az előrejelzés helyes-e vagy sem.  Más szóval: `ZeroOneLoss(x,y) = 1`, ha `x!=y`; Ellenkező esetben `0`.
  
- A **meghatározási együttható**(más néven R<sup>2</sup>) a modell prediktív erejét mutatja 0 és 1 közötti értékként. Nulla érték azt jelenti, hogy a modell véletlenszerű (semmit sem jelent); 1 a tökéletes illeszkedést jelenti. Azonban körültekintően kell használni az R<sup>2</sup> értékek értelmezését, mivel az alacsony értékek teljesen normálisak lehetnek, és a magas értékek gyanúja is lehet.
  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 