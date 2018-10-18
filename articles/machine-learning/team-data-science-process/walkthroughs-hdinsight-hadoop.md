---
title: HDInsight Hadoop adatelemzési útmutatók Hive használatával egy Azure-on |} A Microsoft Docs
description: Példák a csoportos adatelemzési folyamat, amely a prediktív elemzések az Azure HDInsight hadoop Hive használatával bejárása.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 2aa64d6dc144e85e1a0f03b8548fa47dba91a2eb
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393159"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Hive használatával egy Azure HDInsight Hadoop adatelemzési útmutatók 

Ezek az útmutatók a Hive használata egy HDInsight Hadoop-fürtöt a prediktív elemzések. A csoportos adatelemzési folyamat lépéseinek követik. A csoportos adatelemzési folyamat áttekintését lásd: [adatelemzési folyamat](overview.md). Bevezetés az Azure HDInsight, lásd: [Azure HDInsight bemutatása, Hadoop-technológiák és a Hadoop-fürtök](../../hdinsight/hadoop/apache-hadoop-introduction.md).

További adatelemzési útmutatók, a csoportos adatelemzési folyamat végrehajtható szerint vannak csoportosítva a **platform** használnak. Lásd: [forgatókönyvek végrehajtása a csoportos adatelemzési folyamat](walkthroughs.md) a példák egy részletezéshez.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>HDInsight Hadoop Hive-val taxi tippek előrejelzése

A [használata a HDInsight Hadoop-fürtök](hive-walkthrough.md) forgatókönyv New York-i taxikra adatait használja előrejelzésére: 

- Tipp: fizetős e 
- Tipp összegek eloszlása

A forgatókönyv valósul meg, a Hive használatával egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/). Megtudhatja, hogyan tárolhatja, ismerje meg, és a egy nyilvánosan elérhető NYC funkciót engineer adatait taxiköltség utazást és diszkont adatkészlet. Is használhatja az Azure Machine Learning készíthet és helyezhet üzembe a modellt.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Hirdetmény kattintások és a HDInsight Hadoop Hive használatának előrejelzése

A [használata Azure HDInsight Hadoop-fürtök 1 TB méretű adatkészleteken](hive-criteo-walkthrough.md) egy nyilvánosan elérhető forgatókönyv [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) kattintson az adatkészlet tipp fizetős e, és a tartomány a várt mennyiségű előrejelzésére. A forgatókönyv valósul meg, a Hive használatával egy [Azure HDInsight Hadoop-fürt](https://azure.microsoft.com/services/hdinsight/) szeretné tárolni, ismerje meg, a visszafejtés funkciót, és le a mintaadatokat. Az Azure Machine Learning segítségével hozhat létre, betanítását és pontozását egy bináris osztályozási modell előrejelzésének, hogy a felhasználó rákattint az alkalmazásban a hirdetmény forráshierarchiából. A forgatókönyv azt állapítja meg, közzé egy, ezek a modellek webszolgáltatásként való ismertető.


## <a name="next-steps"></a>További lépések

A csoportos adatelemzési folyamat alkotó főbb összetevőit, lásd: [csoportos adatelemzési folyamat áttekintése](overview.md).

A csoportos adatelemzési folyamat életciklusa, amelyek segítségével az adatelemzési projektek strukturálása tárgyalását lásd: [csoportos adatelemzési folyamat életciklusa](lifecycle.md). Az életciklus elejétől a végéig, projektek általában követve azok végrehajtásakor, lépéseit ismerteti. 

