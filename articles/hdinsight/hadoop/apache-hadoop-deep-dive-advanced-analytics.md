---
title: Részletes bemutatója - advanced analytics - Azure HDInsight |} Microsoft Docs
description: Ismerje meg, hogyan speciális elemzésekre algoritmusokat használ a big Data típusú adatok feldolgozásához.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f121a1f95eed04bf8086c03a5d4406c092dc9445
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400673"
---
# <a name="deep-dive---advanced-analytics"></a>Részletes bemutatója - speciális elemzés

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Mi a kiemelt elemzés hdinsight?

A HDInsight lehetővé teszi a strukturált, strukturálatlan nagy mennyiségű az értékes információkat, és a gyorsan változó adatok. Speciális elemzésekre a magas szinten méretezhető architektúra, statisztikai használata és a machine learning modellek és az intelligens irányítópultok biztosítja, hogy a jelentéssel bíró, amelyen. Gépi tanulás, vagy *prediktív elemzési*, azonosíthatja, és ismerje meg, a kapcsolatok előrejelzéseket készítsen, és ismerteti a döntések az adatok az algoritmus használ.

## <a name="advanced-analytics-process"></a>Bővített analitikát folyamat

![Folyamat](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Miután állapította meg az üzleti problémát, és elindította gyűjtése és az adatok feldolgozása, kell létrehoznia egy modell a a kérdése kívánja előre jelezni. A modell egy vagy több gépi tanulási algoritmusok használni előrejelzés üzleti igényeinek legjobban megfelelő típusú.  A legtöbb, az adatok és a többi kiértékelésére, használja a modell betanításához használandó. 

Miután létrehozása, betölteni, teszteléséhez, és a modell kiértékelése, a következő lépéssel hoz a modell rendszerbe állítása, így biztosítja a kérdésekre adott válaszok kezdődik. Az utolsó lépés a modell teljesítmény figyeléséhez és szükség szerint igazítható. 

## <a name="common-types-of-algorithms"></a>Általános típusú algoritmusokat

Speciális elemzési megoldásokat biztosítanak gépi tanulási algoritmusok. Ez a kategóriák algoritmusok és a kapcsolódó általános üzleti alkalmazási esetek összegzését.

![Machine Learning használati esetek](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

A legjobban záró aránytól kiválasztásával, valamint kell figyelembe vennie, függetlenül attól, hogy képzési adatokkal. Gépi tanulási algoritmusok kategóriái a következők:

* Felügyelt - algoritmus kell kell képezni a címkével ellátott adatok, mielőtt eredmények
* Félig felügyelt - algoritmus által extra tárolók által egy trainer interaktív lekérdezéssel, amelyek nem álltak rendelkezésre a képzési kezdeti szakaszában kiegészíthető
* Felügyelt - algoritmus nem igényel képzési adatok 
* Megerősítése - algoritmus által használt szoftver ügynökök annak meghatározásához, hogy épp ezért tökéletes választás belül egy adott környezetben (robotics a gyakran használt)


| Algoritmus kategória| Használat | Learning típusa | Algoritmusok |
| --- | --- | --- | -- |
| Besorolás | Személyek vagy dolgot csoportokba besorolása | Felügyelt | Döntési fák, a logisztikai regresszió, a Neurális hálózatokat |
| Fürtözés | Példák készlete homogén csoportokba osztja | Felügyeletlen | Fürtszolgáltatás K-közép |
| Minta észlelése | Az adatok gyakori társítások azonosítása | Felügyeletlen | Társítási szabályok |
| Regressziós | Numerikus eredmények előrejelzése | Felügyelt | Lineáris regresszió, a Neurális hálózatokat |
| Megerősítése | Határozza meg a robotokat optimális működése | Megerősítése | Monte Carlo szimulációja, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Gépi tanulási a HDInsight-on

HDInsight több gépi tanulási a beállítások egy speciális elemzésekre munkafolyamat rendelkezik:

* [Gépi tanulás és Spark](#machine-learning-and-spark)
* [R és R Server](#r-and-r-server)
* [Az Azure Machine Learning és a Hive](#azure-machine-learning-and-hive)
* [Spark és részletes tanulás](#spark-and-deep-learning)

### <a name="machine-learning-and-spark"></a>Gépi tanulás és Spark

[HDInsight Spark](../spark/apache-spark-overview.md) van egy Azure által üzemeltetett elérhető [Spark](http://spark.apache.org/), egy egységesített, nyissa meg a forrás, a párhuzamos adatok feldolgozást végző keretrendszer, amely használ a memórián belüli feldolgozást a Big Data-elemzések növelése érdekében. A Spark program sebességét, a könnyű, valamint a kifinomult analytics lett tervezve. A Spark memóriában elosztott tárolt számítási képességei jól funkcionálnak a szerepel a machine learning és a graph számítások iteratív algoritmusaival a. 

Nincsenek három algoritmikus modellezési képességekkel kerüljön az elosztott környezetben méretezhető machine learning szalagtárak:

* [**MLlib** ](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib tartalmazza az eredeti API Spark RDDs platformra épül.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML csomag egy újabb, amely egy magasabb szintű API ML folyamatok megalkotásához Spark DataFrames platformra épül.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – a Microsoft Machine Learning-könyvtárral az Apache Spark (MMLSpark) adatszakértőkön hatékonyabbá téve a kísérleti növelésére, valamint a legmodernebb machine learning szolgáltatást használja, a Spark célja technikák, beleértve a mély learning, nagy méretű adatkészleteken. A MMLSpark könyvtárban a PySpark modelljei felépítéséhez közös modellezési feladatok egyszerűbbé teszi. 

### <a name="r-and-r-server"></a>R és R Server

HDInsight részeként a HDInsight-fürtöt hozhat létre [R Server](../r-server/r-server-overview.md) nagy adatkészletek és modellek használatra kész. Ez az új képesség biztosít az adatelemzők és egy ismert R felülettel is méretezhető Statisztikusok igény szerinti HDInsight keresztül nem növeli a fürt beállítása és karbantartása.

### <a name="azure-machine-learning-and-hive"></a>Az Azure Machine Learning és a Hive

[Az Azure Machine Learning Studio](https://studio.azureml.net/) prediktív elemzési modell, valamint egy teljes körűen felügyelt szolgáltatás segítségével üzembe helyezheti prediktív modelljeit felhasználásához felhasználásra kész webszolgáltatásként eszközöket biztosít. Az Azure Machine Learning eszközeivel Ezenfelül teljes prediktív elemzési megoldásokat a felhőben gyorsan hozzon létre, tesztelheti, azok és felügyelhet. Jelöljön ki egy nagy méretű algoritmustár, létrehozási modelleket egy webalapú studio használata és egyszerűen a webszolgáltatásként a modell rendszerbe állítása.

### <a name="spark-and-deep-learning"></a>Spark és részletes tanulás

[Részletes tanulási](https://www.microsoft.com/research/group/dltc/) gépi tanulás által használt ága *mély Neurális hálózatokat* (DNNs) által az emberi agy biológiai folyamatokat ösztönző. Sok kutatói mély learning egy mesterséges eszközintelligencia ígéret megközelítés jelenik meg. Néhány példa a mély tanulási szóbeli nyelvi fordító, a lemezkép felismerés rendszerek és a gép indoklást. A saját munkahelyi mély szeretnének előzetes érdekében a Microsoft az ingyenes, könnyen használható, nyílt forráskódú kifejlesztette [Microsoft kognitív eszközkészlet](https://www.microsoft.com/cognitive-toolkit/). Az eszközkészlet használatos nagymértékben számos Microsoft-termékek, vállalatok világszerte léptékű mély tanulási telepítéséhez van szükség, és a legújabb algoritmusok és technikák érdekelt diákoknál. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Forgatókönyv – pontszám képek városfejlesztés minták azonosításához

Tekintsük át, egy speciális elemzésekre gépi, HDInsight eszközzel csővezeték tanulási példát.

Ebben a forgatókönyvben láthatja, hogyan hozott létre DNNs mély tanulási-keretrendszer Microsoft kognitív Toolkit (CNTK) tartozó, egy PySpark használata a HDInsight Spark-fürt Azure Blob Storage-fiókban tárolt nagy méretű kép gyűjtemények pontozó operationalized is lehet. Ezt a módszert használja egy közös DNN használati eset, légifelvételes kép besorolás alkalmazott, és a városfejlesztés legutóbbi minták azonosításához használható.  Egy előre képzett kép besorolási modell fogja használni. A modell az előre képzett a a [CIFAR 10-es dataset](https://www.cs.toronto.edu/~kriz/cifar.html) és 10 000 visszatartott képek alkalmazták.

Nincsenek speciális elemzésekre példánkban három kulcsfontosságú feladatokat:

1. Az Apache Spark 2.1.0 terjesztési hozzon létre egy Azure HDInsight Hadoop-fürt. 
2. Microsoft kognitív eszközkészlet telepítése egy Azure HDInsight Spark-fürt minden csomópontján egyéni parancsfájl futtatása. 
3. A HDInsight Spark-fürt által alkalmazni a betanított Microsoft kognitív eszközkészlet mély tanulása egy Azure Blob Storage-fiókot a Spark Python API-val (PySpark) a fájlokat egy előre elkészített Jupyter notebook feltölteni. 

A példa a lefordított és elosztott Alex Krizhevsky, Vinod Nair és Geoffrey Hinton CIFAR-10 lemezképet. A CIFAR-10 az adatkészletben 60 000 32 x 32 lemezképek 10 egymást kölcsönösen kizáró osztályok tartozó szín:

![Képek](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

A dataset a további részletekért lásd: Alex Krizhevsky [tanulási több rétegek a funkció a apró lemezképeinek](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Az adatkészlet egy képzési 50 000 képek és lemezképek 10 000 TesztKészlet lett particionálva. Az első set lett megadva a következő Microsoft kognitív eszközkészlet segítségével a mélyhivatkozással réteg erdőtopológia kezelésre convolutional maradék hálózati (ResNet) modell betanításához [ebben az oktatóanyagban](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) a kognitív eszközkészlet GitHub-tárházban lévő. A fennmaradó 10 000 képek használtak, a modell pontosságát tesztelése. Ez az elosztott számítások honnan szerepet: a feladat előzetesen feldolgozni, és a képek pontozási magas párhuzamosítható. Az aktuális mentett betanított modell használtuk:

* A lemezképek és a fürt munkavégző csomópontokhoz betanított modell PySpark.
* Python előzetesen feldolgozni a képek a HDInsight Spark-fürt mindegyik csomópontján.
* Kognitív eszközkészlet betöltése a modell és a pontszám Előfeldolgozott minden egyes csomóponton.
* A PySpark parancsfájl futtatása a Jupyter notebookok összesíteni az eredményeket, és a [Matplotlib](https://matplotlib.org/) a modell teljesítmény megjelenítéséhez.

A teljes előfeldolgozása/pontozás 10 000 lemezkép időt vesz igénybe egy percnél 4 munkavégző csomópontokhoz rendelkező fürtön. A modell pontosan képes a címkék ~ 9,100 (91 %) képek. A félreértések mátrix besorolás leggyakrabban előforduló hibákat mutatja be. Például a mátrix kutya macska, és ez fordítva is igaz mislabeling történik, hogy látható-e nagyobb gyakran más címke párok esetében.

![Results (Eredmények)](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Próbálja ki!

Hajtsa végre a [ebben az oktatóanyagban](../spark/apache-spark-microsoft-cognitive-toolkit.md) megvalósításához a megoldás-végpontok: a telepítő egy HDInsight Spark-fürt, kognitív eszközkészlet telepítése és futtatása a Jupyter Notebook, amely 10 000 CIFAR képek pontszámaihoz.

## <a name="next-steps"></a>További lépések

Hive és az Azure gépi tanulás

* [Hive és az Azure Machine Learning-végpont](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Az 1 TB-os dataset Azure HDInsight Hadoop-fürtök használata](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Spark és MLLib

* [Gépi tanulási a Spark on HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](../spark/apache-spark-machine-learning-mllib-ipython.md)

Részletes tanulási és kognitív eszközkészlet mások

* [Embarrassingly párhuzamos kép besorolást, az Azure HDInsight Spark kognitív eszközkészlet és TensorFlow használatával](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
* [Adattudomány Azure virtuális gép](../../machine-learning/data-science-virtual-machine/overview.md)
* [Az Azure hdinsight H2O.ai bemutatása](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
