---
title: "Használja az Apache Spark on strukturált az Event Hubs az Azure HDInsight streaming |} Microsoft Docs"
description: "Build Apache Spark streaming minta adatfolyam küldeni az Azure Event Hubs és a HDInsight Spark-fürt scala-alkalmazások segítségével a ezeket az eseményeket fogadni."
keywords: "Apache spark streaming, spark streamelési, spark minta, apache spark streamelési, például az esemény hub azure-minta, spark-minta"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: f302b84685b1992faef4813c0262223bcb5909aa
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Apache Spark strukturált adatfolyam-továbbításhoz Eseményközpontokból származó események feldolgozásához HDInsight

Ebből a cikkből megismerheti Spark strukturált Streaming használatával valós idejű telemetriai feldolgozni. Ehhez hajtsa végre az alábbi általános lépésekből áll:

1. Fordítsa le, és a helyi munkaállomáson futtassa esemény készítő mintaalkalmazás, amely küldendő Event Hubs eseményeket hoz létre.
2. Használja a [Spark rendszerhéj](apache-spark-shell.md) határozza meg, és egy egyszerű Spark strukturált adatfolyam-alkalmazást futtat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

* Az Azure Event Hubs névtér. Lásd: [hozzon létre egy Azure Event Hubs névtér](apache-spark-eventhub-streaming.md#create-an-azure-event-hub) további információt.

* Oracle Java fejlesztői készlet. A későbbiekben telepítheti az [Itt](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Letöltheti a [Itt](https://maven.apache.org/download.cgi). Útmutatás Maven telepítendő érhető el [Itt](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Build, konfigurálásához és futtatásához az esemény-készítő
Ebben a feladatban klón mintaalkalmazás, amely véletlenszerű események létrehozása és elküldése a konfigurált eseményközpontba. A mintaalkalmazás érhető el a Githubon: [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Győződjön meg arról, hogy a git eszközök vannak telepítve. Letölthető [GIT letölti](http://www.git-scm.com/downloads). 
2. Nyisson meg egy parancssort vagy a Terminálszolgáltatások rendszerhéj, és futtassa a következő parancsot a könyvtárból az Ön által választott, a projekt klónozását.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Ezzel létrehoz egy eventhubs-ügyfél-minta nevű új mappát. A rendszerhéj vagy a parancssorból Ugrás ebbe a mappába.
4. Futtassa a Maven hozható létre az alkalmazás a következő paranccsal:

          mvn package

5. A rendszerhéj vagy a parancssort, keresse meg a célkönyvtárat, amely jön létre, és tartalmazza a fájl ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. Ezután meg kell adnia a parancssor futtatásához az esemény gyártó az Eseményközpont. Ezt úgy teheti meg az értékeket a parancs a következőképpen cserélje le:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. A teljes parancs például az alábbihoz hasonló lenne:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. A parancs indítása, és ha a konfiguráció helyes, néhány perc múlva megjelenik-e az eseményeket az eseményközpontjába, az alábbihoz hasonló küld kapcsolódó kimeneti:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Hagyja meg az esemény készítő, miközben továbbra is be a lépéseket.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Indítsa el a Spark on HDInsight-fürt
Ebben a feladatban fogja SSH a HDInsight-fürt átjárócsomópontjához, indítsa el a Spark rendszerhéjat, és futtatni egy Spark Streaming alkalmazás, amely lekérdezi és feldolgozza az Event Hubs eseményeit. 

A pont a HDInsight-fürt készen áll. Ha nem, akkor várjon, amíg az befejeződik, kiépítés kell. Ha készen áll, folytassa a következő lépéseket:

1. Csatlakozzon a HDInsight-fürthöz SSH használatával. Útmutatásért lásd: [HDInsight SSH használatával csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. Az alkalmazás létrehozása a Spark Streaming Event Hubs csomag szükséges. Indítsa el a Spark, hogy automatikusan kéri le a függőségei közül a [Maven központi](https://search.maven.org), lehet, hogy a csomagokat az alábbiak szerint cserélje a Maven-koordináták ellátására:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. A Spark rendszerhéj befejeződése után betöltése, kell megjelennie:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. A következő kódrészletet átmásolja egy szövegszerkesztőben, és módosítsa azt, akkor a házirend kulccsal rendelkezik, ezért a Namespace állítja be az Eseményközpont megfelelő.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. A módosított részlet illessze be a várakozási scala > promptjába, és nyomja le az ENTER visszatérési. A következőhöz hasonló kimenetnek kell megjelennie:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. A Tovább, megkezdődik a hozhatnak létre a Spark strukturált adatfolyam-lekérdezést a forrás kell megadni. Illessze be a következő Spark rendszerhéjat, és nyomja le az ENTER visszatérési.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. A következőhöz hasonló kimenetnek kell megjelennie:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. A lekérdezés a következő szerzői, hogy a konzol kimenetét ír. Ehhez a következő beillesztése Spark rendszerhéj, majd nyomja le az visszatérési.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. Megtekintheti az egyes kötegekben, indítsa el a kimenet az alábbihoz hasonló

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. Ez a kimeneti eredmények minden microbatch események feldolgozása követi. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. Új események az esemény termelő érkezésekor, akkor dolgozza fel a strukturált adatfolyam-lekérdezés.
15. Győződjön meg arról, a HDInsight-fürt törlése, ha elkészült, ez a minta futtatása.



## <a name="see-also"></a>Lásd még

* [Spark streaming áttekintése](apache-spark-streaming-overview.md)













