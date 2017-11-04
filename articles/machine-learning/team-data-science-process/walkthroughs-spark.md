---
title: "PySpark és Scala használatával az Azure HDInsight Spark-forgatókönyvek |} Microsoft Docs"
description: "Példák az Team tudományos folyamat, amely a prediktív elemzés végrehajtásához az Azure HDInsight Spark Scala és a PySpark ismerteti."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 76c9382cc5210bb5ac8551ebb7b16ecabe0dcd39
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>PySpark és Scala használatával az Azure HDInsight Spark adatok tudományos forgatókönyvek

Ezek a forgatókönyvek PySpark és Scala egy Azure Spark-fürt kell használni a prediktív elemzés. Ezek leírt lépések az adatok tudományos folyamatban. Az Team tudományos folyamat áttekintését lásd: [adatok tudományos folyamat](overview.md). A Spark on HDInsight áttekintését lásd: [a Spark on HDInsight bemutatása](../../hdinsight/spark/apache-spark-overview.md).

További adatok tudományos forgatókönyvek, amelyek az Team tudományos folyamat végrehajtása szerint vannak csoportosítva a **platform** használnak. Lásd: [az Team tudományos folyamat végrehajtása forgatókönyvek](walkthroughs.md) ezekben a példákban az részletezéshez.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Azure Spark a PySpark használatával taxi tippek előrejelzése

A [használja a Spark on Azure HDInsight](spark-overview.md) forgatókönyv előre jelezni, hogy tipp fizetnek, és a tartomány díjak várhatóan fizetendő Győr taxikra adatait használja. Az Team tudományos folyamat forgatókönyv Using használ egy [Azure HDInsight Spark-fürt](https://azure.microsoft.com/services/hdinsight/) tárolására, vizsgálatát, és a beállítást, a nyilvánosan elérhető NYC taxi út visszafejtés adatait és díjszabás adatkészlet. Jelen összefoglaló téma beállítása másolatot egy HDInsight Spark-fürt és a Jupyter PySpark notebookok a forgatókönyv a többi használt. Ezek notebookok megjelenítése hogyan feltérképezheti az adatokat, majd hogyan létrehozását és felhasználását a modellek. A speciális adatok feltárása és modellezési notebook bemutatja, hogyan kereszt-ellenőrzési, abszolút, a hyper-paramétert tartalmaz, és a modell kiértékelése.

### <a name="data-exploration-and-modeling-with-spark"></a>Az adatok feltárása és Spark modellezés 
Megismerkedhet a DataSet adatkészlet és a létrehozása, a pontszám és a gépi tanulási modellek által feldolgozása révén kiértékelheti az [adatok bináris besorolási és regressziós modell létrehozása Spark MLlib eszközkészlete](spark-data-exploration-modeling.md) témakör.

### <a name="model-consumption"></a>Model felhasználás
A létre ebben a témakörben besorolási és regressziós modell pontozása céljából, lásd: [pontszám és értékelje ki a Spark-beépített machine learning modellek](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kereszt-ellenőrzési és hyperparameter abszolút
Lásd: [speciális adatok feltárása és Spark modellezés](spark-advanced-data-exploration-modeling.md) meg, hogyan lehet a modellek betanítása a kereszt-ellenőrzési és a hyper-paraméter abszolút használatával.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Azure Spark Scala használatával taxi tippek előrejelzése

A [használata Scala a Spark on Azure](scala-walkthrough.md) forgatókönyv előre jelezni, hogy tipp fizetnek, és a tartomány díjak várhatóan fizetendő Győr taxikra adatait használja. Azt illusztrálja, hogyan Scala használandó felügyelt gépi tanulási feladatok a Spark gépi tanulási (MLlib) és a SparkML csomagok egy Azure HDInsight Spark-fürtön. Az végigvezeti a feladatok alkotó a [adatok tudományos folyamat](http://aka.ms/datascienceprocess): adatfeldolgozást és a feltárása, a képi megjelenítés, a szolgáltatás mérnöki csapathoz, a modellezési és a model felhasználás. A beépített modellek logisztikai és lineáris regressziós, véletlenszerű erdők és átmenetes súlyozott fák tartalmazza.


## <a name="next-steps"></a>Következő lépések

A fő összetevőből épül fel az Team tudományos folyamat tárgyalását lásd: [Team adatok tudományos folyamat áttekintése](overview.md).

A csoport az tudományos folyamata életciklussal, amely segítségével az adatok tudományos projektek szerkezeti tárgyalását lásd: [Team adatok tudományos folyamat életciklus](lifecycle.md). Az életciklus kezdetétől a végéig, projektek általában követve azok végrehajtásakor, lépéseit mutatja be. 

