---
title: "Apache Spark strukturált Streamelés Kafka az Azure Cosmos DB - az Azure HDInsight |} Microsoft Docs"
description: "Útmutató Apache Spark strukturált Streaming adatokat olvasni az Apache Kafka, és a tárolás Azure Cosmos DB. Ebben a példában a HDInsight Spark a Jupyter notebook használatával adatok folyamatos átviteléhez."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/16/2017
ms.author: larryfr
ms.openlocfilehash: 49a57431c778ee018ecd71e3921cd21c3be94885
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Válassza a Spark strukturált Kafka és Azure Cosmos DB Stream továbbítása

Útmutató segítségével Spark strukturált Streaming adatokat olvasni az Azure HDInsight Apache Kafka, és majd tárolja az adatokat az Azure Cosmos DB.

Azure Cosmos-adatbázis egy olyan globálisan elosztott, több modellre adatbázis. Ez a példa az SQL API adatbázis modellt használ. További információkért lásd: a [Üdvözli az Azure Cosmos DB](../cosmos-db/introduction.md) dokumentum.

Strukturált Spark streaming olyan adatfolyam feldolgozása a Spark SQL épül. Lehetővé teszi adatfolyam számítások express ugyanaz, mint a batch számítási statikus adatok. A strukturált Streaming további információkért lásd: a [strukturált Streaming programozási útmutató [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) az Apache.org webhelyen.

> [!IMPORTANT]
> Ebben a példában HDInsight 3.6 Spark 2.1 használni. Strukturált Streaming tekinthető __alpha__ Spark 2.1-es verziójának.
>
> A jelen dokumentumban leírt lépések, amely tartalmazza a Spark on HDInsight és egy HDInsight-fürt Kafka Azure erőforráscsoport-csoport létrehozása. Ezeken a fürtökön is egy Azure virtuális hálózatot, amely lehetővé teszi a Kafka közvetlenül kommunikálni a Spark-fürtön belül található a fürtön.
>
> Amikor elkészült, a jelen dokumentumban leírt lépések, ne felejtse el a felesleges költségek elkerülése érdekében a fürtök törlése.

## <a name="create-the-clusters"></a>A fürtök létrehozása

A HDInsight Apache Kafka nem férhet hozzá a Kafka brókerek a nyilvános interneten keresztül. A Kafka fürt csomópontja azonos Azure virtuális hálózaton, amelyeket a kiszolgálóhoz csatlakozik Kafka kell lennie. Ehhez a példához a Kafka és a Spark-fürtök egy Azure virtuális hálózat található. Az alábbi ábra bemutatja, hogyan kommunikációs a fürtök között zajló kommunikációról:

![Spark és Kafka fürtök egy Azure virtuális hálózatban ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> A virtuális hálózaton belüli kommunikáció a Kafka szolgáltatás korlátozódik. A fürtben, mint az SSH és az Ambari, más szolgáltatások az interneten keresztül is elérhető. A hdinsight eszközzel elérhető nyilvános portokon további információkért lásd: [portok és a HDInsight által használt URI-azonosítók](hdinsight-hadoop-port-settings-for-services.md).

Létrehozhat egy Azure virtuális hálózatra, Kafka, és a Spark-fürtök manuális, célszerűbb Azure Resource Manager sablonnal. Az alábbi lépések segítségével telepíthet egy Azure virtuális hálózatot, Kafka, és a Spark-fürtök az Azure-előfizetéshez.

1. A következő gomb segítségével jelentkezzen be az Azure-ba, és nyissa meg a sablon az Azure portálon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/> </a>

    A GitHub-tárházban, ebben a projektben található, az Azure Resource Manager-sablon ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Ez a sablon hoz létre a következő erőforrásokat:

    * Egy Kafka HDInsight 3.6-fürtön.

    * A Spark on HDInsight 3.6 fürtön.

    * Egy Azure virtuális hálózatot, amely a HDInsight-fürtöt tartalmaz.

    * Egy Azure Cosmos DB SQL API-adatbázis.

    > [!IMPORTANT]
    > A strukturált, ebben a példában használt adatfolyam-továbbítási notebook a Spark on HDInsight 3.6 igényel. Ha a HDInsight Spark egy korábbi verzióját használja, hibák merülnek fel a notebook használatakor.

2. A következő információk segítségével a feltöltik a **egyéni telepítési** szakasz:
   
    ![HDInsight egyéni központi telepítés](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürthöz.

    * **Hely**: Adjon meg egy földrajzilag Önhöz legközelebb eső helyet.

    * **Fürt neve kiinduló**: Ez az érték használható a Spark alap néven és Kafka fürtök. Ha például **hdi** hoz létre a Spark, spark-hdi__ nevű és egy Kafka fürtön nevű **kafka-hdi**.

    * **A fürt bejelentkezési felhasználónevét**: A rendszergazda felhasználóneve a Spark és Kafka fürt.

    * **A fürt bejelentkezési jelszó**: a Spark és Kafka fürt rendszergazdai jelszóval.

    * **SSH-felhasználónév**: az SSH-felhasználó a Spark- és Kafka fürtök létrehozása.

    * **SSH-jelszónak**: a Spark és Kafka fürt SSH-felhasználó jelszavát.

3. Olvassa el a **feltételek és kikötések**, majd válassza ki **elfogadom a feltételeket és a fenti feltételek**.

4. Végül ellenőrizze **rögzítés az irányítópulton** majd **beszerzési**. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Az erőforrások létrehozása után, egy összegző lap is megjelenik.

![A virtuális hálózat és a fürtök csoport erőforrásadatok](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Figyelje meg, hogy a HDInsight-fürtök neve **spark-BASENAME** és **kafka-BASENAME**, ahol BASENAME a sablonhoz megadott név. Ezeket a neveket a későbbi lépésekben használja, a fürtök történő csatlakozás során.

## <a name="get-the-kafka-brokers"></a>A Kafka brókerek beolvasása

Ebben a példában a kód Kafka broker a fürt állomásai között Kafka csatlakozik. A két Kafka broker állomások címét megkereséséhez használja a következő PowerShell- vagy Bash példa:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Amikor a rendszer kéri, adja meg a jelszót a fürt (rendszergazda) bejelentkezési fiók

> [!NOTE]
> Ez a példa vár `$CLUSTERNAME` magában foglalja a Kafka fürt nevét.
>
> Ez a példa a [jq](https://stedolan.github.io/jq/) segédprogram a JSON-dokumentum kívül az adatok.

A kimenet az alábbi szöveghez hasonló:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Ezt az információt akkor menteni, mert használatban van ebben a dokumentumban a következő szakaszok.

## <a name="get-the-notebooks"></a>A notebookok beolvasása

A jelen dokumentumban ismertetett példa kódja megtalálható [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Töltse fel a notebookok

Az alábbi lépések segítségével töltse fel a notebookok a projektből a Spark on HDInsight-fürt számára:

1. A böngészőben csatlakoztassa a Jupyter notebook a Spark-fürtön. Cserélje le a következő URL-címet, `CLUSTERNAME` nevű, a __Spark__ fürt:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazda) és a fürt létrehozásakor használt jelszót.

2. A lap felső jobb oldalán, használja a __feltöltése__ gombra kattintva töltse fel a __adatfolyam-taxi-adatok-a-kafka.ipynb__ fájl a fürthöz. Válassza ki __nyitott__ való feltöltés indításához.

3. Keresse a __adatfolyam-taxi-adatok-a-kafka.ipynb__ bejegyzés notebookok, és válassza ki a listában __feltöltése__ mellette gombra.

4. Ismételje meg a 1-3 betöltése a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ notebookot.

## <a name="load-tweets-into-kafka"></a>Twitter-üzeneteket betölthető Kafka

A fájlok feltöltése után válassza a __adatfolyam-taxi-adatok-a-kafka.ipynb__ nyissa meg a notebook bejegyzést. Az adatok betöltése az Kafka notebook használatához kövesse.

## <a name="process-tweets-using-spark-structured-streaming"></a>Folyamat Twitter-üzeneteket Spark strukturált adatfolyam használata

Jupyter Notebook kezdőlapján válassza ki a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ bejegyzés. Kövesse a notebook adatfolyam adatok Kafka és Azure Cosmos DB Spark strukturált Streaming használatával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a használata Spark strukturált Streaming rendelkezik, tekintse meg a következő dokumentumok tudhat meg többet a Spark, Kafka és Azure Cosmos DB használata:

* [Spark streaming (DStream) rendelkező Kafka használata](hdinsight-apache-spark-with-kafka.md).
* [Indítsa el a Jupyter Notebook és a Spark on HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Üdvözli az Azure Cosmos DB](../cosmos-db/introduction.md)