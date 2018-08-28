---
title: Az Apache Spark-streamelés a Kafkával – Azure HDInsight
description: 'Útmutató: Spark Apache Spark használatával adatok streamelése vagy onnan máshová az Apache Kafka DStreams használatával. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.'
keywords: a kafka-például kafka zookeeper, spark-streamelés a kafka, a spark Stream kafka példa
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: jasonh
ms.openlocfilehash: d06e9d26051fbfafc4d717ec180e8760157aefd9
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094166"
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark Stream (DStream) Példa a HDInsight alatt futó Kafka

Útmutató: Spark Apache Spark használatával adatok streamelése vagy onnan máshová az Apache Kafka on HDInsight használatával DStreams. Ez a példa, amely a Spark-fürtön fut egy Jupyter notebookot használja.

> [!NOTE]
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

> [!IMPORTANT]
> Ebben a példában DStreams, amely régebbi a Spark streamelési technológia. Egy példa, amely újabb a Spark Stream funkciókat használja: a [Spark strukturált Stream használata a Kafkával](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentumot.

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. Semmit, a Kafka-szel kommunikáló, a Kafka-fürt csomópontjainak azonos Azure virtuális hálózatban kell lennie. Ebben a példában a Kafka és a Spark-fürtök Azure virtuális hálózatban találhatók. Az alábbi ábrán látható, hogy a fürtök közötti kommunikáció áramlását:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka magát a virtuális hálózaton belüli kommunikációra korlátozódik, bár egyéb szolgáltatások a fürtön, például az SSH és az Ambari az interneten keresztül érhető el. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Bár létrehozhat egy Azure virtuális hálózatra, a Kafka, és a Spark-fürtök manuális, egyszerűbb legyen a használata az Azure Resource Manager-sablon. Az alábbi lépések segítségével üzembe helyezése az Azure virtuális hálózat, a Kafka és Spark-fürtök az Azure-előfizetéshez.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Az Azure Resource Manager-sablon a következő helyen található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. A sablon egy három feldolgozó csomópontot tartalmazó Kafka-fürtöt hoz létre.

    Ez a sablon létrehoz egy HDInsight 3.6-fürt Kafka és Spark.

2. Az alábbi információk segítségével feltöltik a a **egyéni üzembe helyezés** szakaszban:
   
    ![HDInsight-egyéni üzembe helyezés](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürt.

    * **Hely**: válasszon, földrajzilag közeli helyet.

    * **Fürt neve alapján**: ezt az értéket használja, a Spark alapnevét, és a Kafka-fürtök. Ha például **hdi** nevű egy Spark-fürtöt hoz létre __spark-hdi__ és a egy nevű Kafka-fürt **kafka-hdi**.

    * **A fürt bejelentkezési név**: a Spark és Kafka-fürtök rendszergazdai felhasználóneve.

    * **A fürt bejelentkezési jelszavának**: a Spark és Kafka-fürtök rendszergazdai felhasználójának jelszava.

    * **SSH-felhasználónév**: az SSH-felhasználó, a Spark és Kafka-fürtök létrehozása.

    * **SSH-jelszó**: a Spark és Kafka-fürtök az SSH-felhasználó jelszavát.

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. Körülbelül 20 perc alatt létrehozni a fürtöket vesz igénybe.

Az erőforrások létrehozása után megjelenik egy összegző lapja.

![A vnet és fürtök összefoglaló erőforráscsoport](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Figyelje meg, hogy a neve, a HDInsight-fürtök **spark-BASENAME** és **kafka-BASENAME**, ahol a sablonhoz megadott BASENAME. Ezeket a neveket használni a későbbi lépésekben a fürtök való kapcsolódás során.

## <a name="use-the-notebooks"></a>A notebookok használata

A dokumentumban leírt példa kódja a következő helyen található: [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Ebben a példában végrehajtásához kövesse a `README.md`.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Mivel a jelen dokumentumban leírt lépések az Azure-erőforráscsoport mindkét fürtöket létrehozni, törölheti az erőforráscsoportot az Azure Portalon. A csoport törlése eltávolítja az ebben a dokumentumban, az Azure Virtual Network és storage-fiókot a fürtök által használt alábbi létrehozott összes erőforrást.

## <a name="next-steps"></a>További lépések

Ebben a példában útmutatóból megtudhatta, hogyan olvasási és írási Kafka a Spark használatával. Az alábbi hivatkozások segítségével Fedezzen fel más módokat a Kafka használata:

* [A HDInsight Apache Kafka használatának első lépései](kafka/apache-kafka-get-started.md)
* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](kafka/apache-kafka-mirroring.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](hdinsight-apache-storm-with-kafka.md)

