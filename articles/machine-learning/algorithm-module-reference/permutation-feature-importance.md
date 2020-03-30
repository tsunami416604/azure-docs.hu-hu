---
title: 'Permutációs szolgáltatás fontossága: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Permutációs szolgáltatás fontossági modult az Azure Machine Learningben a szolgáltatásváltozók permutációs funkciófontossági pontszámainak kiszámításához egy betanított modell és egy tesztadatkészlet esetén.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456063"
---
# <a name="permutation-feature-importance"></a>A permutáció funkció fontossága

Ez a cikk ismerteti, hogyan használhatja a Permutációs szolgáltatásfontosság modul az Azure Machine Learning designer (előzetes verzió), az adatkészlet szolgáltatásfontossági pontszámok kiszámításához. Ezeket a pontszámokat arra használhatja, hogy segítsen meghatározni a modellben a legjobb funkciókat.

Ebben a modulban a jellemzőértékek véletlenszerűen kerülnek véletlensorrendbe, oszloponként. A modell teljesítményét előtte és utána mérik. A teljesítmény méréséhez válasszon egyet a szabványos metrikák közül.

A modul által visszaadott pontszámok a betanított modell teljesítményének *változását* jelentik a permutáció után. A fontos funkciók általában érzékenyebbek a keverési folyamatra, így nagyobb fontossági pontszámokat eredményeznek. 

Ez a cikk áttekintést nyújt a permutációs funkcióról, annak elméleti alapjairól és a gépi tanulásban alkalmazott [alkalmazásokról: Permutációs funkciófontosság](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>A Permutációs funkció fontosságának használata

A szolgáltatás pontszámok készletének létrehozásához egy már betanított modell, valamint egy teszt adatkészlet szükséges.  

1.  Adja hozzá a Permutációs szolgáltatás fontossági modulját a folyamathoz. Ezt a modult a **Szolgáltatáskiválasztása** kategóriában találja. 

2.  Betanított modell csatlakoztatása a bal oldali bemenethez. A modellnek regressziós modellnek vagy besorolási modellnek kell lennie.  

3.  A megfelelő beviteli területen csatlakoztasson egy adatkészletet. Lehetőleg válasszon egyet, amely eltér a modell betanításához használt adatkészlettől. Ez az adatkészlet a betanított modell alapján történő pontozáshoz használatos. Azt is használják a modell kiértékelésére, miután a jellemzőértékek megváltoztak.  

4.  A **Véletlenszerű vetőmag**esetében adja meg a randomizáláshoz használt értéket. Ha a 0 értéket adja meg (ez az alapértelmezett), a rendszeróra alapján szám jön létre.

     A kezdőérték nem kötelező, de meg kell adnia egy értéket, ha reprodukálhatóságot szeretne ugyanazon folyamat futtatásai között.  

5.  A **metrika a teljesítmény méréséhez**válasszon ki egy metrikát, amelyet a modell minőségének permutáció utáni kiszámításakor kell használnia.  

     Az Azure Machine Learning tervezője a következő metrikákat támogatja attól függően, hogy besorolási vagy regressziós modellt értékel-e:  

    -   **Classification** (Osztályozás)

        Pontosság, Pontosság, Visszahívás  

    -   **Regresszió**

        Pontosság, Visszahívás, Átlagos abszolút hiba, Gyökátlagos négyzethiba, Relatív abszolút hiba, Relatív négyzethiba, Meghatározási együttható  

     Ezeknek a értékelési mutatóknak és számításuk módjának részletesebb leírását a Modell kiértékelése című témakörben tetszésben [tetszhet.](evaluate-model.md)  

6.  Küldje el a folyamatot.  

7.  A modul a jellemzőoszlopok listáját és a hozzájuk társított pontszámokat adja ki. A lista a pontszámok csökkenő sorrendjében van rangsorolva.  


##  <a name="technical-notes"></a>Technikai megjegyzések

A permutációs szolgáltatásfontosság úgy működik, hogy véletlenszerűen módosítja az egyes jellemzőoszlopok értékeit, egyszerre egy oszlopot. Ezután kiértékeli a modellt. 

A modul által biztosított rangsorok gyakran eltérnek a [szűrőalapú szolgáltatásválasztásból](filter-based-feature-selection.md)kapott rangsoroktól. A szűrőalapú szolgáltatáskijelölés a modell létrehozása *előtt* kiszámítja a pontszámokat. 

A különbség oka az, hogy a permutációs funkció fontossági funkció nem méri a szolgáltatás és a célérték közötti társítást. Ehelyett azt rögzíti, hogy az egyes funkciók milyen hatással vannak a modell ből származó előrejelzésekre.
  
## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
