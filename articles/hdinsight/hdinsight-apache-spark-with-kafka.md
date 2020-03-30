---
title: Apache Spark-alapú streamelés az Apache Kafkával – Azure HDInsight
description: Ismerje meg, hogyan továbbíthat adatokat az Apache Kafkába vagy azOn kívüli adatokDStreams használatával. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327399"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) példa az Apache Kafka segítségével a HDInsighton

Ismerje meg, hogyan továbbíthat adatokat az [Apache](https://spark.apache.org/) [Kafka-ba](https://kafka.apache.org/) a HDInsight-on a [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html)segítségével. Ez a példa egy [Jupyter-jegyzetfüzetet](https://jupyter.org/) használ, amely a Spark-fürtön fut.

> [!NOTE]  
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

> [!IMPORTANT]  
> Ez a példa a DStreams, amely egy régebbi Spark streaming technológia. Egy példa, amely az újabb Spark streamelési funkciókat használ, tekintse meg a [Spark strukturált streamelés apache Kafka dokumentum.](hdinsight-apache-kafka-spark-structured-streaming.md)

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka a HDInsight-on nem biztosít hozzáférést a Kafka brókerekhez a nyilvános interneten keresztül. Bármi, ami beszél a Kafka kell lennie ugyanabban az Azure virtuális hálózat, mint a csomópontok a Kafka-fürtben. Ebben a példában a Kafka és a Spark-fürtök egy Azure virtuális hálózatban találhatók. Az alábbi ábra bemutatja, hogyan folyik a kommunikáció a fürtök között:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Bár maga a Kafka a virtuális hálózaton belüli kommunikációra korlátozódik, a fürt más szolgáltatásai, például az SSH és az Ambari az interneten keresztül érhetők el. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Bár manuálisan hozhat létre egy Azure-beli virtuális hálózatot, a Kafka- és a Spark-fürtöket, egyszerűbb en azure Resource Manager-sablont használni. Az alábbi lépésekkel üzembe helyezhet egy Azure-beli virtuális hálózatot, a Kafka- és Spark-fürtöket az Azure-előfizetésében.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Az Azure Resource Manager **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**sablon a helyen található.

    > [!WARNING]  
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. A sablon egy három feldolgozó csomópontot tartalmazó Kafka-fürtöt hoz létre.

    Ez a sablon egy HDInsight 3.6-os fürtöt hoz létre a Kafka és a Spark számára is.

1. Az **egyéni telepítés** szakasz bejegyzéseinek feltöltéséhez használja az alábbi információkat:

    |Tulajdonság |Érték |
    |---|---|
    |Erőforráscsoport|Hozzon létre egy csoportot, vagy jelöljön ki egy meglévőt.|
    |Hely|Válasszon ki egy földrajzilag közeli helyet.|
    |Alapfürt neve|Ez az érték a Spark és a Kafka fürtök alapneve. A **hdistreaming** beírása például __spark-hdistreaming__ nevű Spark-fürtöt és **kafka-hdistreaming**nevű Kafka-fürtöt hoz létre.|
    |Fürt bejelentkezési felhasználóneve|A Spark és a Kafka fürtök rendszergazdai felhasználóneve.|
    |Fürt bejelentkezési jelszava|A Spark és a Kafka fürtök rendszergazdai felhasználói jelszava.|
    |SSH-felhasználónév|A Spark és a Kafka fürtök létrehozásához az SSH-felhasználó.|
    |SSH-jelszó|A Spark és a Kafka fürtök SSH-felhasználójának jelszava.|

    ![A HDInsight egyéni telepítési paraméterei](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

1. Végül válassza a **Vásárlás**lehetőséget. A fürtök létrehozása körülbelül 20 percet vesz igénybe.

Az erőforrások létrehozása után megjelenik egy összegző lap.

![Erőforráscsoport összegzése a virtuális hálózathoz és a fürtökhöz](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Figyelje meg, hogy a HDInsight-fürtök neve **spark-BASENAME** és **kafka-BASENAME**, ahol a BASENAME a sablonhoz megadott név. Ezeket a neveket a későbbi lépésekben használhatja, amikor a fürtökhöz csatlakozik.

## <a name="use-the-notebooks"></a>A jegyzetfüzetek használata

A dokumentumban ismertetett példa kódja [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)a következő helyen érhető el: .

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Mivel a jelen dokumentumban szereplő lépések mindkét fürtöt ugyanabban az Azure-erőforráscsoportban hozják létre, törölheti az erőforráscsoportot az Azure Portalon. A csoport törlése eltávolítja az összes olyan erőforrást, amelyet a jelen dokumentum, az Azure virtuális hálózat és a fürtök által használt tárfiók követésével hoz létre.

## <a name="next-steps"></a>További lépések

Ebben a példában megtanulta, hogyan használhatja a Sparkot a Kafka olvasásához és írásához. Az alábbi hivatkozásoksegítségével további módszereket fedezhet fel a Kafka használatára:

* [Ismerkedés az Apache Kafkával a HDInsighton](kafka/apache-kafka-get-started.md)
* [A MirrorMaker segítségével létrehozhatja az Apache Kafka másolatát a HDInsight-on](kafka/apache-kafka-mirroring.md)
* [Az Apache Storm használata az Apache Kafkával a HDInsighton](hdinsight-apache-storm-with-kafka.md)
