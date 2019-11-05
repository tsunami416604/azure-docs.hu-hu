---
title: 'A permutáció funkció fontossága: modul leírása'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a permutáció funkció fontossági modulját Azure Machine Learning szolgáltatásban, hogy kiszámítsa a betanított modellt és a tesztelési adatkészletet a funkció változóinak fontossági pontjaiban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517942"
---
# <a name="permutation-feature-importance"></a>A permutáció funkció fontossága

Ez a cikk azt ismerteti, hogyan használható a [permutáció funkció fontossági](permutation-feature-importance.md) modulja Azure Machine learning Designerben (előzetes verzió), hogy számításba lehessen venni az adatkészlet szolgáltatásbeli fontossági pontszámait. Ezekkel a pontszámokkal meghatározhatja a modellben használandó legjobb szolgáltatásokat.

Ebben a modulban a szolgáltatások értékeit véletlenszerűen rendezi a rendszer, egyszerre egy oszlopot, a modell teljesítményét pedig az előtt és után méri. Kiválaszthatja a teljesítmény méréséhez megadott szabványos mérőszámok egyikét.

A modul által visszaadott pontszám a betanított modell teljesítményének **változását** szemlélteti a permutáció után. A fontos funkciók általában érzékenyebbek a csoszogó folyamatra, így magasabb jelentőségű pontszámokat eredményeznek. 

Ez a cikk áttekintést nyújt a permutációs funkciók fontosságáról, elméleti alapjairól és alkalmazásairól a Machine learningben: [permutáció funkció fontossága](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>A permutáció szolgáltatás fontosságának használata

A szolgáltatási pontszámok létrehozásához már betanított modellre, valamint egy tesztelési adatkészletre van szükség.  

1.  Adja hozzá a **permutáció funkció fontossági** modulját a folyamathoz. Ezt a modult a **szolgáltatás kiválasztási** kategóriájában találja. 

2.  Csatlakoztasson egy betanított modellt a bal oldali bemenethez. A modellnek regressziós modellnek vagy besorolási modellnek kell lennie.  

3.  A megfelelő bemeneten csatlakoztasson egy adatkészletet, lehetőleg az egyiket, amely eltér a modell betanításához használt adatkészlettől. Ez az adatkészlet a betanított modellen alapuló pontozásra szolgál, valamint a modell kiértékeléséhez a funkció értékeinek módosítása után.  

4.  A **véletlenszerű magok**esetében adjon meg egy értéket, amelyet a rendszer a magok véletlenszerű felhasználására használ. Ha 0 (alapértelmezett) értéket ad meg, a rendszer egy számot hoz létre a rendszeróra alapján.

     A mag értéke nem kötelező, de meg kell adnia egy értéket, ha azt szeretné, hogy a reprodukálhatóság az adott folyamaton belül fut.  

5.  A **teljesítmény mérésére szolgáló metrika**esetében válassza ki azt a metrikát, amelyet a rendszer a modell minőségének kiszámításához használ a permutáció után.  

     A Azure Machine Learning Designer a következő mérőszámokat támogatja, attól függően, hogy egy besorolási vagy regressziós modellt értékel ki:  

    -   **Classification** (Osztályozás)

        Pontosság, pontosság, visszahívás, átlagos naplózási veszteség  

    -   **Regressziós**

        Pontosság, visszahívás, átlagos abszolút hiba, legfelső szintű négyzetes hiba, relatív abszolút hiba, relatív négyzetes hiba, a meghatározás együtthatója  

     A kiértékelési mérőszámok részletes ismertetését és azok kiszámításának módját lásd: [modell kiértékelése](evaluate-model.md).  

6.  A folyamat futtatása.  

7.  A modul megjeleníti a szolgáltatások oszlopainak listáját és a hozzájuk társított pontszámokat, rangsorolva a pontszámok sorrendjét, csökkenő sorrendben.  


##  <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz a megvalósítás részleteit, a tippeket és a gyakran feltett kérdésekre adott válaszokat tartalmazza.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Hogyan hasonlítja össze ezt a funkciót más funkciók kiválasztási módszereivel?

A permutáció funkció fontossága úgy működik, hogy véletlenszerűen megváltoztatja az egyes szolgáltatások oszlopainak értékeit, egyszerre egy oszlopot, majd kiértékeli a modellt. 

A permutáció funkció fontossága által biztosított rangsorok gyakran eltérnek a [szűrők alapján kiválasztott funkcióktól](filter-based-feature-selection.md), amely a modell létrehozása **előtt** kiszámítja a pontszámokat. 

Ennek az az oka, hogy a permutáció funkció fontossága nem méri a szolgáltatás és a célérték közötti társítást, hanem rögzíti, hogy az egyes szolgáltatások milyen mértékben befolyásolják a modell előrejelzéseit.
  
## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 
