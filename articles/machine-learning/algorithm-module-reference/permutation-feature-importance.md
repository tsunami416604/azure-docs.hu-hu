---
title: 'A permutáció funkció fontossága: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a permutáció funkció fontossági modulját Azure Machine Learningban, hogy kiszámítsa a funkció változóinak fontossági sorrendjét a betanított modell és a tesztelési adatkészlet alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: 8611abd4e504e0378b744b12d0adb5fa22f5476b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920365"
---
# <a name="permutation-feature-importance"></a>A permutáció funkció fontossága

Ez a cikk azt ismerteti, hogyan használható a permutáció funkció fontossági modulja Azure Machine Learning Designerben (előzetes verzió), hogy számításba lehessen venni az adatkészlet szolgáltatásbeli fontossági pontszámait. Ezekkel a pontszámokkal meghatározhatja a modellben használandó legjobb szolgáltatásokat.

Ebben a modulban a szolgáltatások értékeit véletlenszerűen rendezi a rendszer, egyszerre egy oszlopot. A modell teljesítményének mérése előtt és után történik. A teljesítmény méréséhez kiválaszthatja a standard mérőszámok egyikét.

A modul által visszaadott pontszám a betanított modell teljesítményének *változását* szemlélteti a permutáció után. A fontos funkciók általában érzékenyebbek a csoszogó folyamatra, így magasabb jelentőségű pontszámokat eredményeznek. 

Ez a cikk áttekintést nyújt a permutációs szolgáltatásról, annak elméleti alapjairól és alkalmazásairól a Machine learningben: [permutáció funkció fontossága](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>A permutáció szolgáltatás fontosságának használata

A szolgáltatási pontszámok létrehozása megköveteli, hogy már betanított modellel, valamint egy tesztelési adatkészlettel rendelkezzen.  

1.  Adja hozzá a permutáció funkció fontossági modulját a folyamathoz. Ezt a modult a **szolgáltatás kiválasztási** kategóriájában találja. 

2.  Csatlakoztasson egy betanított modellt a bal oldali bemenethez. A modellnek egy regressziós modellnek vagy egy besorolási modellnek kell lennie.  

3.  A jobb oldali bemeneten csatlakoztasson egy adatkészletet. Lehetőleg válasszon egyet a modell tanításához használt adatkészlettől. Ez az adatkészlet a betanított modellen alapuló pontozásra szolgál. A modell kiértékelésére is használatos a funkció értékének megváltozása után.  

4.  **Véletlenszerű vetőmag**esetén adjon meg egy értéket, amelyet a rendszer a véletlenszerű vetőmagként használ. Ha 0 (alapértelmezett) értéket ad meg, a rendszer egy számot hoz létre a rendszeróra alapján.

     A mag értéke nem kötelező, de meg kell adnia egy értéket, ha azt szeretné, hogy a reprodukálhatóság az adott folyamaton belül fut.  

5.  A **teljesítmény mérésére szolgáló metrika**esetében válassza ki azt a metrikát, amelyet a modell minőségének a permutáció utáni kiszámításához kell használni.  

     A Azure Machine Learning Designer a következő mérőszámokat támogatja, attól függően, hogy egy besorolási vagy regressziós modellt értékel-e:  

    -   **Classification** (Osztályozás)

        Pontosság, pontosság, visszahívás  

    -   **Regressziós**

        Pontosság, visszahívás, átlagos abszolút hiba, legfelső szintű négyzetes hiba, relatív abszolút hiba, relatív négyzetes hiba, a meghatározás együtthatója  

     A kiértékelési mérőszámok részletes ismertetését és azok kiszámításának módját lásd: [modell kiértékelése](evaluate-model.md).  

6.  A folyamat futtatása.  

7.  A modul megjeleníti a szolgáltatások oszlopainak listáját, valamint a hozzájuk tartozó pontszámokat. A lista a pontszámok csökkenő sorrendjében van rangsorolva.  


##  <a name="technical-notes"></a>Technikai megjegyzések

A permutáció funkció fontossága úgy működik, hogy véletlenszerűen megváltoztatja az egyes szolgáltatások oszlopainak értékeit, egyszerre egy oszlopot. Ezután kiértékeli a modellt. 

A modul által biztosított rangsorok gyakran eltérnek a [szűrésen alapuló funkció kiválasztásának](filter-based-feature-selection.md)lépéseitől. A szűrésen alapuló funkció kiválasztása a modell létrehozása *előtt* számítja ki a pontszámokat. 

A különbség oka, hogy a permutáció funkció fontossága nem méri a szolgáltatás és a célérték közötti társítást. Ehelyett rögzíti, hogy az egyes szolgáltatások milyen mértékben befolyásolják a modell előrejelzéseit.
  
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
