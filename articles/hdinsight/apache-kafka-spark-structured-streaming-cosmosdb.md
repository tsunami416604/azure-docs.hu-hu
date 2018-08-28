---
title: Az Apache Spark strukturált Streamelés a Kafkából az Azure Cosmos DB használatával – Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Spark strukturált Stream adatokat olvasni az Apache Kafka és az Azure Cosmos DB-be tárolja majd. Ebben a példában Jupyter notebookkal streamel adatokat a Spark on HDInsightból.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: jasonh
ms.openlocfilehash: c18234e50711b2496b793263ca8d314f16347cbe
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107795"
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Használja a Spark strukturált Stream Kafka és az Azure Cosmos DB-vel

Ismerje meg, hogyan használható a Spark strukturált Stream adatokat olvasni az Azure HDInsight alapú Apache Kafka, és ezután tárolja az adatokat az Azure Cosmos DB-be.

Az Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis. Ebben a példában egy SQL API adatbázis modellt használja. További információkért lásd: a [Üdvözli az Azure Cosmos DB](../cosmos-db/introduction.md) dokumentumot.

A Spark strukturált stream egy Spark SQL-alapú streamfeldolgozó rendszer. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében. A strukturált streamelésről további információt az Apache.org oldalon lévő [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) (Strukturált streamelés programozási útmutatója [Alfa]) szakaszban talál.

> [!IMPORTANT]
> Ebben a példában a HDInsight 3.6-os Spark 2.2 használja.
>
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. Semmit, a Kafka-szel kommunikáló, a Kafka-fürt csomópontjainak azonos Azure virtuális hálózatban kell lennie. Ebben a példában a Kafka és a Spark-fürtök Azure virtuális hálózatban találhatók. Az alábbi ábrán látható, hogy a fürtök közötti kommunikáció áramlását:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Bár létrehozhat egy Azure virtuális hálózatra, a Kafka, és a Spark-fürtök manuális, egyszerűbb legyen a használata az Azure Resource Manager-sablon. Az alábbi lépések segítségével üzembe helyezése az Azure virtuális hálózat, a Kafka és Spark-fürtök az Azure-előfizetéshez.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Az Azure Resource Manager-sablon a projekthez tartozó GitHub-adattárban található ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Ez a sablon a következő erőforrásokat hozza létre:

    * Egy Kafka on HDInsight 3.6-fürt.

    * A Spark on HDInsight 3.6-fürt.

    * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

        > [!NOTE]
        > A sablon által létrehozott virtuális hálózat a 10.0.0.0/16 címteret használja.

    * Egy Azure Cosmos DB SQL API-adatbázis.

    > [!IMPORTANT]
    > Az ebben a példában használt strukturált stream a Spark on HDInsight 3.6-os verzióját igényli. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. Az alábbi információk segítségével feltöltik a a **egyéni üzembe helyezés** szakaszban:
   
    ![HDInsight-egyéni üzembe helyezés](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Előfizetés**: Válassza ki az Azure-előfizetést.
   
    * **Erőforráscsoport**: hozzon létre egy csoportot, vagy válasszon ki egy meglévőt. Ez a csoport tartalmazza a HDInsight-fürt.

    * **Hely**: válasszon, földrajzilag közeli helyet.

    * **Cosmos DB-fiók nevét**: ezt az értéket használja, a Cosmos DB-fiók nevét.

    * **Fürt neve alapján**: ezt az értéket használja, a Spark alapnevét, és a Kafka-fürtök. Ha például **myhdi** nevű egy Spark-fürtöt hoz létre __spark-myhdi__ és a egy nevű Kafka-fürt **kafka-myhdi**.

    * **Fürt verziója**: A HDInsight-fürt verziója.

        > [!IMPORTANT]
        > Ebben a példában a HDInsight 3.6-os szolgáltatás tesztelése, és egyéb fürttípusok esetleg nem fog.

    * **A fürt bejelentkezési név**: a Spark és Kafka-fürtök rendszergazdai felhasználóneve.

    * **A fürt bejelentkezési jelszavának**: a Spark és Kafka-fürtök rendszergazdai felhasználójának jelszava.

    * **SSH-felhasználónév**: az SSH-felhasználó, a Spark és Kafka-fürtök létrehozása.

    * **SSH-jelszó**: a Spark és Kafka-fürtök az SSH-felhasználó jelszavát.

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. Körülbelül 20 perc alatt létrehozni a fürtöket vesz igénybe.

> [!IMPORTANT]
> A fürtök, a virtuális hálózat és a Cosmos DB-fiók létrehozása akár 45 percig is eltarthat.

## <a name="create-the-cosmos-db-database-and-collection"></a>A Cosmos DB-adatbázis és gyűjtemény létrehozása

Az itt bemutatott projekt Cosmos DB tárolja az adatokat. A kód futtatása előtt létre kell hoznia egy _adatbázis_ és _gyűjtemény_ Cosmos DB-példányában. Is le kell kérnie a dokumentum végpont és a _kulcs_ Cosmos DB-hez kérelmek hitelesítéséhez. 

Ehhez a egyirányú azt használja a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). A következő szkript létrehoz egy adatbázist `kafkadata` és a egy nevű gyűjtemény `kafkacollection`. Ezután az elsődleges kulcsot adja vissza.

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

A dokumentum végpont és az elsődleges kulcsra az alábbi szöveghez hasonlít:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> A végpont és a kulcs értékeit, mentse a Jupyter notebookok a szükség van rájuk.

## <a name="get-the-kafka-brokers"></a>A Kafka-közvetítők beolvasása

Ebben a példában a kódot a Kafka-fürt Kafka-közvetítő gazdagépeire csatlakozik. A két Kafka-közvetítő gazdagép címét, használja a következő PowerShell- vagy Bash-példa:

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
> A Bash példa vár `$CLUSTERNAME` tartalmazza a Kafka-fürt nevére.
>
> Ez a példa a [jq](https://stedolan.github.io/jq/) elemezni az adatokat a JSON-dokumentum segédprogramot.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Amikor a rendszer kéri, adja meg a jelszót a fürt bejelentkezési (rendszergazdai) fiókjának

A kimenet az alábbi szöveghez hasonló:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Mentse ezt az információt, az alábbi szakaszok a dokumentum szolgál.

## <a name="get-the-notebooks"></a>A notebookok beolvasása

A dokumentumban leírt példa kódja a következő helyen található: [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>A notebookok feltöltése

Az alábbi lépések segítségével a Spark on HDInsight-fürt a projektből a notebookok feltöltése:

1. A webböngészőben csatlakozzon a Spark-fürtön lévő Jupyter notebookhoz. A következő URL-címben cserélje le a `CLUSTERNAME` elemet a __Spark__-fürt nevére.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

2. A jobb felső sarkában az oldal, használja a __feltöltése__ gombra kattintva töltse fel a __Stream-i taxik-data-az-kafka.ipynb__ fájlt a fürtbe. A feltöltés elindításához válassza a __Megnyitás__ elemet.

3. Keresse meg a __Stream-i taxik-data-az-kafka.ipynb__ bejegyzést a notebookok, és válassza ki a listában __feltöltése__ gombot.

4. Ismételje meg a 1-3 betölteni a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ notebookot.

## <a name="load-taxi-data-into-kafka"></a>A Kafka, taxi-adatok betöltése

A fájlok feltöltése után válassza ki a __Stream-i taxik-data-az-kafka.ipynb__ bejegyzést a notebook megnyitásához. Kövesse a jegyzetfüzet adatokat tölthet be a Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Folyamat i taxik adatainak használata a Spark strukturált Stream

A Jupyter Notebook kezdőlapján válassza ki a __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ bejegyzés. Kövesse a Notebookban lévő adatok kafka és Spark strukturált Stream használata Azure Cosmos DB-be.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Spark strukturált Stream használata, tekintse meg a következő dokumentumok tudhat meg többet a Spark, Kafka és Azure Cosmos DB használata:

* [Spark streamelés (DStream) használata a Kafkával](hdinsight-apache-spark-with-kafka.md).
* [A Jupyter Notebook és a Spark on HDInsight használatának első lépései](spark/apache-spark-jupyter-spark-sql.md)
* [Üdvözli az Azure Cosmos DB](../cosmos-db/introduction.md)
