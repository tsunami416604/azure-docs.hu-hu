---
title: Gépi tanulási algoritmus kiválasztása
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-algoritmusok kiválasztása felügyelt és felügyelet nélküli tanuláshoz fürtözési, besorolási vagy regressziós kísérletekben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328663"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Az Azure Machine Learning algoritmusainak kiválasztása

Gyakori kérdés: "Melyik gépi tanulási algoritmust használjam?" A kiválasztott algoritmus elsősorban az adatelemzési forgatókönyv két különböző aspektusától függ:

 - **Mit szeretne tenni az adataival?** Pontosabban, mi az az üzleti kérdés, amit meg szeretne válaszolni a korábbi adatokból való tanulással?

 - **Milyen követelmények az adatelemzési forgatókönyv?** Pontosabban, mi a pontosság, a betanítási idő, linearitás, paraméterek száma és a szolgáltatások száma a megoldás támogatja?

 ![Az algoritmusok kiválasztásának szempontjai: Mit szeretne tudni? Mik a forgatókönyv-követelmények?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Üzleti forgatókönyvek és a Machine Learning algoritmus Cheat Sheet

Az [Azure Machine Learning algoritmus cheat sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) segít az első szempont: Mit szeretne tenni az **adatokkal?** A Machine Learning algoritmus cheat sheet, keresse meg a kívánt feladatot, és keresse meg az [Azure Machine Learning tervezőalgoritmus](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) a prediktív elemzési megoldás. 

A Machine Learning tervezője algoritmusok átfogó portfólióját biztosítja, például [többosztályos döntési erdőt](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [ajánlási rendszereket](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [neurális hálózati regressziót](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [többosztályos neurális hálózatot](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)és [K-Means fürtözést.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) Minden algoritmus célja, hogy egy másik típusú gépi tanulási probléma kezelésére. Tekintse meg a [Machine Learning tervezőalgoritmus és a modul referencia](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) egy teljes listát, valamint dokumentációt arról, hogyan működik az egyes algoritmusok, és hogyan kell hangolni paraméterek optimalizálása az algoritmus.

> [!NOTE]
> A gépi tanulási algoritmus cheat sheet letöltéséhez nyissa meg az [Azure Machine learning algoritmus cheat sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Az Azure Machine Learning algoritmus cheat sheet útmutatásával együtt szem előtt tartani a megoldás gépi tanulási algoritmusának kiválasztásakor egyéb követelmények. A következőkben további figyelembe vesszen tényezőket, például a pontosságot, a betanítási időt, a linearitást, a paraméterek számát és a funkciók számát.

## <a name="additional-requirements-for-a-data-science-scenario"></a>További követelmények egy adatelemzési forgatókönyvhöz

Ha már tudja, hogy mit szeretne tenni az adatokkal, további követelményeket kell meghatároznia a megoldáshoz. 

Hozzon döntéseket és esetleg kompromisszumokat az alábbi követelményeknek megfelelően:

- Pontosság
- Betanítási idő
- Linearitás
- Paraméterek száma
- Szolgáltatások száma

## <a name="accuracy"></a>Pontosság

A gépi tanulás pontossága méri a modell hatékonyságát, mint a valódi eredmények arányát az összes esethez képest. A Machine Learning tervezőjében a [Modell kiértékelése modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) az iparági szabványnak megfelelő értékelési metrikák készletét számítja ki. Ezzel a modullal mérheti a betanított modell pontosságát.

A lehető legpontosabb válasz megszerzése nem mindig szükséges. Néha a közelítés megfelelő, attól függően, hogy mire szeretné használni. Ha ez a helyzet, akkor lehet, hogy csökkentsék a feldolgozási idő drámaian ragasztás több hozzávetőleges módszerek. Hozzávetőleges módszerek is természetesen hajlamosak elkerülni overfitting.

A Modell kiértékelése modult háromféleképpen használhatja:

- Pontszámok generálása a betanítási adatok felett a modell kiértékeléséhez
- Pontszámok létrehozása a modellen, de hasonlítsa össze ezeket a pontszámokat egy fenntartott tesztkészlet pontszámaival
- Két különböző, de kapcsolódó modell pontszámainak összehasonlítása ugyanazon adathalmaz használatával

A gépi tanulási modellek pontosságának kiértékeléséhez használható metrikák és megközelítések teljes listáját a [Modell modul kiértékelése című témakörben](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)található.

## <a name="training-time"></a>Betanítási idő

Felügyelt tanulás, a képzés azt jelenti, hogy a korábbi adatok segítségével hozzon létre egy gépi tanulási modell, amely minimálisra csökkenti a hibákat. A modell betanításához szükséges percek vagy órák száma algoritmusok között nagymértékben változik. Az edzési idő gyakran szorosan kötődik a pontossághoz; az egyik jellemzően kíséri a másikat. 

Emellett egyes algoritmusok érzékenyebbek az adatpontok számára, mint mások. Előfordulhat, hogy egy adott algoritmust választ, mert időkorlátot alkalmaz, különösen akkor, ha az adatkészlet nagy.

A Machine Learning-tervezőben a gépi tanulási modell létrehozása és használata általában három lépésből áll:

1.  Konfiguráljon egy modellt egy adott típusú algoritmus kiválasztásával, majd adja meg annak paramétereit vagy hiperparamétereit. 

2.  Adjon meg egy adatkészletet, amely címkével van ellátva, és az algoritmussal kompatibilis adatokkal rendelkezik. Csatlakoztassa mind az adatokat, mind a modellt a [Betanítási modell modulhoz.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)

3.  A betanítás befejezése után használja a betanított modell t a [pontozási modulok](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) egyikével, hogy előrejelzéseket készítsen az új adatokról.

## <a name="linearity"></a>Linearitás

A statisztika és a gépi tanulás linearitása azt jelenti, hogy lineáris kapcsolat van egy változó és egy állandó között az adatkészletben. A lineáris osztályozási algoritmusok például azt feltételezik, hogy az osztályokat egyenes vonal (vagy annak nagyobb dimenziós analógja) el lehet választani egymástól.

Sok gépi tanulási algoritmus használja a linearitást. Az Azure Machine Learning tervezőjében a következők a következők: 

- [Többosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Kétosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Vektorgépek támogatása](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

A lineáris regressziós algoritmusok feltételezik, hogy az adattrendek egyenes vonalat követnek. Ez a feltételezés nem rossz néhány probléma, de mások számára ez csökkenti a pontosságot. Hátrányaik ellenére a lineáris algoritmusok népszerűek, mint az első stratégia. Ezek általában algoritmikusan egyszerű és gyors a vonat.

![Nemlineáris osztályhatár](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Nemlineáris osztályhatár:*** *A lineáris osztályozási algoritmusra támaszkodva alacsony pontosságot eredményezne.*

![Nemlineáris trenddel rendelkező adatok](./media/how-to-select-algorithms/nonlinear-trend.png)

***Nemlineáris trenddel rendelkező adatok***: *Lineáris regressziós módszer használata a szükségesnél sokkal nagyobb hibákat eredményezne.*

## <a name="number-of-parameters"></a>Paraméterek száma

A paraméterek azok a gombok, amelyeket egy adattudós az algoritmus beállításakor megfordít. Ezek olyan számok, amelyek befolyásolják az algoritmus viselkedését, például a hibatűrést vagy az ismétlések számát, vagy az algoritmus viselkedésének változatai közötti beállításokat. A betanítási idő és az algoritmus pontossága néha érzékeny lehet a megfelelő beállítások megszerzésére. A nagy számú paraméterrel rendelkező algoritmusok általában a legtöbb próbaverziót és hibát igénylik a megfelelő kombináció megtalálásához.

Másik lehetőségként a [Tune Model Hyperparameters modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) machine learning designer: A modul célja, hogy meghatározza az optimális hiperparaméterek egy gépi tanulási modell. A modul több modellt épít és tesztel a beállítások különböző kombinációinak használatával. Összehasonlítja a mutatókat az összes modell, hogy a beállítások kombinációit. 

Bár ez egy nagyszerű módja annak, hogy győződjön meg arról, hogy átívelt a paraméter tér, a modell betanításához szükséges idő exponenciálisan növekszik a paraméterek száma. A fejjel az, hogy a sok paraméter általában azt jelzi, hogy egy algoritmus nagyobb rugalmasságot. Ez gyakran nagyon jó pontosságot érhet el, feltéve, hogy megtalálja a paraméterbeállítások megfelelő kombinációját.

## <a name="number-of-features"></a>Szolgáltatások száma

A gépi tanulásban a szolgáltatás az elemezni kívánt jelenség számszerűsíthető változója. Bizonyos adattípusok esetében a szolgáltatások száma az adatpontok számához képest nagyon nagy lehet. Gyakran ez a helyzet a genetika vagy szöveges adatok esetében. 

Számos funkció is bog le néhány tanulási algoritmusok, így képzési idő megvalósíthatóan hosszú. [A támogatási vektorgépek](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) különösen jól illeszkednek a nagy számú funkcióval rendelkező forgatókönyvekhez. Emiatt számos alkalmazásban használták őket az információlekéréstől a szöveg- és képbesorolásig. Támogatási vektorgépek osztályozási és regressziós feladatokhoz is használható.

A funkciókiválasztása a statisztikai tesztek bemenetekre történő alkalmazásának folyamatára vonatkozik, adott egy adott kimenetalapján. A cél annak meghatározása, hogy mely oszlopok prediktívebbek a kimenettel. A Machine Learning designer [szűrőalapú szolgáltatáskijelölési modulja](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) több funkciókijelölési algoritmust biztosít, amelyek közül választhat. A modul olyan korrelációs módszereket tartalmaz, mint a Pearson korreláció és a khi-négyzet értékek.

A [Permutációs szolgáltatás fontossági modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) segítségével kiszámíthatja az adatkészlet szolgáltatásfontossági pontszámait. Ezután kihasználhatja ezeket a pontszámokat, hogy segítsen meghatározni a legjobb funkciókat használni a modellben.


## <a name="next-steps"></a>További lépések

 - [További információ az Azure Machine Learning tervezőjéről](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Az Azure Machine Learning tervezőjében elérhető gépi tanulási algoritmusok leírását a [Machine Learning tervezői algoritmusa és modulhivatkozása című](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) témakörben tartalmazza.
 - A mélytanulás, a gépi tanulás és a mi közötti kapcsolat megismeréséhez tekintse meg a [Deep Learning vs. Machine Learning című témakört.](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
