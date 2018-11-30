---
title: HDInsight Spark-forgatókönyvek Pysparkkal és Scalával használata az Azure-ban |} A Microsoft Docs
description: Példák a csoportos adatelemzési folyamat, amely a prediktív elemzések az Azure HDInsight Spark-ügyféladatoknak Pysparkkal és Scalával bejárása.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: a7697d2be1a43af93109bb7bbdab50828275f5aa
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446398"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Az Pysparkkal és Scalával az Azure HDInsight Spark adatelemzési útmutatók

Ezek a forgatókönyvek az Azure Spark-fürtön Pysparkkal és Scalával prediktív elemzések használata. A csoportos adatelemzési folyamat lépéseinek követik. A csoportos adatelemzési folyamat áttekintését lásd: [adatelemzési folyamat](overview.md). A Spark on HDInsight áttekintését lásd: [a HDInsight-alapú Spark bemutatása](../../hdinsight/spark/apache-spark-overview.md).

További adatelemzési útmutatók, a csoportos adatelemzési folyamat végrehajtható szerint vannak csoportosítva a **platform** használnak. Lásd: [forgatókönyvek végrehajtása a csoportos adatelemzési folyamat](walkthroughs.md) a példák egy részletezéshez.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Azure Spark használata a PySpark taxi tippek előrejelzése

A [használata a Spark on Azure HDInsight](spark-overview.md) forgatókönyv New York-i taxikra adatait használja e tipp fizetős és fizetendő várt mennyiségű számos előre jelezni. A csoportos adatelemzési folyamat az egy forgatókönyv-használ egy [Azure HDInsight Spark-fürt](https://azure.microsoft.com/services/hdinsight/) szeretné tárolni, ismerje meg, és a visszafejtés adatait a nyilvánosan elérhető NYC taxi út funkciót és adatkészlet díjszabás. Ez a témakör áttekintést állítja be úgy a HDInsight Spark-fürt és a Jupyter PySpark notebookok a forgatókönyv további részében használja. Ezeket a notebookokat bemutatják, hogyan lehet az adatokat, majd hogyan hozhat létre és használhat fel modelleket. A speciális adatok feltárása és modellezés notebook bemutatja, hogyan közé tartozik a kereszt-ellenőrzés, abszolút, a hyper-paraméter és a modell értékelése.

### <a name="data-exploration-and-modeling-with-spark"></a>Adatáttekintés és modellezés a Spark segítségével 
Ismerje meg az adatkészlet, és hozzon létre, pontszám és a gépi tanulási modellek által feldolgozása révén kiértékelheti az [adatokat bináris besorolási és regressziós modelleket hozhat létre a Spark MLlib eszközkészlete](spark-data-exploration-modeling.md) témakör.

### <a name="model-consumption"></a>A használatalapú modell
Az ebben a témakörben létre besorolási és regressziós modellek pontozása kezelésével kapcsolatos információkért lásd: [pontszám és kiértékelheti a Spark használatával összeállított gépi tanulási modellek](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kereszt-ellenőrzési és a hiperparaméter kezdik
Lásd: [speciális adatáttekintés és modellezés a Spark segítségével](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek tanítása az kereszt-ellenőrzési és a hyper-paraméter kezdik.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Azure Spark a Scala használatával taxi tippek előrejelzése

A [használata Scala Spark az Azure-ban](scala-walkthrough.md) forgatókönyv New York-i taxikra adatait használja e tipp fizetős és fizetendő várt mennyiségű számos előre jelezni. Azt jeleníti meg a Scala használata felügyelt gépi tanulási feladatok és a Spark machine learning-kódtár (MLlib) és a könnyen használható csomagok az Azure HDInsight Spark-fürtön. Emellett végigvezeti a feladatok alkotó a [adatelemzési folyamat](https://aka.ms/datascienceprocess): adatbetöltés és feltárása, képi megjelenítés, funkciófejlesztési, modellezés és használatalapú modellt. A modellek tartalmazzák a logisztikai és lineáris regresszió, véletlenszerű erdők és gyorsított fa átmenetes.


## <a name="next-steps"></a>További lépések

A csoportos adatelemzési folyamat alkotó főbb összetevőit, lásd: [csoportos adatelemzési folyamat áttekintése](overview.md).

A csoportos adatelemzési folyamat életciklusa, amelyek segítségével az adatelemzési projektek strukturálása tárgyalását lásd: [csoportos adatelemzési folyamat életciklusa](lifecycle.md). Az életciklus elejétől a végéig, projektek általában követve azok végrehajtásakor, lépéseit ismerteti. 

