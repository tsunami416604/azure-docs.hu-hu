---
title: Algoritmusok kiválasztása
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasszikus) algoritmusok kiválasztása a fürtözési, besorolási vagy regressziós kísérletek felügyelt és nem felügyelt tanulásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 2073123a61e919c10caaaea141f776e842f4d717
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427734"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Algoritmusok kiválasztása Azure Machine Learning Studiohoz (klasszikus)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

A válasz arra a kérdésre, hogy milyen gépi tanulási algoritmust használok? mindig "függ". Ez az adatok méretétől, minőségétől és természetétől függ. Ez attól függ, hogy mit szeretne tenni a válaszban. Ez attól függ, hogy az algoritmus matematikai értéke hogyan lett lefordítva a használt számítógépre vonatkozó utasításokba. Ez attól függ, hogy mennyi idő van. Még a legtapasztaltabb adatszakértők sem tudják megállapítani, hogy melyik algoritmus fogja elvégezni a legjobban a kipróbálás előtt.

A Machine Learning Studio (klasszikus) a legkorszerűbb algoritmusokat, például a skálázható, kibővített döntési fákat, a Bayes ajánlási rendszereket, a mély neurális hálózatokat és a Microsoft Research által fejlesztett döntési Dzsungeleket biztosít. Az olyan méretezhető, nyílt forráskódú gépi tanulási csomagokat, mint a Vowpal Wabbit, szintén támogatja a Machine Learning Studio. A Machine Learning Studio (klasszikus) támogatja a gépi tanulási algoritmusokat a többosztályos és a bináris besoroláshoz, a regresszióhoz és a fürtözéshez. Tekintse meg [Machine learning modulok](/azure/machine-learning/studio-module-reference/index)teljes listáját.
A dokumentáció tartalmaz néhány információt az egyes algoritmusokról, és ismerteti, hogyan hangolhatja be a paramétereket a használati algoritmus optimalizálásához.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>A Machine Learning algoritmus Cheat Sheet

A **[Microsoft Azure Machine learning algoritmust tartalmazó Cheat Sheet](../algorithm-cheat-sheet.md)** segítségével kiválaszthatja a megfelelő gépi tanulási algoritmust a prediktív elemzési megoldásokhoz az algoritmusok Azure Machine learning könyvtárából.
Ez a cikk bemutatja, hogyan használhatja ezt a Cheat lapot.

> [!NOTE]
> A Cheat-táblázat letöltéséhez és a jelen cikk végrehajtásához nyissa meg a [Machine learning-algoritmus Cheat Sheet című lapot](../algorithm-cheat-sheet.md).
> 
> 

Ezek a javaslatok számos adatszakértőből és gépi tanulási szakértőkből származó visszajelzéseket és tippeket tartalmaznak. Nem értünk egyet mindenben, de igyekszünk összehangolni a véleményüket egy durva konszenzussal. A nézeteltérések többsége a következővel kezdődik: "Ez függ..."


> [!TIP]
> A Machine learning alapjairól szóló, könnyen értelmezhető infographic-áttekintést az általános gépi tanulási kérdések megválaszolásához használt népszerű algoritmusok megismerését ismertető [cikkben](basics-infographic-with-algorithm-examples.md)találhatja meg.

## <a name="flavors-of-machine-learning"></a>A gépi tanuláshoz tartozó ízek

### <a name="supervised"></a>Felügyelt

A felügyelt tanulási algoritmusok példákon alapuló előrejelzéseket hoznak létre. A korábbi készletek díjszabásával például kitalálhatja a jövőbeli árakat. A képzéshez használt egyes példák a kamat értékkel vannak megjelölve, ebben az esetben a tőzsdei árfolyamon. A felügyelt tanulási algoritmus mintákat keres az értékek címkéjén. Bármilyen releváns információt használhat – a hét napját, a szezont, a vállalat pénzügyi adatait, az iparág típusát, a zavaró geopolitikai események jelenlétét, és minden algoritmus különböző típusú mintákat keres. Miután az algoritmus megtalálta a legjobb mintát, ezt a mintát használja, hogy előrejelzéseket készítsen a címke nélküli tesztelési adatként – a holnap árait.

A felügyelt tanulás a gépi tanulás népszerű és hasznos típusa. Egyetlen kivétel esetén a Azure Machine Learning Studio (klasszikus) összes modulja felügyelt tanulási algoritmusok. A felügyelt tanulásnak számos különböző típusa van, amelyek a Azure Machine Learning Studio (klasszikus) területen jelennek meg: besorolás, regresszió és anomália észlelés.

* **Besorolás**. Ha az adat egy kategória előrejelzésére használatos, a felügyelt tanulást besorolásnak is nevezzük. Ez a helyzet olyankor, amikor egy képet "Cat" vagy "Dog" képként rendel hozzá. Ha csak két lehetőség van, akkor a rendszer **kétosztályos** vagy **binomiális besorolású**. Ha több kategória van, mint az NCAA March Madness verseny győztesének előrejelzése, akkor ezt a problémát **többosztályos besorolásnak**nevezzük.
* **Regresszió**. Egy érték előrejelzése esetén, mint a részvényárfolyamok esetében, a felügyelt tanulást regressziónak nevezzük.
* **Anomáliák észlelése**. Előfordulhat, hogy a cél az egyszerűen szokatlan adatpontok azonosítása. A csalások észlelése, például a szokatlanul szokatlan hitelkártya-kiadások gyanúja. A lehetséges variációk számos és a képzési példákat tartalmaznak, így nem valósítható meg, hogy megtudja, mi a csalárd tevékenység. A anomália észlelésének módja az, hogy egyszerűen megtudhatja, hogy a szokásos tevékenység hogyan néz ki (a nem csalárd tranzakciók előzményeit használva), és azonosítsa a jelentős mértékben eltérőket.

### <a name="unsupervised"></a>Unsupervised

A nem felügyelt tanulásban az adatpontokhoz nem tartoznak címkék. Ehelyett egy nem felügyelt tanulási algoritmus célja az, hogy valamilyen módon szervezze az adatmennyiséget, vagy leírja a szerkezetét. Ez azt jelentheti, hogy fürtökbe csoportosítják, vagy az összetett adatelemzés különböző módjait keresi, hogy egyszerűbb vagy több rendszerezést lehessen megtekinteni.

### <a name="reinforcement-learning"></a>Megerősítő tanulás

A megerősítő tanulásban az algoritmus az egyes adatpontokra adott válaszként kiválaszt egy műveletet. A tanulási algoritmus rövid idő múlva egy jutalom jelzést is kap, amely azt jelzi, hogy milyen jó döntés volt.
Ennek alapján az algoritmus a legmagasabb jutalom elérése érdekében módosítja a stratégiáját. Jelenleg nincsenek megerősítő tanulási algoritmus-modulok Azure Machine Learning Studio (klasszikus). A megerősítő tanulás gyakori a robotika területén, ahol az érzékelői beolvasása egy adott időpontban egy adatpont, és az algoritmusnak a robot következő műveletét kell választania. A eszközök internetes hálózata-alkalmazások számára is természetes.

## <a name="considerations-when-choosing-an-algorithm"></a>Az algoritmus kiválasztásakor felmerülő megfontolások

### <a name="accuracy"></a>Pontosságát

A lehető legpontosabb válasz nem mindig szükséges.
Időnként a közelítés megfelelő, attól függően, hogy mit kíván használni a alkalmazáshoz. Ha ez az eset áll fenn, előfordulhat, hogy a feldolgozási időt jelentősen csökkenteni kell a további közelítő módszerekkel. A további közelítő módszerek egyik másik előnye, hogy természetesen a túlilleszkedés elkerülése érdekében.

### <a name="training-time"></a>Betanítási idő

A modellek betanításához szükséges percek vagy órák száma az algoritmusok közötti nagy mennyiségre változik. A képzési idő gyakran szorosan kötődik a pontossághoz – az egyik általában a másikat kíséri. Emellett egyes algoritmusok érzékenyebbek az adatpontok számára, mint mások.
Ha az idő korlátozott, az algoritmus kiválasztását is elvégezheti, különösen akkor, ha az adathalmaz nagy.

### <a name="linearity"></a>Linearitás

A gépi tanulási algoritmusok sok a linearitást használják. A lineáris besorolási algoritmusok azt feltételezik, hogy az osztályokat egyenes vonallal (vagy a magasabb dimenziós analóg) el lehet választani. Ilyenek például a logisztikai regressziós és támogató vektoros gépek (Azure Machine Learning Studio (klasszikus)).
A lineáris regressziós algoritmusok azt feltételezik, hogy az adattrendek egy egyenes vonalat követnek. Ezek a feltételezések nem rosszak bizonyos problémák esetén, mások pedig a pontosságot.

![Nem lineáris osztály határa](./media/algorithm-choice/image1.png)

***Nem lineáris osztály határa*** *– a lineáris besorolási algoritmusra való támaszkodás kis pontosságot eredményezhet*

![Nem lineáris trendtel rendelkező adatokat](./media/algorithm-choice/image2.png)

Nem ***lineáris trendtel rendelkező adatokat*** *– a lineáris regressziós módszer használata sokkal nagyobb hibákat eredményez, mint amennyi szükséges*

A veszélyek ellenére a lineáris algoritmusok nagyon népszerűek a támadás első soraként. Általában egyszerű és gyors algorithmically válnak.

### <a name="number-of-parameters"></a>Paraméterek száma

A paraméterekkel az adattudós egy algoritmus beállításakor válik elérhetővé. Ezek olyan számok, amelyek hatással vannak az algoritmus viselkedésére, például a hibatűrésre vagy az ismétlések számára, illetve az algoritmus működésének különböző változatai közötti beállításokra. Az algoritmus betanítási ideje és pontossága esetenként meglehetősen érzékeny lehet a megfelelő beállítások beszerzésére. A nagy számú paraméterrel rendelkező algoritmusok általában a legtöbb próbaidőszakot és hibát igénylik a jó kombináció megtalálásához.

Azt is megteheti, hogy a Azure Machine Learning Studio (klasszikus) [paraméter-elsöprő](algorithm-parameters-optimize.md) modul blokkja automatikusan megkísérli az összes paraméter-kombinációt a választott részletességgel. Habár ez nagyszerű módja annak, hogy megbizonyosodjon róla, hogy a paraméter területét átölelte, a modell betanításához szükséges idő exponenciálisan növekszik a paraméterek számával.

Minél több paraméterrel rendelkezik, az azt jelenti, hogy egy algoritmus nagyobb rugalmasságot biztosít. Ez gyakran nagyon jó pontosságot érhet el, és a paraméterek beállításainak megfelelő kombinációját is megtalálhatja.

### <a name="number-of-features"></a>Szolgáltatások száma

Bizonyos adattípusok esetében a szolgáltatások száma nagyon nagy lehet az adatpontok számával szemben. Ez gyakran előfordul a genetika vagy a szöveges adatértékek esetében. A funkciók nagy száma leszűkítheti a tanulási algoritmusokat, így hosszú időt unfeasibly. A támogatott vektoros gépek különösen alkalmasak ebben az esetben (lásd alább).

### <a name="special-cases"></a>Különleges esetek

Egyes tanulási algoritmusok kifejezetten feltételezik az adatok szerkezetét vagy a kívánt eredményeket. Ha megtalálja az igényeinek megfelelőt, hasznosabb eredményeket, pontosabb előrejelzéseket vagy gyorsabb betanítási időt biztosíthat.

| **Algoritmus** | **Pontosságát** | **Betanítási idő** | **Linearitás** | **Paraméterek** | **Megjegyzések** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Kétosztályos besorolás** | | | | | |
| [logisztikai regresszió](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [döntési dzsungel](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Kevés a memória lábnyoma |
| [megnövelt döntési fa](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Nagy memória-lábnyom |
| [neurális hálózat](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[További Testreszabás is lehetséges](azure-ml-netsharp-reference-guide.md) |
| [átlagos perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [a vektoros gép támogatása](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Kiválóan alkalmas a nagyméretű szolgáltatások számára |
| [helyileg támogatott vektoros gép](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Kiválóan alkalmas a nagyméretű szolgáltatások számára |
| [Bayes pont gép](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Többosztályos besorolás** | | | | | |
| [logisztikai regresszió](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [döntési dzsungel](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Kevés a memória lábnyoma |
| [neurális hálózat](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[További Testreszabás is lehetséges](azure-ml-netsharp-reference-guide.md) |
| [egy-v-mind](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Tekintse meg a kiválasztott kétosztályos módszer tulajdonságait. |
| **Regressziós** | | | | | |
| [lineáris](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayes lineáris](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [döntési erdő](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [megnövelt döntési fa](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Nagy memória-lábnyom |
| [gyors erdő quantile](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Eloszlások a pontok előrejelzése helyett |
| [neurális hálózat](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[További Testreszabás is lehetséges](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Műszakilag lineáris. A prediktív számláláshoz |
| [sorszámok](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |A rangsor megrendelésének előrejelzéséhez |
| **Anomáliadetektálás** | | | | | |
| [a vektoros gép támogatása](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Különösen hasznos a nagyméretű funkciókhoz |
| [PCA-alapú anomáliák észlelése](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K – azt jelenti, hogy](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Egy fürtözési algoritmus |

**Algoritmus tulajdonságai:**

**●** – kiváló pontosságot, gyors betanítási időt és a linearitás használatát mutatja be

**○** – a megfelelő pontosságot és mérsékelt betanítási időt jeleníti meg

## <a name="algorithm-notes"></a>Algoritmussal kapcsolatos megjegyzések

### <a name="linear-regression"></a>Lineáris regresszió

Ahogy azt korábban említettük, a [lineáris regresszió](/azure/machine-learning/studio-module-reference/linear-regression) egy vonalat (vagy síkot vagy hyperplane) illeszt az adathalmazhoz. Ez egy egyszerű és gyors igásló, de előfordulhat, hogy a probléma túlzottan leegyszerűsíthető.

![Lineáris trendtel rendelkező adatokat](./media/algorithm-choice/image3.png)

***Lineáris trendtel rendelkező adatokat***

### <a name="logistic-regression"></a>Logisztikai regresszió

Annak ellenére, hogy a "regresszió" szerepel a névben, a logisztikai regresszió valójában egy hatékony eszköz a [kétosztályos](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) és a [többosztályos](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) besoroláshoz. Gyors és egyszerű. Az a tény, hogy egy egyenes vonal helyett a "alakú görbét használ, természetes illeszkedést biztosít az adatcsoportokba való osztáshoz. A logisztikai regresszió lineáris osztályt biztosít, így a használatakor a lineáris közelítést kell használnia.

![Logisztikai regresszió két osztályos adatértékre egyetlen funkcióval](./media/algorithm-choice/image4.png)

***Egy logisztikai regresszió a kétosztályos adathoz egyetlen funkcióval*** *– az osztály határa az a pont, ahol a logisztikai görbe a két osztályhoz hasonlóan közelít*

### <a name="trees-forests-and-jungles"></a>Fák, erdők és dzsungelek

Döntési erdők[(regresszió](/azure/machine-learning/studio-module-reference/decision-forest-regression), [kétosztályos](/azure/machine-learning/studio-module-reference/two-class-decision-forest)és [többosztályos](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)), döntési dzsungel ([kétosztályos](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) és [többosztályos](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)), valamint a megnövelt döntési fák ([regresszió](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) és [kétosztályos](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) mind a döntési fák, mind a megalapozott gépi tanulási koncepció alapján. A döntési fák számos változatban szerepelnek, de mind ugyanezt a dolgot is kiosztják – a szolgáltatás területét a leginkább ugyanazzal a címkével rendelkező régiókba osztjuk. Ezek lehetnek konzisztens kategóriák vagy állandó értékek régiói, attól függően, hogy a besorolást vagy a regressziót végzi.

![Döntési fa kiosztása egy szolgáltatás területének](./media/algorithm-choice/image5.png)

***A döntési fa a szolgáltatás területét a nagyjából egységes értékek régióiba osztja***

Mivel a szolgáltatás területe önkényesen kisebb régiókra osztható, könnyen elképzelhető, hogy az adatpontok régiónként való elosztása elég jól. Ez egy rendkívüli példa a túlzott méretezésre. Ennek elkerülése érdekében a fák nagy készletét speciális matematikai gondossággal kell elvégezni, hogy a fák ne legyenek korrelálva. Ennek a "döntési erdőnek" az átlaga egy olyan fa, amely elkerüli a túlilleszkedés elkerülését. A döntési erdők nagy mennyiségű memóriát használhatnak fel. A döntési dzsungel egy olyan Variant, amely kevesebb memóriát használ fel, valamivel hosszabb betanítási idő árán.

A megnövelt döntési fák elkerülik a túlilleszkedés elkerülését azáltal, hogy megszabják, hogy hányszor oszthatók fel, és hogy az egyes régiókban hány adatpont engedélyezett. Az algoritmus faszerkezetű sorozatot állít össze, amelyek mindegyike megtanítja, hogy kompenzálja a fát a fa előtt. Az eredmény egy nagyon pontos tanuló, amely nagy mennyiségű memóriát használ. A teljes technikai leírásért tekintse meg a [Friedman eredeti dokumentumát](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

A [Fast Forest quantile regresszió](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) a különleges esetekhez tartozó döntési fák egy változata, amely nem csupán a régión belüli adatmennyiség (medián) értékét, hanem a quantiles formájában történő terjesztését is szeretné tudni.

### <a name="neural-networks-and-perceptrons"></a>Neurális hálózatok és perceptrons

A neurális hálózatok olyan agyi ihletésű tanulási algoritmusok, amelyek [többosztályos](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [kétosztályos](/azure/machine-learning/studio-module-reference/two-class-neural-network)és [regressziós](/azure/machine-learning/studio-module-reference/neural-network-regression) problémákkal rendelkeznek. A rendszer végtelen változatosságot eredményez, de a Azure Machine Learning Studioon (klasszikuson) belüli neurális hálózatok az irányított aciklikus-diagramok teljes formája. Ez azt jelenti, hogy a bemeneti funkciók továbbítva lesznek (soha nem visszafelé) a rétegek sorozatából, mielőtt a kimenetek be lettek kapcsolva. Az egyes rétegekben a bemenetek súlyozása különböző kombinációkban történik, és a rendszer a következő rétegre továbbítja azokat. Az egyszerű számítások ezen kombinációja azt eredményezi, hogy a mágia alapján a kifinomult osztályok és az adattrendek is megtanultak. A számos rétegben elvégezhető hálózatok a "mély tanulást" végzik, amelyekkel a legtöbb technikai jelentéskészítési és tudományos fikciós technológia használható.

Ez a nagy teljesítményű szolgáltatás azonban nem ingyenes. A neurális hálózatok hosszú időt vehetnek igénybe a betanítás során, különösen a nagyméretű adathalmazok esetében. Több paraméterrel is rendelkeznek, mint a legtöbb algoritmus, ami azt jelenti, hogy a leválasztási paraméterek nagy mértékben kibővítik a betanítási időt.
A lehetőségek azonban a [saját hálózati struktúrájának megadására szeretnének megadhatók](azure-ml-netsharp-reference-guide.md).

![A neurális hálózatok által megszerzett határok](./media/algorithm-choice/image6.png)

***A neurális hálózatok által megismert határok összetett és szabálytalan lehet***

A [kétosztályos átlagú perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) a neurális hálózatok válasza a betanítási időpontokra. Olyan hálózati struktúrát használ, amely lineáris osztálybeli határokat biztosít. A mai szabványok csaknem primitívek, de hosszú múltra tekintenek a robusztus működés, és elég kicsi a gyors tanuláshoz.

### <a name="svms"></a>SVMs

A support Vector Machines (SVMs) megkeresi a határt, amely a lehető legszélesebb árréssel választja el az osztályokat. Ha a két osztály nem lehet egyértelműen elválasztva, az algoritmusok megtalálják a legjobb határt. Amint Azure Machine Learning Studio (klasszikus), a [kétosztályos SVM](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) csak egyenes vonalban végezhető el (SVM-Speak, lineáris kernelt használ).
Mivel ez a lineáris közelítést teszi lehetővé, elég gyorsan futtatható. Ahol az igazán ragyog, a funkció-intenzív adatértékekkel, például szöveg-vagy genomikai adattal rendelkezik. Ezekben az esetekben a SVMs gyorsabban és kevesebb, mint a legtöbb más algoritmussal is elkülönítheti az osztályokat, továbbá csak szerény mennyiségű memóriát igényel.

![A Vector Machine Class határának támogatása](./media/algorithm-choice/image7.png)

***Egy jellemzően támogatott vektoros gépi osztály határa maximalizálja a két osztályt elválasztó margót***

A Microsoft Research egy másik terméke, a [kétosztályos helyi SVM](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) a SVM nem lineáris változata, amely a lineáris verzió legnagyobb sebességét és memória-hatékonyságát őrzi meg. Ideális azokra az esetekre, amikor a lineáris megközelítés nem ad elég pontos választ. A fejlesztők gyorsan megtartották a problémát, és számos kis lineáris SVM probléma merült fel. Olvassa el a részletes [leírást](http://proceedings.mlr.press/v28/jose13.html) , amely leírja, hogyan húzta le ezt a trükköt.

A nemlineáris SVMs intelligens bővítményének használatával az [egyosztályos SVM](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) olyan határt rajzol, amely szorosan ismerteti a teljes adathalmazt. Ez hasznos a anomáliák észleléséhez. A határtól távol eső új adatpontok elég szokatlanok ahhoz, hogy figyelemmel legyenek.

### <a name="bayesian-methods"></a>Bayes-módszerek

A Bayes-as metódusok kiváló minőségűek: elkerülhetők a túlilleszkedés. Ezt úgy teheti meg, hogy a válasz valószínű eloszlásával kapcsolatban előre feltételezi a feltételeit. Ennek a módszernek egy másik mellékterméke, hogy nagyon kevés paraméterrel rendelkeznek. A Azure Machine Learning Studio (klasszikus) a[kétosztályos Bayes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)és a regresszió (a[Bayes lineáris regresszió](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)) esetében egyaránt tartalmaz Bayes-algoritmusokat.
Vegye figyelembe, hogy ezek az adatsorok eloszthatók, és egyenes vonallal is elférnek.

Egy korábbi megjegyzésben a Bayes "Point Machines fejlesztették ki a Microsoft Research szolgáltatásban. Néhány kivételesen szép elméleti munkát végeznek. Az érdeklődő tanuló a [JMLR eredeti cikkére](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) és egy [éleslátó blogra van irányítva Chris Bishop alapján](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Speciális algoritmusok
Ha van egy nagyon konkrét célja, lehet, hogy szerencséje van. A Azure Machine Learning Studio (klasszikus) gyűjteményen belül vannak olyan algoritmusok, amelyek a következőkre specializálódnak:

- rangsorolási előrejelzés ([sorszám regresszió](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- előrejelzések száma ([Poisson-regresszió](/azure/machine-learning/studio-module-reference/poisson-regression)),
- anomáliák észlelése (az [elsődleges összetevők elemzése](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) , valamint egy, a [támogatási vektoros gépeken](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine)alapuló)
- fürtözés ([K-means](/azure/machine-learning/studio-module-reference/k-means-clustering))

![PCA-alapú anomáliák észlelése](./media/algorithm-choice/image8.png)

***PCA-alapú anomáliák észlelése*** *– az adatok túlnyomó többsége egy sztereotip eloszlásba esik; a terjesztéstől számított jelentős pontok gyanúja*

![Az adathalmaz K-t használva csoportosítva](./media/algorithm-choice/image9.png)

***Az adathalmazok öt fürtbe vannak csoportosítva, K-means használatával***

Létezik egy együttes, [egy-v-all többosztályos osztályozó](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass)is, amely az n-Class besorolási problémát az n-1 2 osztály besorolási problémáira bontja. A pontosságot, a betanítási időt és a lineáris tulajdonságot a felhasznált kétosztályos osztályozók határozzák meg.

![Kétosztályos osztályozók kombinálva, három osztályból álló osztályozó formájában](./media/algorithm-choice/image10.png)

***Egy kétosztályos osztályozó pár egy háromrétegű osztályozó alkotása.***

A Azure Machine Learning Studio (klasszikus) a [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model)címe alatt egy hatékony gépi tanulási keretrendszer elérését is magában foglalja.
A VW itt kategorizálja a kategorizálást, mivel az a besorolási és a regressziós problémák megismerésére is képes, és akár részlegesen címkézett adatokból is tanulhat. Azt is beállíthatja, hogy tetszőleges számú tanulási algoritmust, adatvesztési funkciót és optimalizálási algoritmust használjon. A rendszer az alapoktól kezdve hatékonyan, párhuzamosan és rendkívül gyorsan lett kialakítva. Nevetségesen nagy kimutatott funkciókkal kezeli a szolgáltatásait.
A Microsoft Research saját John Langford által megkezdett és vezetett, a VW egy, a Stock Car-algoritmusok egyik mezőjében bekövetkező bejegyzés. Nem minden probléma illik a VW-re, de ha igen, érdemes lehet megmászni a tanulási görbe felületén. [Önálló, nyílt forráskódként](https://github.com/JohnLangford/vowpal_wabbit) is elérhető több nyelven.

## <a name="next-steps"></a>Következő lépések

* A Machine learning alapjairól szóló, könnyen értelmezhető infographic-áttekintést az általános gépi tanulási kérdések megválaszolásához használt népszerű algoritmusok megismerését ismertető [cikkben](basics-infographic-with-algorithm-examples.md)találhatja meg.

* A Machine Learning Studio (klasszikus) szolgáltatásban elérhető gépi tanulási algoritmusok kategóriája alapján a következő témakörben talál információt: [inicializálási modell](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) a Machine learning Studio (klasszikus) algoritmus és modul súgójában.

* Machine Learning Studio (klasszikus) algoritmusok és modulok teljes betűrendes listáját a (Z) Machine Learning Studio (klasszikus) [Machine learning Studio (klasszikus) modulok listájában](/azure/machine-learning/studio-module-reference/a-z-module-list) találhatja meg.
