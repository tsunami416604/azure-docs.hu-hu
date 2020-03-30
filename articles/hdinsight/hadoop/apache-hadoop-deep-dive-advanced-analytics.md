---
title: Mélymerülés - fejlett analitika - Azure HDInsight
description: Ismerje meg, hogy a fejlett analitika hogyan használja az algoritmusokat a big data feldolgozásához az Azure HDInsightban.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646523"
---
# <a name="deep-dive---advanced-analytics"></a>Mélymerülés - fejlett analitika

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Mi a HDInsight speciális elemzése?

A HDInsight lehetővé teszi, hogy értékes betekintést nyerjen nagy mennyiségű strukturált, strukturálatlan és gyorsan változó adatból. A fejlett analitika nagymértékben méretezhető architektúrák, statisztikai és gépi tanulási modellek, valamint intelligens irányítópultok használatával szolgál, hogy érdemi elemzéseket nyújthasson. A gépi tanulás vagy *a prediktív elemzés*olyan algoritmusokat használ, amelyek azonosítják és tanulnak az adatokban lévő kapcsolatokból, hogy előrejelzéseket készítsenek, és irányítsák a döntéseket.

## <a name="advanced-analytics-process"></a>Fejlett elemzési folyamat

![Fejlett elemzési folyamat](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Miután azonosította az üzleti problémát, és elkezdte az adatok gyűjtését és feldolgozását, létre kell hoznia egy modellt, amely az előre jelezni kívánt kérdést képviseli. A modell egy vagy több gépi tanulási algoritmust fog használni az üzleti igényeknek leginkább megfelelő előrejelzési típushoz.  Az adatok többségét a modell betanításához kell használni, a többit pedig a tesztvagy a kiértékeléshez kell használni.

A modell létrehozása, betöltése, tesztelése és kiértékelése után a következő lépés a modell üzembe helyezése, hogy megkezdhesse a kérdésekre adott válaszokat. Az utolsó lépés a modell teljesítményének figyelése és szükség szerint való hangolása.

## <a name="common-types-of-algorithms"></a>Az algoritmusok gyakori típusai

A fejlett elemzési megoldások gépi tanulási algoritmusok készletét biztosítják. Az alábbiakban összefoglaljuk az algoritmusok és a kapcsolódó közös üzleti használati esetek kategóriáit.

![Gépi tanulási kategória összefoglalói](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

A legjobban illeszkedő algoritmus(ok) kiválasztásával együtt meg kell fontolnia, hogy meg kell-e adnia az adatokat a betanításhoz. A gépi tanulási algoritmusok a következőképpen vannak kategorizálva:

* Felügyelt – az algoritmust be kell tanítani a címkézett adatok készletére, mielőtt eredményeket tudna
* Félig felügyelt - algoritmus bővíthető extra célok at interaktív lekérdezés egy tréner, amely nem állt rendelkezésre a kezdeti szakaszban a képzés
* Felügyelet nélkül – az algoritmus nem igényel betanítási adatokat
* Megerősítés - algoritmus szoftverügynökök segítségével határozza meg az ideális viselkedést egy adott környezetben (gyakran használják a robotika)

| Algoritmus kategória| Használat | Tanulási típus | Algoritmusok |
| --- | --- | --- | -- |
| Osztályozás | Személyek vagy dolgok csoportokba sorolása | Felügyelt | Döntési fák, Logisztikai regresszió, neurális hálózatok |
| Fürtözés | Példák együttesének felosztása homogén csoportokra | Felügyelet nélkül | K-eszközök klaszterezése |
| Mintafelismerés | Az adatok gyakori társításának azonosítása | Felügyelet nélkül | Társítási szabályok |
| Regresszió | Számszerű eredmények előrejelzése | Felügyelt | Lineáris regresszió, neurális hálózatok |
| Megerősítés | Határozza meg a robotok optimális viselkedését | Megerősítés | Monte Carlo szimulációk, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Gépi tanulás a HDInsightban

A HDInsight számos gépi tanulási lehetőséget kínál a speciális elemzési munkafolyamatokhoz:

* Gépi tanulás és Apache Spark
* R- és ML-szolgáltatások
* Az Azure Machine Learning és az Apache Hive
* Apache Spark és Deep learning

### <a name="machine-learning-and-apache-spark"></a>Gépi tanulás és Apache Spark

[A HDInsight Spark](../spark/apache-spark-overview.md) az [Apache Spark](https://spark.apache.org/)Azure-ban tárolt ajánlata, amely egy egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszer, amely a memórián belüli feldolgozást használja a Big Data-elemzés kiemelésére. A Spark feldolgozómotor a sebesség, a könnyű használat és a kifinomult analitika érdekében készült. A Spark memórián belüli elosztott számítási képességei jó választássá teszik a gépi tanulásban és grafikonszámításokban használt iteratív algoritmusok számára.

Három méretezhető gépi tanulási kódtárak, amelyek algoritmikus modellezési képességek et az elosztott környezetben:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) – Az MLlib a Spark RDD-kre épülő eredeti API-t tartalmazza.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) – A SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít a Spark DataFrames-en az ML-folyamatok létrehozásához.
* [**MMLSpark**](https://github.com/Azure/mmlspark) – A Microsoft Machine Learning-függvénytár az Apache Sparkhoz (MMLSpark) úgy lett kialakítva, hogy az adatszakértők hatékonyabbá tegyék a Sparkot, növeljék a kísérletezés irámát, és kihasználják az élvonalbeli gépi tanulási technikákat, beleértve a mély tanulást is, nagyon nagy adatkészleteken. Az MMLSpark-könyvtár leegyszerűsíti a pyspark-i modellek létrehozásához végzett gyakori modellezési feladatokat.

### <a name="r-and-ml-services"></a>R- és ML-szolgáltatások

A HDInsight részeként létrehozhat egy HDInsight-fürtöt [az ML-szolgáltatásokkal,](../r-server/r-server-overview.md) amelyek készen állnak a nagy adatkészletekkel és modellekkel való használatra. Ez az új funkció az adatszakértők és a statisztikusok számára egy ismerős R-felületet biztosít, amely igény szerint skálázható a HDInsight-on keresztül, a fürt beállításának és karbantartásának többletterhelése nélkül.

### <a name="azure-machine-learning-and-apache-hive"></a>Az Azure Machine Learning és az Apache Hive

[Az Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net/) eszközöket biztosít a prediktív elemzések modellezéséhez, és egy teljes körűen felügyelt szolgáltatást, amelya prediktív modellek használatra kész webszolgáltatásokként való üzembe helyezéséhez használható. Az Azure Machine Learning eszközöket biztosít a teljes körű prediktív elemzési megoldások felhőbeli létrehozásához, teszteléséhez, működőképessé tételéhez és kezeléséhez. Válasszon egy nagy algoritmuskönyvtárból, használjon webalapú stúdiót modellek létrehozásához, és egyszerűen telepítheti a modellt webszolgáltatásként.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark és Deep learning

[A mély tanulás](https://www.microsoft.com/research/group/dltc/) a gépi tanulás egyik ága, amely *mély neurális hálózatokat* (DNN- eket) használ, amelyeket az emberi agy biológiai folyamatai ihlettek. Sok kutató úgy látja, hogy a mély tanulás a mesterséges intelligencia ígéretes megközelítése. A mélytanulásra példa a beszélt nyelvi fordítók, a képfelismerő rendszerek és a gépi érvelés. Annak érdekében, hogy segítse a saját munkáját a mélytanulásban, a Microsoft kifejlesztette az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkit-ot.](https://www.microsoft.com/en-us/cognitive-toolkit/) Az eszköztárat széles körben használják a Microsoft-termékek széles skálája, a vállalatok világszerte, amelyeknek szükségük van a mélytanulás nagy mértékű telepítésére, valamint a legújabb algoritmusok és technikák iránt érdeklődő diákok.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Forgatókönyv - Score Képek azonosítására minták városfejlesztés

Tekintse át egy példát egy speciális elemzési gépi tanulási folyamat HDInsight használatával.

Ebben a forgatókönyvben látni fogja, hogyan DNNs egy deep learning keretrendszerben előállított, a Microsoft Cognitive Toolkit (CNTK) lehet működőképessé egy Azure Blob Storage-fiókban tárolt nagy lemezképek pyspark egy HDInsight Spark-fürtön. Ez a megközelítés egy közös DNN használati esetre, légikép-besorolásra vonatkozik, és a városfejlesztés legújabb mintáinak azonosítására használható.  Egy előre betanított képbesorolási modellt fog használni. A modell előre be van képezve a [CIFAR-10 adatkészleten,](https://www.cs.toronto.edu/~kriz/cifar.html) és 10 000 visszatartott lemezképre lett alkalmazva.

Ebben a speciális elemzési forgatókönyvben három fő feladat van:

1. Hozzon létre egy Azure HDInsight Hadoop-fürtöt egy Apache Spark 2.1.0-s disztribúcióval.
2. Egyéni parancsfájl futtatásával telepítse a Microsoft Cognitive Toolkit-et egy Azure HDInsight Spark-fürt összes csomópontjára.
3. Töltsön fel egy előre elkészített Jupyter-jegyzetfüzetet a HDInsight Spark-fürtre, és alkalmazzon egy betanított Microsoft Cognitive Toolkit mélytanulási modellt egy Azure Blob Storage-fiókban lévő fájlokra a Spark Python API (PySpark) használatával.

Ez a példa az Alex Krizhevsky, Vinod Nair és Geoffrey Hinton által összeállított és terjesztett CIFAR-10 képkészletet használja. A CIFAR-10 adatkészlet 60 000 32×32 színű képet tartalmaz, amelyek 10 egymást kölcsönösen kizáró osztályba tartoznak:

![Machine Learning példaképek](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Az adatkészletről további információt Alex Krizhevsky [Többrétegű funkciók tanulása apró képekből](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)című témakörben talál.

Az adatkészlet 50 000 képből és 10 000 képből álló tesztkészletté lett particionálva. Az első készlet egy húszrétegű mély konvolúciós maradék hálózati (ResNet) modell betanítására lett használva a Microsoft Cognitive Toolkit használatával, a Cognitive Toolkit GitHub-tárházból származó [oktatóanyag](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) követésével. A fennmaradó 10 000 képet a modell pontosságának tesztelésére használták. Ez az, ahol az elosztott számítástechnika jön szóba: a feladat az előfeldolgozás és pontozási a képek nagyon párhuzamosítható. A mentett képzett modell a kezében, használtuk:

* PySpark a lemezképek és a betanított modell a fürt feldolgozó csomópontjai.
* Python a HDInsight Spark-fürt minden egyes csomópontján a lemezképek előzetes feldolgozása.
* Kognitív eszközkészlet betölteni a modellt, és pontszám az előre feldolgozott képek et minden csomóponton.
* Jupyter notebookok a PySpark-parancsfájl futtatásához, az eredmények összesítéséhez, és a [Matplotlib](https://matplotlib.org/) segítségével a modell teljesítményét.

A 10 000 lemezkép teljes előfeldolgozása/pontozása kevesebb mint egy percet vesz igénybe egy 4 feldolgozócsomókkal rendelkező fürtön. A modell pontosan megjósolja a címkék ~ 9100 (91%) Képek. A tévesztési mátrix a leggyakoribb osztályozási hibákat mutatja be. A mátrix például azt mutatja, hogy a kutyák atyjának címkézése és fordítva gyakrabban fordul elő, mint más címkepárok esetében.

![Machine Learning eredménydiagram](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Próbáld ki!

Kövesse [ezt az oktatóanyagot](../spark/apache-spark-microsoft-cognitive-toolkit.md) a megoldás teljes körű megvalósításához: hozzon létre egy HDInsight Spark-fürtöt, telepítse a Cognitive Toolkit-et, és futtassa a Jupyter-jegyzetfüzetet, amely 10 000 CIFAR-lemezképet készít.

## <a name="next-steps"></a>További lépések

Apache Hive és Azure Machine Learning

* [Az Apache Hive és az Azure Machine Learning végpontok között](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Azure HDInsight-hadoop-fürt használata 1 TB-os adatkészleten](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark és MLLib

* [Gépi tanulás az Apache Sparksegítségével a HDInsighton](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark a Machine Learningsegítségével: Az Apache Spark használata a HDInsightban az épület hőmérsékletének elemzéséhez a Fűtés- és légtechnikai adatok használatával](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: Az Apache Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Kognitív eszközkészlet és mások

* [Adatelemzési Azure virtuális gép](../../machine-learning/data-science-virtual-machine/overview.md)
* [Bemutatkozik a H2O.ai az Azure HDInsightban](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
