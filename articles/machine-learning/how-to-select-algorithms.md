---
title: Gépi tanulási algoritmus kiválasztása
titleSuffix: Azure Machine Learning
description: Azure Machine Learning algoritmusok kiválasztása a fürtözési, besorolási vagy regressziós kísérletek felügyelt és nem felügyelt tanulásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 98f7edac5bbec7a88999c728b2e4db8be7a3d2b5
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891346"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Azure Machine Learning algoritmusok kiválasztása

Gyakori kérdés, hogy milyen gépi tanulási algoritmust használok? A kiválasztott algoritmus elsődlegesen az adatelemzési forgatókönyv két különböző aspektusán múlik:

 - **Mit szeretne tenni az adataival?** Konkrétan milyen üzleti kérdést szeretne megválaszolni a múltbeli adatok megismerésével?

 - **Mik az adatelemzési forgatókönyv követelményei?** Pontosabban, mi a pontosság, a képzési idő, a linearitás, a paraméterek száma és a megoldás által támogatott szolgáltatások száma?

 ![Az algoritmusok kiválasztásának szempontjai: mit szeretne tudni? Mik a forgatókönyvekre vonatkozó követelmények?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Üzleti forgatókönyvek és a Machine Learning algoritmus – Cheat Sheet

Az [Azure Machine learning algoritmust tartalmazó Cheat Sheet](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) segít az első megfontolásban: **mit szeretne tenni az adatokkal**? A Machine Learning algoritmust tartalmazó Cheat lapon keresse meg a végrehajtani kívánt feladatot, majd keresse meg a prediktív elemzési megoldás [Azure Machine learning Designer](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) algoritmusát. 

A Machine Learning Designer olyan algoritmusok átfogó portfólióját kínálja, mint a [többosztályos döntési erdő](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), a javasolt [rendszerek](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), a [neurális hálózat regressziója](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), a [többosztályos neurális hálózat](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)és a [K – fürtözés](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri). Minden algoritmus úgy lett kialakítva, hogy más típusú gépi tanulási problémát lehessen kezelni. Tekintse meg a [Machine learning Designer algoritmust és a modul-referenciát](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) a teljes listához, valamint a dokumentációt arról, hogy az egyes algoritmusok hogyan működnek és hogyan hangolják össze a paramétereket az algoritmus optimalizálásához

> [!NOTE]
> A Machine learning-algoritmus Cheat-táblázatának letöltéséhez nyissa meg az [Azure Machine learning-algoritmust tartalmazó Cheat](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)lapot.
> 
> 

A Azure Machine Learning algoritmus Cheat (útmutató) című témakör útmutatását követve további követelményeket is figyelembe kell vennie a gépi tanulási algoritmus kiválasztásakor a megoldáshoz. A következőkben további szempontokat is figyelembe kell venni, például a pontosságot, a betanítási időt, a linearitást, a paraméterek számát és a szolgáltatások számát.

## <a name="comparison-of-machine-learning-algorithms"></a>A gépi tanulási algoritmusok összehasonlítása

Egyes tanulási algoritmusok kifejezetten feltételezik az adatok szerkezetét vagy a kívánt eredményeket. Ha megtalálja az igényeinek megfelelőt, hasznosabb eredményeket, pontosabb előrejelzéseket vagy gyorsabb betanítási időt biztosíthat.

Az alábbi táblázat a besorolási, a regressziós és a fürtözési családok algoritmusának legfontosabb jellemzőit összegzi:

| **Algoritmus** | **Pontosságát** | **Betanítási idő** | **Linearitás** | **Paraméterek** | **Megjegyzések** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Besorolási család** | | | | | |
| [Kétosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |Megfelelő  |Gyors |Igen |4 | |
| [Kétosztályos döntési erdő](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |No |5 |A lassabb pontozási időket jeleníti meg. Javasoljuk, hogy ne működjön együtt One-vs-All Multiclassval, mert a faszerkezetes előrejelzések felhalmozódása során a futófelület zárolása által okozott lassabb pontozási idő |
| [Kétosztályos, megnövelt döntési fa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |No |6 |Nagy memória-lábnyom |
| [Kétosztályos neurális hálózat](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |Megfelelő |Közepes |No |8 | |
| [Kétosztályos átlagú perceptron](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |Megfelelő |Közepes |Igen |4 | |
| [Kétosztályos támogatású vektoros gép](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |Megfelelő |Gyors |Igen |5 |Kiválóan alkalmas a nagyméretű szolgáltatások számára |
| [Többosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |Megfelelő |Gyors |Igen |4 | |
| [Többosztályos döntési erdő](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |No |5 |Lassabb pontozási idő megjelenítése |
| [Többosztályos növelt döntési fa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |No |6 | Egyre nagyobb pontosságot biztosít a kisebb lefedettséggel járó kis kockázat miatt |
| [Többosztályos neurális hálózat](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |Megfelelő |Közepes |No |8 | |
| [Egy-és többosztályos](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Tekintse meg a kiválasztott kétosztályos módszer tulajdonságait. |
| **Regressziós család** | | | | | |
| [Lineáris regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |Megfelelő |Gyors |Igen |4 | |
| [Döntési erdő regressziója](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|Kiváló |Közepes |No |5 | |
| [A döntési fa regressziójának fokozása](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |No |6 |Nagy memória-lábnyom |
| [Neurális hálózat regressziója](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |Megfelelő |Közepes |No |8 | |
| **Fürtözési család** | | | | | |
| [K – fürtözés](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |Kiváló |Közepes |Igen |8 |Egy fürtözési algoritmus |

## <a name="requirements-for-a-data-science-scenario"></a>Az adatelemzési forgatókönyvre vonatkozó követelmények

Ha már tudja, hogy mit szeretne tenni az adataival, meg kell határoznia a megoldás további követelményeit. 

A következő követelményekkel és lehetséges kompromisszumokkal teheti a döntéseket:

- Pontosságát
- Betanítási idő
- Linearitás
- Paraméterek száma
- Szolgáltatások száma

## <a name="accuracy"></a>Pontosságát

A gépi tanulás pontossága azt méri, hogy a modell hatékonysága a valódi eredményeknek az összes esethez viszonyított aránya. Machine Learning Designerben a [modell kiértékelése modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) az iparági szabványnak megfelelő értékelési metrikákat számítja ki. Ezzel a modullal mérhető a betanított modell pontossága.

A lehető legpontosabb válasz nem mindig szükséges. Időnként a közelítés megfelelő, attól függően, hogy mit kíván használni a alkalmazáshoz. Ha ez az eset áll fenn, előfordulhat, hogy a feldolgozási időt jelentősen csökkenteni kell a további közelítő módszerekkel. A közelítő metódusok természetesen általában a túlilleszkedés elkerülését is elkerülik.

Háromféle módon használhatja a modell kiértékelése modult:

- Pontszámok előállítása a betanítási adataihoz a modell kiértékelése érdekében
- Pontszámok létrehozása a modellhez, de a pontszámok összevetése egy foglalt tesztelési csoport pontszámai alapján
- A pontszámok összehasonlítása két különböző, de kapcsolódó modell esetében ugyanazon adathalmaz használatával

A gépi tanulási modellek pontosságának értékeléséhez használható mérőszámok és megközelítések teljes listáját lásd: [modell kiértékelése modul](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Betanítási idő

A felügyelt tanulás során a betanítási módszer a korábbi adatgyűjtést használja a hibák minimalizálására szolgáló gépi tanulási modell létrehozásához. A modellek betanításához szükséges percek vagy órák száma az algoritmusok közötti nagy mennyiségre változik. A betanítási idő gyakran szorosan kötődik a pontossághoz. az egyik általában a másikat kíséri. 

Emellett egyes algoritmusok érzékenyebbek az adatpontok számára, mint mások. Kiválaszthat egy adott algoritmust, mert van egy időkorlátja, különösen akkor, ha az adathalmaz nagy.

Machine Learning Designerben a Machine learning-modell létrehozása és használata általában egy három lépésből álló folyamat:

1.  Konfiguráljon egy modellt egy adott típusú algoritmus kiválasztásával, majd adja meg a paramétereit vagy a hiperparaméterek beállítása. 

2.  Adja meg a címkével ellátott és az algoritmussal kompatibilis adatokat tartalmazó adatkészletet. Az és a modell összekapcsolásával a [modell modult is betaníthatja](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  A betanítás befejezése után a betanított modellt a [pontozási modulok](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) egyikével hajthatja végre az új adatmennyiségek előrejelzéséhez.

## <a name="linearity"></a>Linearitás

A statisztikák és a gépi tanulás linearitása azt jelenti, hogy lineáris kapcsolat áll fenn egy változó és egy állandó között az adatkészletben. A lineáris besorolási algoritmusok például feltételezik, hogy az osztályokat egyenes vonallal (vagy a magasabb dimenziós analóg) lehet elválasztani.

A gépi tanulási algoritmusok sok a linearitást használják. Azure Machine Learning Designerben a következőket tartalmazzák: 

- [Többosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Kétosztályos logisztikai regresszió](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [A vektoros gépek támogatása](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

A lineáris regressziós algoritmusok azt feltételezik, hogy az adattrendek egy egyenes vonalat követnek. Ez a feltételezés nem okoz gondot bizonyos problémák esetén, mások pedig csökkentik a pontosságot. Hátrányaik ellenére a lineáris algoritmusok az első stratégiaként népszerűek. Általában egyszerű és gyors algorithmically válnak.

![Nem lineáris osztály határa](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Nemlineáris osztály határa***: a *lineáris besorolási algoritmusra való támaszkodás kis pontosságot eredményezne.*

![Nem lineáris trendtel rendelkező adatokat](./media/how-to-select-algorithms/nonlinear-trend.png)

Nem ***lineáris trendtel rendelkező adatokat***: *a lineáris regressziós módszer használata sokkal nagyobb hibákat eredményez, mint amennyi szükséges.*

## <a name="number-of-parameters"></a>Paraméterek száma

A paraméterekkel az adattudós egy algoritmus beállításakor válik elérhetővé. Ezek olyan számok, amelyek hatással vannak az algoritmus viselkedésére, például a hibatűrésre vagy az ismétlések számára, illetve az algoritmus működésének különböző változatai közötti beállításokra. Az algoritmus betanítási ideje és pontossága esetenként érzékeny lehet a megfelelő beállítások beszerzésére. A nagy számú paraméterrel rendelkező algoritmusok általában a legtöbb próbaidőszakot és hibát igénylik a jó kombináció megtalálásához.

Azt is megteheti, hogy a [modell hiperparaméterek beállítása modulját](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) Machine learning Designerben: ennek a modulnak a célja a gépi tanulási modell optimális hiperparaméterek beállítása meghatározása. A modul több modellt is felépít és tesztel a különböző beállítások kombinációjának használatával. Összehasonlítja az összes modell mérőszámait a beállítások kombinációjának lekéréséhez. 

Habár ez nagyszerű módja annak, hogy megbizonyosodjon róla, hogy a paraméter területét átölelte, a modell betanításához szükséges idő exponenciálisan növekszik a paraméterek számával. Minél több paraméterrel rendelkezik, az azt jelenti, hogy egy algoritmus nagyobb rugalmasságot biztosít. Ez gyakran nagyon jó pontosságot érhet el, és a paraméterek beállításainak megfelelő kombinációját is megtalálhatja.

## <a name="number-of-features"></a>Szolgáltatások száma

A gépi tanulásban a funkció az elemezni kívánt jelenség számszerűsíthető változója. Bizonyos adattípusok esetében a szolgáltatások száma nagyon nagy lehet az adatpontok számával szemben. Ez gyakran előfordul a genetika vagy a szöveges adatértékek esetében. 

Számos szolgáltatás képes leszűkíteni bizonyos tanulási algoritmusokat, így hosszú időt unfeasibly. A [támogatott vektoros gépek](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) különösen jól alkalmazhatók a nagy számú funkcióval rendelkező forgatókönyvekhez. Emiatt számos alkalmazásban használták őket a szöveg-és képbesorolási adatok beolvasására. A támogatott vektoros gépek a besorolási és a regressziós feladatokhoz is használhatók.

A szolgáltatás kiválasztása arra utal, hogy a rendszer a megadott kimenet alapján statisztikai teszteket alkalmazzon bemenetekre. A cél annak meghatározása, hogy mely oszlopok legyenek a kimenet prediktív megjelenítése. A Machine Learning Designer [szűrő alapú funkció-kiválasztási modulja](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) több funkció-kiválasztási algoritmust biztosít a kiválasztásához. A modul olyan korrelációs módszereket tartalmaz, mint például a Pearson korrelációs és a KHI-négyzetes értékek.

A [permutáció szolgáltatás fontossági moduljának](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) használatával kiszámíthatja az adatkészlet szolgáltatásbeli fontossági pontszámait is. Ezt követően kihasználhatja ezeket a pontszámokat, és meghatározhatja a modellben használandó legjobb szolgáltatásokat.

## <a name="next-steps"></a>További lépések

 - [További információ a Azure Machine Learning designerről](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - A Azure Machine Learning Designerben elérhető gépi tanulási algoritmusok leírását lásd: [Machine learning Designer algoritmus és modul-hivatkozás](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - A mély tanulás, a gépi tanulás és a mesterséges intelligencia közötti kapcsolat megismeréséhez tekintse meg a [Deep learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri) és a Machine learning
