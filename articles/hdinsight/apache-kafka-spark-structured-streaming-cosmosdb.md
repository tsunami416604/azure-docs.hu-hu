---
title: Az Apache Spark & Az Apache Kafka és a Cosmos DB – Azure HDInsight
description: Ismerje meg, hogyan használhatja az Apache Spark strukturált streamelési segítségével az Apache Kafka adatait, és hogyan tárolhatja azokat az Azure Cosmos DB-ben. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406175"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Az Apache Spark strukturált streamelésének használata az Apache Kafkával és az Azure Cosmos DB-vel

Ismerje meg, hogyan használhatja az [Apache Spark](https://spark.apache.org/) [strukturált streamelésével](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) az [Apache Kafka](https://kafka.apache.org/) adatait az Azure HDInsightszolgáltatáson, majd hogyan tárolhatja az adatokat az Azure Cosmos DB-ben.

[Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, többmodelles adatbázis. Ez a példa egy SQL API-adatbázismodellt használ. További információkért tekintse meg az Üdvözli az [Azure Cosmos](../cosmos-db/introduction.md) DB-dokumentumot.

A Spark strukturált stream egy Spark SQL-alapú streamfeldolgozó rendszer. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében. A strukturált streamelésről további információt a [strukturált streamelési programozási útmutató](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) Apache.org.

> [!IMPORTANT]  
> Ez a példa a Spark 2.2-t használta a HDInsight 3.6-on.
>
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka a HDInsight-on nem biztosít hozzáférést a Kafka brókerekhez a nyilvános interneten keresztül. Bármi, ami beszél a Kafka kell lennie ugyanabban az Azure virtuális hálózat, mint a csomópontok a Kafka-fürtben. Ebben a példában a Kafka és a Spark-fürtök egy Azure virtuális hálózatban találhatók. Az alábbi ábra bemutatja, hogyan folyik a kommunikáció a fürtök között:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Bár manuálisan hozhat létre egy Azure-beli virtuális hálózatot, a Kafka- és a Spark-fürtöket, egyszerűbb en azure Resource Manager-sablont használni. Az alábbi lépésekkel üzembe helyezhet egy Azure-beli virtuális hálózatot, a Kafka- és Spark-fürtöket az Azure-előfizetésében.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Az Azure Resource Manager sablon a projekt GitHub-tárházában ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)található.

    Ez a sablon a következő erőforrásokat hozza létre:

   * Egy Kafka on HDInsight 3.6-fürt.

   * Spark a HDInsight 3.6-os fürtön.

   * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket. A sablon által létrehozott virtuális hálózat a 10.0.0.0/16 címteret használja.

   * Egy Azure Cosmos DB SQL API-adatbázis.

    > [!IMPORTANT]  
    > Az ebben a példában használt strukturált stream a Spark on HDInsight 3.6-os verzióját igényli. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

1. Az **egyéni telepítés** szakasz bejegyzéseinek feltöltéséhez használja az alábbi információkat:

    |Tulajdonság |Érték |
    |---|---|
    |Előfizetés|Válassza ki az Azure-előfizetését.|
    |Erőforráscsoport|Hozzon létre egy csoportot, vagy jelöljön ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürtöt.|
    |Cosmos DB-fiók neve|Ez az érték a Cosmos DB-fiók neve. A név csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. 3-31 karakter közötti hosszúságúnak kell lennie.|
    |Alapfürt neve|Ez az érték a Spark és a Kafka fürtök alapneve. Ha például a **myhdi** beírja a __spark-myhdi__ nevű Spark-fürtöt és egy **kafka-myhdi**nevű Kafka-fürtöt.|
    |Fürt verziója|A HDInsight fürtverziója. Ez a példa a HDInsight 3.6-os tesztelt, és nem működik más fürttípusokkal.|
    |Fürt bejelentkezési felhasználóneve|A Spark és a Kafka fürtök rendszergazdai felhasználóneve.|
    |Fürt bejelentkezési jelszava|A Spark és a Kafka fürtök rendszergazdai felhasználói jelszava.|
    |Ssh felhasználónév|A Spark és a Kafka fürtök létrehozásához az SSH-felhasználó.|
    |Ssh jelszó|A Spark és a Kafka fürtök SSH-felhasználójának jelszava.|

    ![A HDInsight egyéni központi telepítési értékei](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

1. Végül válassza a **Vásárlás**lehetőséget. A fürtök, a virtuális hálózat és a Cosmos DB-fiók létrehozása akár 45 percet is igénybe vehet.

## <a name="create-the-cosmos-db-database-and-collection"></a>A Cosmos DB adatbázis és -gyűjtemény létrehozása

A dokumentumban használt projekt a Cosmos DB-ben tárolja az adatokat. A kód futtatása előtt először létre kell hoznia egy _adatbázist_ és _gyűjteményt_ a Cosmos DB-példányban. A dokumentumvégpontot és a Cosmos DB-nek érkező kérelmek hitelesítéséhez használt _kulcsot_ is be kell olvasnia.

Ennek egyik módja az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használata. A következő parancsfájl létrehoz `kafkadata` egy nevű `kafkacollection`adatbázist és egy gyűjteményt. Ezután visszaadja az elsődleges kulcsot.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

A dokumentumvégpont és az elsődleges kulcsadatok a következő szöveghez hasonlóak:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Mentse a végpontot és a kulcsértékeket, ahogy azok a Jupyter notebookokban szükségesek.

## <a name="get-the-notebooks"></a>A jegyzetfüzetek beszerezése

A dokumentumban ismertetett példa kódja [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)a következő helyen érhető el: .

## <a name="upload-the-notebooks"></a>A notebookok feltöltése

Az alábbi lépésekkel töltheti fel a jegyzetfüzeteket a projektből a Spark HDInsight-fürtre:

1. A webböngészőben csatlakozzon a Spark-fürtön lévő Jupyter notebookhoz. A következő URL-címben cserélje le a `CLUSTERNAME` elemet a __Spark__-fürt nevére.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

2. A lap jobb felső részén a __Feltöltés__ gombbal feltöltheti a __Stream-taxi-data-to-kafka.ipynb__ fájlt a fürtbe. A feltöltés elindításához válassza a __Megnyitás__ elemet.

3. Keresse meg a __Stream-taxi-data-to-kafka.ipynb__ bejegyzést a jegyzetfüzetek listájában, és válassza a __Feltöltés__ gombot mellette.

4. Ismételje meg __az__ 1-3.

## <a name="load-taxi-data-into-kafka"></a>Taxi adatok betöltése a Kafkába

A fájlok feltöltése után válassza ki a __Stream-taxi-data-to-kafka.ipynb__ bejegyzést a jegyzetfüzet megnyitásához. Kövesse a notebook lépéseit az adatok Kafkába való betöltéséhez.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Taxiadatok feldolgozása a Spark strukturált adatfolyamhasználatával

A [Jupyter Notebook](https://jupyter.org/) kezdőlapján válassza ki a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ bejegyzést. Kövesse a notebook lépéseit a Kafka és az Azure Cosmos DB-ből a Spark strukturált streamelése használatával történő adatfolyam-továbbításhoz.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az Apache Spark strukturált streamelésének használatát, tekintse meg az alábbi dokumentumokat, amelyek ből többet szeretne tudni az Apache Spark, az Apache Kafka és az Azure Cosmos DB használatáról:

* [Az Apache Spark streaming (DStream) használata az Apache Kafka segítségével.](hdinsight-apache-spark-with-kafka.md)
* [Kezdje a Jupyter notebookés az Apache Spark alkalmazással a HDInsighton](spark/apache-spark-jupyter-spark-sql.md)
* [Üdvözli az Azure Cosmos DB](../cosmos-db/introduction.md)
