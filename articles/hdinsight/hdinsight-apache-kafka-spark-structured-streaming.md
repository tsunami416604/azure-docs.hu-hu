---
title: 'Oktatóanyag: Az Apache Spark strukturált stream használata a Kafkával – Azure HDInsight '
description: Megtudhatja, hogyan használhatja az Apache Spark streamelést az adatok az Apache Kafkába való betöltéséhez, illetve az onnan való exportálásához. Ebben az oktatóanyagban egy Jupyter notebookkal streamelünk adatokat a Spark on HDInsightból.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: jasonh
ms.openlocfilehash: 47879350c56b9e8b943e1bff61359727746b697d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598305"
---
# <a name="tutorial-use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Oktatóanyag: A Spark strukturált stream használata a Kafkával a HDInsighton

Az oktatóanyag bemutatja, hogyan használhatja a Spark strukturált streamet adatok olvasásához és írásához az Apache Kafkával az Azure HDInsighton.

A Spark strukturált stream egy Spark SQL-alapú streamfeldolgozó rendszer. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Strukturált streamelés a Kafkával
> * Kafka- és Spark-fürtök létrehozása
> * A notebook feltöltése a Sparkba
> * A notebook használata
> * Az erőforrások eltávolítása

Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="prerequisites"></a>Előfeltételek

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információkért lásd [a Spark on HDInsight használatával végzett adatbetöltést és lekérdezésfuttatást ismertető](spark/apache-spark-load-data-run-query.md) dokumentumot.

* A [Scala](https://www.scala-lang.org/) programozási nyelv ismerete. Az oktatóanyagban használt kód Scala nyelven van megírva.

* A Kafka-témakörök létrehozásának ismerete. További információkért lásd [a Kafka on HDInsight használatába bevezető](kafka/apache-kafka-get-started.md) dokumentumot.

> [!IMPORTANT]
> A dokumentum lépéseihez egy olyan Azure-erőforráscsoport szükséges, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
> 
> A kényelmes használat érdekében ez a dokumentum tartalmaz egy hivatkozást egy olyan sablonra, amellyel az összes szükséges Azure-erőforrás létrehozható. 
>
> A HDInsight a virtuális hálózatokon való használatával kapcsolatos további információért tekintse meg [a HDInsight virtuális hálózattal való kiterjesztését ismertető](hdinsight-extend-hadoop-virtual-network.md) dokumentumot.

## <a name="structured-streaming-with-kafka"></a>Strukturált streamelés a Kafkával

A Spark strukturált stream egy, a Spark SQL-motorra épülő streamfeldolgozó rendszer. A strukturált stream használatával ugyanúgy írhat streamelési lekérdezéseket, mint a kötegelt lekérdezések esetében.

Az alábbi kódrészletek az adatok Kafkából való beolvasását és fájlban való tárolását mutatják be. Az első egy köteg-, míg a második egy streamelési művelet:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Mindkét kódrészlet a Kafkából olvassa be az adatokat, majd fájlba írja azokat. A két példa közötti különbségek:

| Batch | Streamelés |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

A streamelési művelet az `awaitTermination(30000)` utasítást is tartalmazza, amely 30000 ezredmásodperc elteltével leállítja a streamet. 

A strukturált streamelés a Kafkával való használatához a projektnek függőségi viszonyban kell lennie az `org.apache.spark : spark-sql-kafka-0-10_2.11` csomaggal. A csomag verziójának egyeznie kell a Spark on HDInsight verziójával. A Spark 2.2.0 (ez a HDInsight 3.6 verzióján érhető el) esetében a különböző projekttípusokra vonatkozó függőségek adatait a következő helyen találja: [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Az oktatóanyaghoz megadott Jupyter-notebookban a következő cella tölti be ezt a csomagfüggőséget:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Spark-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek. 

Az alábbi ábra a Spark és a Kafka közötti kommunikáció áramlását mutatja be.

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Azure-beli virtuális hálózat, majd az abban lévő Kafka- és Spark-fürtök létrehozásához hajtsa végre a következő lépéseket:

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Az Azure Resource Manager-sablon a következő helyen található: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Ez a sablon a következő erőforrásokat hozza létre:

    * Egy Kafka on HDInsight 3.6-fürt.
    * Egy Spark 2.2.0 on HDInsight 3.6-fürt.
    * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

    > [!IMPORTANT]
    > Az oktatóanyagban alkalmazott strukturált stream használatához a Spark 2.2.0 on HDInsight 3.6 szükséges. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

    | Beállítás | Érték |
    | --- | --- |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | Az erőforrásokat tartalmazó erőforráscsoport. |
    | Hely | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
    | Spark-fürt neve | A Spark-fürt neve. Az első hat karakternek a Kafka-fürt nevétől eltérőnek kell lennie. |
    | Kafka-fürt neve | A Kafka-fürt neve. Az első hat karakternek a Spark-fürt nevétől eltérőnek kell lennie. |
    | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
    | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
    | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |
   
    ![A testreszabott sablon képernyőképe](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. 

> [!NOTE]
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="upload-the-notebook"></a>A notebook feltöltése

Ha fel szeretné tölteni a notebookot a projektből a Spark on HDInsight-fürtre, használja a következő lépéseket:

1. Töltse le a projektet a következő helyről: [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

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

Az oktatóanyagból megtudta, hogyan használhatja a Spark strukturált streamet adatok írásához és olvasásához a Kafka on HDInsightból. A Storm a Kafkával való használatának megismeréséhez kövesse az alábbi hivatkozást.

> [!div class="nextstepaction"]
> [Az Apache Storm használata a Kafkával](hdinsight-apache-storm-with-kafka.md)
