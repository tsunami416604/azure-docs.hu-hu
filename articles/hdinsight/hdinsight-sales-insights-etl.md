---
title: 'Oktatóanyag: Hozzon létre egy teljes körű ETL-folyamatot az Azure HDInsight értékesítési elemzési adatainak levezetnie'
description: Megtudhatja, hogyan hozhat létre ETL-folyamatokat az Azure HDInsightsegítségével, hogy a Spark igény szerinti fürtjei és a Power BI használatával betekintést nyerjen az értékesítési adatokból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247265"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Oktatóanyag: Hozzon létre egy teljes körű adatfolyamatot az Azure HDInsight értékesítési elemzési adatainak levezetnie

Ebben az oktatóanyagban egy végpontok között futó adatfolyamatot hozhat létre, amely kibontási, átalakítási és betöltési (ETL) műveleteket hajt végre. A folyamat az Azure HDInsighton futó [Apache Spark](./spark/apache-spark-overview.md) és Apache Hive-fürtöket fogja használni az adatok lekérdezéséhez és kezeléséhez. Olyan technológiákat is használhat, mint az Azure Data Lake Storage Gen2 az adattároláshoz, és a Power BI a vizualizációhoz.

Ez az adatfolyamat egyesíti a különböző tárolókból származó adatokat, eltávolítja a nem kívánt adatokat, új adatokat fűz hozzá, és mindezt visszatölti a tárolóba az üzleti elemzések megjelenítéséhez. Tudjon meg többet az ETL-folyamatokról [az Extract, transform és load (ETL) nagy méretekben.](./hadoop/apache-hadoop-etl-at-scale.md)

![ETL architektúra](./media/hdinsight-sales-insights-etl/architecture.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI. Lásd: [Az Azure CLI telepítése.](https://docs.microsoft.com/cli/azure/install-azure-cli)

* Az Azure [beépített szerepkörének](../role-based-access-control/built-in-roles.md)egyik tagja – tulajdonos.

* [A Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) az oktatóanyag végén megjeleníti az üzleti elemzéseket.

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>A tárház klónozása parancsfájlokkal és adatokkal

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az Azure Cloud Shellt a felső menüsorból. Válassza ki a fájlmegosztás létrehozására vonatkozó előfizetését, ha a Cloud Shell kéri.

   ![Az Azure Cloud Shell megnyitása](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. A **Környezet kiválasztása** legördülő menüben válassza a **Bash parancsot.**

1. Győződjön meg arról, hogy tagja az Azure-szerepkör [tulajdonosának.](../role-based-access-control/built-in-roles.md) Cserélje `user@contoso.com` le a fiókját, majd írja be a parancsot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Ha nem ad vissza rekordot, akkor nem tagja, és nem tudja befejezni ezt az oktatóanyagot.

1. Sorolja fel a parancsot bebelépő előfizetéseket:

    ```azurecli
    az account list --output table
    ```

    Jegyezze fel a projekthez használni kívánt előfizetés azonosítóját.

1. Állítsa be a projekthez használni kívánt előfizetést. Cserélje `SUBSCRIPTIONID` le a tényleges értéket, majd írja be a parancsot.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Új erőforráscsoport létrehozása a projekthez. Cserélje `RESOURCEGROUP` le a kívánt nevet, majd írja be a parancsot.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Töltse le az oktatóanyag adatait és parancsfájljait a [HDInsight sales insights ETL tárházból.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)  Írja be a következő parancsot:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Győződjön meg róla, `salesdata scripts templates` hogy létrejöttek. Ellenőrizze a következő paranccsal:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>A folyamathoz szükséges Azure-erőforrások üzembe helyezése

1. Végrehajtási engedélyek hozzáadása az összes parancsfájlhoz a következő beírással:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Futtassa a szkriptet. Cserélje `RESOURCE_GROUP_NAME` `LOCATION` ki és a megfelelő értékeket, majd adja meg a parancsot:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    A parancs a következő erőforrásokat telepíti:

    * Egy Azure Blob-tárfiók. Ez a számla a vállalati értékesítési adatokat fogja tárolni.
    * Egy Azure Data Lake Storage Gen2-fiók. Ez a fiók mindkét HDInsight-fürt tárfiókjaként szolgál. Tudjon meg többet a HDInsight és a Data Lake Storage Gen2 az [Azure HDInsight integrációs Data Lake Storage Gen2.](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
    * Felhasználó által hozzárendelt felügyelt identitás. Ez a fiók hozzáférést biztosít a HDInsight-fürtöknek a Data Lake Storage Gen2 fiókhoz.
    * Egy Apache Spark-fürt. Ez a fürt a nyers adatok karbantartására és átalakítására szolgál.
    * Egy Apache Hive [interaktív lekérdezési](./interactive-query/apache-interactive-query-get-started.md) fürt. Ez a fürt lehetővé teszi az értékesítési adatok lekérdezését és megjelenítését a Power BI-val.
    * A hálózati biztonsági csoport (NSG) szabályai által támogatott Azure virtuális hálózat. Ez a virtuális hálózat lehetővé teszi a fürtök számára a kommunikációt és a kommunikáció biztonságát.

A fürt létrehozása körülbelül 20 percet is igénybe vehet.

A `resources.sh` parancsfájl a következő parancsokat tartalmazza. Nem szükséges futtatni ezeket a parancsokat, ha már végrehajtotta a parancsfájlt az előző lépésben.

* `az group deployment create`- Ez a parancs egy`resourcestemplate.json`Azure Resource Manager sablont ( ) használ a megadott erőforrások létrehozásához a kívánt konfigurációval.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch`- Ez a parancs feltölti az értékesítési adatok .csv fájlokat az újonnan létrehozott Blob storage fiók ezzel a paranccsal:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

A fürtök SSH-hozzáférésének alapértelmezett `Thisisapassword1`jelszava a . Ha módosítani szeretné a jelszót, `resourcesparameters.json` nyissa meg a fájlt, `llapClusterLoginPassword`és `llapsshPassword` módosítsa a `sparksshPassword`, `sparkClusterLoginPassword`, és a paraméterek jelszavát.

### <a name="verify-deployment-and-collect-resource-information"></a>A telepítés ellenőrzése és az erőforrásadatok összegyűjtése

1. Ha szeretné ellenőrizni a központi telepítés állapotát, nyissa meg az erőforráscsoportot az Azure Portalon. Válassza **a Központi telepítések lehetőséget a** Beállítások **területen.** Válassza ki a központi `ResourcesDeployment`telepítés nevét, . Itt láthatja a sikeresen üzembe helyezett erőforrásokat és a még folyamatban lévő erőforrásokat.

1. A fürtök nevének megtekintéséhez írja be a következő parancsot:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Az Azure storage-fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. A Data Lake Storage Gen2 fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Data factory létrehozása

Az Azure Data Factory egy olyan eszköz, amely segít automatizálni az Azure-folyamatokat. Nem ez az egyetlen módja ezeknek a feladatoknak, de ez egy nagyszerű módja annak, hogy automatizálják a folyamatokat. Az Azure Data Factory ról az [Azure Data Factory dokumentációjában](https://azure.microsoft.com/services/data-factory/)olvashat bővebben.

Ez az adatgyár egy folyamattal rendelkezik, amely két tevékenységet kínál:

* Az első tevékenység másolja az adatokat az Azure Blob storage a Data Lake Storage Gen 2 tárfiók utánozni az adatok betöltését.
* A második tevékenység átalakítja az adatokat a Spark-fürtben. A parancsfájl a nem kívánt oszlopok eltávolításával alakítja át az adatokat. Hozzáfűz egy új oszlopot is, amely kiszámítja az egyetlen tranzakció által generált bevételt.

Az Azure Data Factory-folyamat beállításához hajtsa végre a következő parancsot:

```bash
./scripts/adf.sh
```

Ez a szkript a következő dolgokat teszi:

1. Létrehoz egy egyszerű `Storage Blob Data Contributor` szolgáltatás a Data Lake Storage Gen2 tárfiók engedélyekkel.
1. Beszerez egy hitelesítési jogkivonatot a POST-kérelmek engedélyezéséhez a [Data Lake Storage Gen2 fájlrendszer REST API-jának.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Kitölti a Data Lake Storage Gen2 tárfiók `sparktransform.py` tényleges `query.hql` nevét a és a fájlokban.
1. A Data Lake Storage Gen2 és a Blob storage-fiókok tárolási kulcsok beszerzése.
1. Létrehoz egy másik erőforrás-telepítést az Azure Data Factory-folyamat létrehozásához a kapcsolódó kapcsolódó szolgáltatásokkal és tevékenységekkel. A tárolási kulcsokat paraméterekként továbbítja a sablonfájlba, hogy a csatolt szolgáltatások megfelelően hozzáférhessenek a tárfiókokhoz.

A Data Factory folyamat a következő parancson keresztül kerül üzembe helyezésre:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Az adatfolyamat futtatása

### <a name="trigger-the-data-factory-activities"></a>Indítsa el a Data Factory tevékenységeket

Az első tevékenység a Data Factory folyamat, amely létrehozott helyezi át az adatokat blob storage Data Lake Storage Gen2. A második tevékenység a Spark-átalakításokat alkalmazza az adatokra, és az átalakított .csv fájlokat egy új helyre menti. A teljes folyamat befejezéséhez eltarthat néhány perc.

A folyamatok indításához a következőket teheti:

* Indítsa el a Data Factory folyamatok a PowerShellben. Cserélje `DataFactoryName` le a tényleges Data Factory nevet, majd futtassa a következő parancsokat:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Vagy

* Nyissa meg az adatgyárat, és válassza **a Szerzői & monitor lehetőséget.** Indítsa el a másolási folyamatot, majd a Spark-folyamatot a portálról. A folyamatok portálon keresztüli indításáról az [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)az Azure Data Factory használatával című témakörben talál további információt.

A folyamatok futásának ellenőrzéséhez tegye az alábbi lépések egyikét:

* Lépjen az adat-előállító **Figyel** szakaszára a portálon keresztül.
* Az Azure Storage Explorerben nyissa meg a Data Lake Storage Gen 2 tárfiókot. Nyissa meg `files` a fájlrendszert, majd `transformed` lépjen a mappába, és ellenőrizze annak tartalmát, hogy a folyamat sikeres volt-e.

Az adatok HDInsight használatával történő átalakításának egyéb módjait [a Jupyter-jegyzetfüzet használatáról szóló cikk ismerteti.](/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tábla létrehozása az Interaktív lekérdezési fürtön a Power BI adatainak megtekintéséhez

1. Másolja `query.hql` a fájlt az LLAP-fürtbe az SCP használatával. Cserélje `LLAPCLUSTERNAME` le a tényleges nevet, majd írja be a parancsot:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Az SSH használatával érheti el az LLAP-fürtöt. Cserélje `LLAPCLUSTERNAME` le a tényleges nevet, majd írja be a parancsot. Ha nem módosította a `resourcesparameters.json` fájlt, a `Thisisapassword1`jelszó a .

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. A parancsfájl futtatásához használja a következő parancsot:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ez a parancsfájl egy felügyelt táblát hoz létre az interaktív lekérdezési fürtön, amelyet a Power BI-ból érhet el.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Power BI-irányítópult létrehozása értékesítési adatokból

1. Nyissa meg a Power BI Desktopot.
1. Válassza az **Adatok lekérése** lehetőséget.
1. Keresse meg a **HDInsight interaktív lekérdezési fürtöt.**
1. Illessze be a fürt URI-ját. A következő formátumban kell lennie: `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Adja `default` meg az adatbázist.
1. Adja meg a fürt eléréséhez használt felhasználónevet és jelszót.

Az adatok betöltése után kísérletezhet a létrehozni kívánt irányítópulttal. A Power BI irányítópultjainak első lépéseiaz alábbi hivatkozásokon található:

* [Irányítópultok a Power BI szolgáltatás tervezői számára – bevezetés](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: A Power BI szolgáltatás ismerkedése](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az összes erőforrást a következő paranccsal, hogy ne kelljen fizetnie.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kivonás, átalakítás és betöltés (ETL) nagy méretekben](./hadoop/apache-hadoop-etl-at-scale.md)
