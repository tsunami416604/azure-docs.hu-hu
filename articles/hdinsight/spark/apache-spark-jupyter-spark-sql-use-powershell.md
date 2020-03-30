---
title: 'Rövid útmutató: Apache Spark-fürt létrehozása az Azure PowerShell használatával az Azure HDInsightban'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure PowerShell egy Azure Spark-fürt létrehozásához az Azure HDInsightban, illetve egy egyszerű Spark SQL-lekérdezés futtatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 7416c25128da8dcaf803a9f03144110941200ab2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049049"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsightban a PowerShell használatával

Ebben a rövid útmutatóban az Azure PowerShell segítségével hozzon létre egy Apache Spark-fürtöt az Azure HDInsightban. Ezután hozzon létre egy Jupyter-jegyzetfüzetet, és ezzel futtathatja a Spark SQL-lekérdezéseket az Apache Hive-táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Spark Azure HDInsight-keretrendszere gyors adatelemzést és fürtös számítástechnikát tesz lehetővé a memórián belüli feldolgozással. A Jupyter notebook lehetővé teszi az adatok kalkulálásával, a kód és a markdown szöveg kombinálásával és az egyszerű vizualizációk kombinálásával.

[Áttekintés: Apache Spark az Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebookján](https://jupyter.org/)

## <a name="prerequisite"></a>Előfeltétel

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- A [PowerShell Az modul](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Apache Spark-fürt létrehozása a HDInsightban

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

A HDInsight-fürtök létrehozása a következő Azure-objektumok és erőforrások létrehozását tartalmazza:

- Azure-erőforráscsoport. Az Azure-erőforráscsoport az Azure-erőforrások tárolója.
- Egy Azure storage-fiók vagy az Azure Data Lake Storage.  Minden egyes HDInsight-fürt egy függő adattárat igényel. Ebben a rövid útmutatóban hozzon létre egy fürtöt, amely az Azure Storage Blobs fürttárolóként használja. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.
- Különböző fürttípusokból álló fürt a HDInsighton.  Ebben a rövid útmutatóban egy Spark 2.3-fürtöt hoz létre.

Egy PowerShell-szkript használatával hozhatja létre az erőforrásokat. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A PowerShell-parancsfájl futtatásakor a rendszer a következő értékek megadását kéri:

|Paraméter|Érték|
|------|------|
|Azure-erőforráscsoport neve | Adjon meg egy egyedi nevet az erőforráscsoportnak.|
|Hely| Adja meg az Azure-régiót, például: USA középső régiója. |
|Alapértelmezett tárfióknév | Adjon meg egy egyedi nevet a tárfióknak. |
|Fürt neve | Adjon meg egy egyedi nevet a HDInsight-fürtnek.|
|Fürt hitelesítő adatai | Ezt a fiókot a fürt irányítópultjához való csatlakozásra fogja használni a rövid útmutató későbbi részében.|
|SSH-felhasználó hitelesítő adatai | Az SSH-ügyfelek segítségével távoli parancssori munkamenetet hozhat létre a HDInsight fürtjeivel.|

1. Válassza a **Try It** lehetőséget a jobb felső sarokban a következő kódblokk hoz az Azure [Cloud Shell](../../cloud-shell/overview.md)megnyitásához, és kövesse az utasításokat az Azure-hoz való csatlakozáshoz.

2. Másolja és illessze be a következő PowerShell-parancsfájlt a Cloud Shellbe.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha a HDInsight-fürtök létrehozásával kapcsolatos probléma lép fel, előfordulhat, hogy ehhez nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

[A Jupyter Notebook](https://jupyter.org/) egy interaktív jegyzetfüzet-környezet, amely támogatja a különböző programozási nyelveket. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a **HDInsight-fürtöket.**
   
   ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png)
   
1. A listából jelölje ki a létrehozott fürtöt.
   
   ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png)
   
1. A fürt **áttekintése** lapon válassza a **Fürtirányítópultok**lehetőséget, majd a **Jupyter-jegyzetfüzet**lehetőséget. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![A Jupyter-jegyzetfüzet megnyitása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "A Jupyter-jegyzetfüzet megnyitása interaktív Spark SQL-lekérdezés futtatásához")

1. Válassza **az Új** > **PySpark** lehetőséget a jegyzetfüzet létrehozásához.

   ![Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![kernel állapota](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "kernel állapota")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön. 
1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha egy Jupyter-jegyzetfüzetet használ a Spark-fürtjével `sqlContext` a HDInsightban, egy készletet kap, amely a Spark SQL használatával futtathatja a Hive-lekérdezéseket. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Apache Hive-lekérdezés a Sparkban a HDInsighton](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png "Hive-lekérdezés a HDInsight Sparkban")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Hive-lekérdezés kimenete a HDInsightban](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png "Hive-lekérdezés kimenete a HDInsightban")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az Azure Storage vagy az Azure Data Lake Storage tárhelyein tárolja az adatokat, így a nem használt fürtök biztonságosan törölhetők. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![Az Azure Portal hdinsight-fürt törlése](./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Darabos tisztítás a PowerShell Az modullal

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Apache Spark-fürtöt a HDInsightban, és hogyan futtathat egy egyszerű Spark SQL-lekérdezést. A következő oktatóanyagra lépve megtudhatja, hogyan használhatja a HDInsight-fürtöt a mintaadatokon futó interaktív lekérdezések futtatásához.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása az Apache Sparkon](./apache-spark-load-data-run-query.md)
