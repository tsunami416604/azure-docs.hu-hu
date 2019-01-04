---
title: Az Apache Spark az Apache kafka platformmal – Azure HDInsight streaming
description: Ismerje meg, hogyan használható az Apache Spark adatok, vagy onnan máshová az Apache Kafka használatával DStreams. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
keywords: a kafka-például kafka zookeeper, spark-streamelés a kafka, a spark Stream kafka példa
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5f4b7994ad5061c64021f3625f42ac028cbee859
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653407"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark Stream (DStream) például az Apache Kafka on HDInsight

Ismerje meg, hogyan használható [Apache Spark](https://spark.apache.org/) be vagy ki az adatok [Apache Kafka](https://kafka.apache.org/) a HDInsight használatával [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Ez a példa egy [Jupyter Notebook](https://jupyter.org/) , amely futtatja a Spark-fürtön.

> [!NOTE]  
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

> [!IMPORTANT]  
> Ebben a példában DStreams, amely régebbi a Spark streamelési technológia. Egy példa, amely újabb a Spark Stream funkciókat használja: a [Spark strukturált Stream az Apache kafka platformmal](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentumot.

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
   
    * **Erőforráscsoport**: Hozzon létre egy csoportot, vagy válasszon ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürt.

    * **Hely**: Válasszon egy helyet, földrajzilag közel.

    * **Fürt neve alapján**: Ez az érték a Spark és Kafka-fürtök alapnevét szolgál. Ha például **hdistreaming** nevű egy Spark-fürtöt hoz létre __spark-hdistreaming__ és a egy nevű Kafka-fürt **kafka-hdistreaming**.

    * **A fürt bejelentkezési név**: Az a Spark és Kafka-fürtök rendszergazdai felhasználójának neve.

    * **A fürt bejelentkezési jelszavának**: A Spark és Kafka-fürtök rendszergazdai felhasználójának jelszava.

    * **SSH-felhasználónév**: Az SSH-felhasználó számára a Spark és Kafka-fürtök létrehozásához.

    * **SSH jelszó**: A Spark és Kafka-fürtök az SSH-felhasználó jelszavát.

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül válassza **beszerzési**. Körülbelül 20 perc alatt létrehozni a fürtöket vesz igénybe.

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
* [A MirrorMaker használata a HDInsight az Apache Kafka replikájának létrehozása](kafka/apache-kafka-mirroring.md)
* [Az Apache Kafka on HDInsight az Apache Storm használata](hdinsight-apache-storm-with-kafka.md)

