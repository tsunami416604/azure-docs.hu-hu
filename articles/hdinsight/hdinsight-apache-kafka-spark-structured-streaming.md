---
title: Az Apache Spark strukturált stream használata a Kafkával – Azure HDInsight | Microsoft Docs
description: Megtudhatja, hogyan használhatja az Apache Spark streamelést (DStream) az adatok az Apache Kafkába való betöltéséhez, illetve az onnan való exportálásához. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>A Spark strukturált stream használata a Kafkával a HDInsighton

Megtudhatja, hogyan használhatja a Spark strukturált streamet az adatok olvasásához az Apache Kafkából az Azure HDInsighton.

A Spark strukturált stream egy Spark SQL-alapú streamfeldolgozó rendszer. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében. A strukturált streamelésről további információt az Apache.org oldalon lévő [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) (Strukturált streamelés programozási útmutatója [Alfa]) szakaszban talál.

> [!IMPORTANT]
> Ez a példa a Spark 2.2-t használja a HDInsight 3.6-on.
>
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Spark-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek. 

Az alábbi ábra a Spark és a Kafka közötti kommunikáció áramlását mutatja be.

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

A kényelem érdekében a következő lépések Azure Resource Manager-sablonnal hoznak létre Kafka- és Spark-fürtöket egy virtuális hálózatban.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Az Azure Resource Manager-sablon a következő helyen található: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Ez a sablon a következő erőforrásokat hozza létre:

    * Egy Kafka on HDInsight 3.6-fürt.
    * Egy Spark 2.2.0 on HDInsight 3.6-fürt.
    * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

    > [!IMPORTANT]
    > Az ebben a példában használt strukturált stream a Spark on HDInsight 3.6-os verzióját igényli. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

    | Beállítás | Érték |
    | --- | --- |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | Az erőforrásokat tartalmazó erőforráscsoport. |
    | Hely | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
    | Spark-fürt neve | A Spark-fürt neve. |
    | Kafka-fürt neve | A Kafka-fürt neve. |
    | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
    | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
    | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |
   
    ![A testreszabott sablon képernyőképe](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. 

> [!NOTE]
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="get-the-notebook"></a>A notebook beszerzése

A dokumentumban leírt példa kódja a következő helyen található: [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>A notebookok feltöltése

Ha fel szeretné tölteni a notebookot a projektből a Spark on HDInsight-fürtre, használja a következő lépéseket:

1. A webböngészőben csatlakozzon a Spark-fürtön lévő Jupyter notebookhoz. A következő URL-címben cserélje le a `CLUSTERNAME` elemet a __Spark__-fürt nevére.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

2. Az oldal jobb felső oldalán lévő __Feltöltés__ gombbal töltse fel a __spark-structured-streaming-kafka.ipynb__ fájlt a fürtbe. A feltöltés elindításához válassza a __Megnyitás__ elemet.

    ![Notebook kiválasztása és feltöltése a feltöltés gombbal](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Válassza ki a KafkaStreaming.ipynb fájlt](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Keresse meg a __spark-structured-streaming-kafka.ipynb__ bejegyzést a notebookok listájában, és válassza ki a mellette lévő __Feltöltés__ gombot.

    ![A notebook feltöltéséhez használja a KafkaStreaming.ipynb bejegyzéshez tartozó feltöltés gombot](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>A notebook használata

A fájlok feltöltése után válassza a __spark-structured-streaming-kafka.ipynb__ bejegyzést a notebook megnyitásához. A Spark strukturált stream Kafka on HDInsight eszközzel való használatának megismeréséhez kövesse a notebookban lévő utasításokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Spark strukturált stream használatát, a következő dokumentumokból még többet tudhat meg a Spark és a Kafka használatáról:

* [Spark streamelés (DStream) használata a Kafkával](hdinsight-apache-spark-with-kafka.md).
* [A Jupyter Notebook és a Spark on HDInsight használatának első lépései](spark/apache-spark-jupyter-spark-sql.md)