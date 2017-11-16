---
title: Adatfolyam-Kafka - Azure HDInsight az Apache Spark on |} Microsoft Docs
description: "Megtudhatja, hogyan használja Spark az Apache Spark on az adatfolyam adatok virtuális gépbe vagy onnan Apache Kafka DStreams használatával. Ebben a példában a HDInsight Spark a Jupyter notebook használatával adatok folyamatos átviteléhez."
keywords: "kafka például kafka zookeeper, spark streamelési kafka, spark streamelési kafka – példa"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 652719a80133434ecb31c00738dcda261cfa0683
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) például Kafka a HDInsight a

Útmutató Spark Apache Spark adatfolyam adatok vagy a HDInsight a DStreams Apache Kafka abból. A példa a Spark-fürtön futó Jupyter notebook.
> [!NOTE]
> A jelen dokumentumban leírt lépések, amely tartalmazza a Spark on HDInsight és egy HDInsight-fürt Kafka Azure erőforráscsoport-csoport létrehozása. Ezeken a fürtökön is egy Azure virtuális hálózatot, amely lehetővé teszi a Kafka közvetlenül kommunikálni a Spark-fürtön belül található a fürtön.
>
> Amikor elkészült, a jelen dokumentumban leírt lépések, ne felejtse el a felesleges költségek elkerülése érdekében a fürtök törlése.

## <a name="create-the-clusters"></a>A fürtök létrehozása

A HDInsight Apache Kafka nem férhet hozzá a Kafka brókerek a nyilvános interneten keresztül. A Kafka fürt csomópontja azonos Azure virtuális hálózaton, amelyeket a kiszolgálóhoz csatlakozik Kafka kell lennie. Ehhez a példához a Kafka és a Spark-fürtök egy Azure virtuális hálózat található. Az alábbi ábra bemutatja, hogyan kommunikációs a fürtök között zajló kommunikációról:

![Spark és Kafka fürtök egy Azure virtuális hálózatban ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Bár Kafka, maga a virtuális hálózaton belüli kommunikáció korlátozódik, például az SSH és az Ambari a fürt más szolgáltatások az interneten keresztül érhető el. A hdinsight eszközzel elérhető nyilvános portokon további információkért lásd: [portok és a HDInsight által használt URI-azonosítók](hdinsight-hadoop-port-settings-for-services.md).

Létrehozhat egy Azure virtuális hálózatra, Kafka, és a Spark-fürtök manuális, célszerűbb Azure Resource Manager sablonnal. Az alábbi lépések segítségével telepíthet egy Azure virtuális hálózatot, Kafka, és a Spark-fürtök az Azure-előfizetéshez.

1. A következő gomb segítségével jelentkezzen be az Azure-ba, és nyissa meg a sablon az Azure portálon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Az Azure Resource Manager sablon itt található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. Ez a sablon a három munkavégző csomópontokhoz tartalmaz Kafka fürtöt hoz létre.

    Ez a sablon egy HDInsight 3.6 fürtöt Kafka és Spark hoz létre.

2. A következő információk segítségével a feltöltik a **egyéni telepítési** szakasz:
   
    ![HDInsight egyéni központi telepítés](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürthöz.

    * **Hely**: Adjon meg egy földrajzilag Önhöz legközelebb eső helyet.

    * **Fürt neve kiinduló**: Ez az érték használható a Spark alap néven és Kafka fürtök. Ha például **hdi** nevű Spark-fürt létrehozása __spark-hdi__ és nevű Kafka fürt **kafka-hdi**.

    * **A fürt bejelentkezési felhasználónevét**: A rendszergazda felhasználóneve a Spark és Kafka fürt.

    * **A fürt bejelentkezési jelszó**: a Spark és Kafka fürt rendszergazdai jelszóval.

    * **SSH-felhasználónév**: az SSH-felhasználó a Spark- és Kafka fürtök létrehozása.

    * **SSH-jelszónak**: a Spark és Kafka fürt SSH-felhasználó jelszavát.

3. Olvassa el a **feltételek és kikötések**, majd válassza ki **elfogadom a feltételeket és a fenti feltételek**.

4. Végül ellenőrizze **rögzítés az irányítópulton** majd **beszerzési**. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Az erőforrások létrehozása után, egy összegző lap jelenik meg.

![A virtuális hálózat és a fürtök összegzése erőforráscsoport](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Figyelje meg, hogy a HDInsight-fürtök neve **spark-BASENAME** és **kafka-BASENAME**, ahol BASENAME a sablonhoz megadott név. Ezeket a neveket a későbbi lépésekben használja, a fürtök történő csatlakozás során.

## <a name="use-the-notebooks"></a>A notebookok használata

A jelen dokumentumban ismertetett példa kódja megtalálható [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Ez a példa befejezéséhez kövesse a `README.md`.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

A jelen dokumentumban leírt lépések az azonos Azure erőforráscsoport mindkét fürtöket létrehozni, mert az erőforráscsoportot az Azure portálon törölheti. A csoport törlése eltávolítja a következő Ez a dokumentum, az Azure-beli virtuális hálózatra és a fürt által használt tárfiók által létrehozott összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Ebben a példában megtanulta, hogyan használható a Spark olvasási és írási Kafka. Az alábbi hivatkozások segítségével felderíteni a más módon történő együttműködésre Kafka:

* [Ismerkedés az Apache Kafka a HDInsight-on](kafka/apache-kafka-get-started.md)
* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](kafka/apache-kafka-mirroring.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](hdinsight-apache-storm-with-kafka.md)

