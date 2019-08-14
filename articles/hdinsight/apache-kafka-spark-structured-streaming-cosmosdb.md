---
title: Strukturált streaming Apache Spark Apache Kafkaról a Azure Cosmos DB-Azure HDInsight
description: Megtudhatja, hogyan használhatja Apache Spark strukturált adatfolyamot az adatok Apache Kafkaból való beolvasásához, majd Azure Cosmos DBba való tárolásához. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: c2d2f56f2a0686b23a3a907686dcee0760d37365
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947069"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Apache Spark strukturált adatfolyam használata Apache Kafka és Azure Cosmos DB

Megtudhatja, hogyan használhatja a [Apache Spark](https://spark.apache.org/) [strukturált adatfolyamot](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) az Azure HDInsight lévő [Apache Kafka](https://kafka.apache.org/) adatainak beolvasásához, majd az adatok Azure Cosmos DBba való tárolásához.

A [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) egy globálisan elosztott, többmodelles adatbázis. Ez a példa egy SQL API-adatbázis-modellt használ. További információkért tekintse meg az [üdvözli a Azure Cosmos db](../cosmos-db/introduction.md) dokumentumot.

A Spark strukturált stream egy Spark SQL-alapú streamfeldolgozó rendszer. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében. A strukturált Streamtel kapcsolatos további információkért tekintse meg a [strukturált streaming programozási útmutatót](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) a következő címen: Apache.org.

> [!IMPORTANT]  
> Ez a példa a Spark 2,2-et használta a HDInsight 3,6-on.
>
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. A Kafka-vel megbeszélt mindennek ugyanabban az Azure-beli virtuális hálózatban kell lennie, mint a Kafka-fürt csomópontjain. Ebben a példában a Kafka és a Spark-fürtök egy Azure-beli virtuális hálózaton találhatók. Az alábbi ábrán a fürtök közötti kommunikáció látható:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Habár az Azure Virtual Network, a Kafka és a Spark-fürtök manuálisan is létrehozhatók, könnyebben Azure Resource Manager sablont használni. Az alábbi lépéseket követve üzembe helyezhet egy Azure-beli virtuális hálózatot, Kafka-t és Spark-fürtöt az Azure-előfizetésében.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png" alt="Deploy to Azure"/>
    </a>

    A Azure Resource Manager sablon a projekthez tartozó GitHub-tárházban található ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Ez a sablon a következő erőforrásokat hozza létre:

   * Egy Kafka on HDInsight 3.6-fürt.

   * Egy Spark on HDInsight 3,6-fürt.

   * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

       > [!NOTE]  
       > A sablon által létrehozott virtuális hálózat a 10.0.0.0/16 címtartományt használja.

   * Egy Azure Cosmos DB SQL API-adatbázis.

     > [!IMPORTANT]  
     > Az ebben a példában használt strukturált stream a Spark on HDInsight 3.6-os verzióját igényli. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. Az alábbi információk segítségével töltheti fel a bejegyzéseket az **Egyéni telepítés** szakaszban:
   
    ![HDInsight egyéni üzembe helyezése](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Előfizetés**: Válassza ki az Azure-előfizetését.
   
    * **Erőforráscsoport**: Hozzon létre egy csoportot, vagy válasszon ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürtöt.

    * **Hely**: Válasszon ki egy földrajzilag közel lévő helyet.

    * **Cosmos db fiók neve**: Ezt az értéket használja a Cosmos DB fiók neve.

    * **Alap fürt neve**: A rendszer ezt az értéket használja a Spark-és Kafka-fürtök alapneveként. A **myhdi** megadása például egy __Spark-myhdi__ nevű Spark-fürtöt és egy **Kafka-myhdi**nevű Kafka-fürtöt hoz létre.

    * **Fürt verziója**: A HDInsight-fürt verziója.

        > [!IMPORTANT]  
        > Ezt a példát a 3,6-es HDInsight teszteli, és előfordulhat, hogy nem működik más típusú fürtökkel.

    * **Fürt bejelentkezési felhasználónevének neve**: A Spark és a Kafka fürtök rendszergazdai felhasználóneve.

    * **Fürt bejelentkezési jelszava**: A Spark-és Kafka-fürtök rendszergazdai felhasználói jelszava.

    * **SSH-Felhasználónév**: A Spark-és Kafka-fürtökhöz létrehozandó SSH-felhasználó.

    * **SSH-jelszó**: A Spark-és Kafka-fürtök SSH-felhasználójának jelszava.

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül válassza a **vásárlás**lehetőséget. A fürtök létrehozása körülbelül 20 percet vesz igénybe.

> [!IMPORTANT]  
> A fürtök, a virtuális hálózat és a Cosmos DB fiók létrehozása akár 45 percet is igénybe vehet.

## <a name="create-the-cosmos-db-database-and-collection"></a>A Cosmos DB adatbázis és gyűjtemény létrehozása

Az ebben a dokumentumban használt projekt Cosmos DB tárolja az adattárakat. A kód futtatása előtt először létre kell hoznia egy _adatbázist_ és egy gyűjteményt az Cosmos db-példányban. A dokumentum-végpontot és a Cosmos DBra irányuló kérelmek hitelesítéséhez használt _kulcsot_ is le kell kérni. 

Ennek egyik módja az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)használata. A következő szkript létrehoz egy nevű `kafkadata` adatbázist és egy nevű `kafkacollection`gyűjteményt. Ezután visszaadja az elsődleges kulcsot.

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
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

A dokumentum-végpont és az elsődleges kulcs adatai az alábbi szöveghez hasonlóak:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Mentse a végpontot és a kulcs értékeit, mivel azok a Jupyter-jegyzetfüzetekben szükségesek.

## <a name="get-the-apache-kafka-brokers"></a>A Apache Kafka-közvetítők beszerzése

Az ebben a példában szereplő kód a Kafka-fürtön található Kafka Broker-gazdagépekhez csatlakozik. A két Kafka-közvetítő gazdagép címeinek megkereséséhez használja a következő PowerShell-vagy bash-példát:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> A bash `$CLUSTERNAME` -példa arra vár, hogy tartalmazza a Kafka-fürt nevét.
>
> Ez a példa a [jQ](https://stedolan.github.io/jq/) segédprogramot használja a JSON-dokumentumból származó adatelemzéshez.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Ha a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) fiókjának jelszavát.

A kimenet az alábbi szöveghez hasonló:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Mentse ezt az információt, ahogy azt a jelen dokumentum következő részeiben használják.

## <a name="get-the-notebooks"></a>Jegyzetfüzetek letöltése

A dokumentumban leírt példa kódja a következő helyen található: [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>A notebookok feltöltése

A következő lépésekkel töltheti fel a jegyzetfüzeteket a projektből a Spark on HDInsight-fürtre:

1. A webböngészőben csatlakozzon a Spark-fürtön lévő Jupyter notebookhoz. A következő URL-címben cserélje le a `CLUSTERNAME` elemet a __Spark__-fürt nevére.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

2. A lap jobb felső részén a __feltöltés__ gombra kattintva töltse fel a __stream-taxi-adat-a-Kafka. ipynb__ fájlt a fürtre. A feltöltés elindításához válassza a __Megnyitás__ elemet.

3. Keresse meg a __stream-taxi-adat-to-Kafka. ipynb__ bejegyzést a jegyzetfüzetek listájában, és kattintson a mellette található __feltöltés__ gombra.

4. Ismételje meg az 1-3-es lépést az __adatfolyam-adatok-a-Kafka-to-Cosmos-db. ipynb__ jegyzetfüzet betöltéséhez.

## <a name="load-taxi-data-into-kafka"></a>Taxik betöltése a Kafkabe

A fájlok feltöltése után válassza ki a __stream-taxi-adat-to-Kafka. ipynb__ bejegyzést a jegyzetfüzet megnyitásához. Kövesse a jegyzetfüzetben lévő lépéseket, és töltse be az adatgyűjtést a Kafkabe.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Taxik feldolgozása a Spark strukturált streaming használatával

A [Jupyter notebook](https://jupyter.org/) kezdőlapon válassza ki a __stream-from-Kafka-to-Cosmos-db. ipynb__ bejegyzést. Kövesse a jegyzetfüzetben található lépéseket a Kafka-ből származó adatok továbbításához és a Azure Cosmos DB a Spark Structured streaming használatával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte Apache Spark strukturált adatfolyam használatát, tekintse meg a következő dokumentumokat, ahol további információt talál a Apache Spark, a Apache Kafka és a Azure Cosmos DB használatáról:

* A [Apache Spark streaming (DStream) használata Apache Kafka használatával](hdinsight-apache-spark-with-kafka.md).
* [Kezdés Jupyter Notebook és Apache Spark a HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Üdvözli a Azure Cosmos DB](../cosmos-db/introduction.md)
