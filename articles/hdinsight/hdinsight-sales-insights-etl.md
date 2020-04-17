---
title: 'Oktatóanyag: Hozzon létre egy teljes körű ETL-folyamatot az Azure HDInsight értékesítési elemzési adatainak levezetnie'
description: Megtudhatja, hogyan hozhat létre ETL-folyamatokat az Azure HDInsightsegítségével, hogy a Spark igény szerinti fürtjei és a Power BI használatával betekintést nyerjen az értékesítési adatokból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: c213b0089af0af295d44afd38bbc5c17b6db159d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535230"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Oktatóanyag: Hozzon létre egy teljes körű adatfolyamatot az Azure HDInsight értékesítési elemzési adatainak levezetnie

Ebben az oktatóanyagban egy végpontok között futó adatfolyamatot hozhat létre, amely kibontási, átalakítási és betöltési (ETL) műveleteket hajt végre. A folyamat az Azure HDInsighton futó [Apache Spark](./spark/apache-spark-overview.md) és Apache Hive-fürtöket fogja használni az adatok lekérdezéséhez és kezeléséhez. Olyan technológiákat is használhat, mint az Azure Data Lake Storage Gen2 az adattároláshoz, és a Power BI a vizualizációhoz.

Ez az adatfolyamat egyesíti a különböző tárolókból származó adatokat, eltávolítja a nem kívánt adatokat, új adatokat fűz hozzá, és mindezt visszatölti a tárolóba az üzleti elemzések megjelenítéséhez. Tudjon meg többet az ETL-folyamatokról [az Extract, transform és load (ETL) nagy méretekben.](./hadoop/apache-hadoop-etl-at-scale.md)

![ETL architektúra](./media/hdinsight-sales-insights-etl/architecture.png)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Azure CLI - legalább 2.2.0-s verzió. Lásd: [Az Azure CLI telepítése.](https://docs.microsoft.com/cli/azure/install-azure-cli)

* jq, egy parancssori JSON processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Az Azure [beépített szerepkörének](../role-based-access-control/built-in-roles.md)egyik tagja – tulajdonos.

* Ha a PowerShell használatával elindítja a Data Factory folyamatot, szüksége lesz az [Az modulra.](https://docs.microsoft.com/powershell/azure/overview)

* [A Power BI Desktop](https://aka.ms/pbiSingleInstaller) az oktatóanyag végén megjeleníti az üzleti elemzéseket.

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>A tárház klónozása parancsfájlokkal és adatokkal

1. Jelentkezzen be Azure-előfizetésbe. Ha az Azure Cloud Shell használatát tervezi, válassza a **Próbálja ki** a kódblokk jobb felső sarkában. Máshol, belép a követel alul:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Győződjön meg arról, hogy tagja az Azure-szerepkör [tulajdonosának.](../role-based-access-control/built-in-roles.md) Cserélje `user@contoso.com` le a fiókját, majd írja be a parancsot:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Ha nem ad vissza rekordot, akkor nem tagja, és nem tudja befejezni ezt az oktatóanyagot.

1. Töltse le az oktatóanyag adatait és parancsfájljait a [HDInsight sales insights ETL tárházból.](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) Írja be a következő parancsot:

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

1. Változó beállítása az erőforráscsoporthoz. Cserélje `RESOURCE_GROUP_NAME` le egy meglévő vagy új erőforráscsoport nevére, majd írja be a következő parancsot:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Futtassa a szkriptet. Cserélje `LOCATION` le a kívánt értéket, majd írja be a parancsot:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Ha nem biztos abban, hogy melyik régiót adja meg, az [az-fióklista-helyek](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list-locations) paranccsal lekérheti az előfizetés támogatott régióinak listáját.

    A parancs a következő erőforrásokat telepíti:

    * Egy Azure Blob-tárfiók. Ez a számla a vállalati értékesítési adatokat fogja tárolni.
    * Egy Azure Data Lake Storage Gen2-fiók. Ez a fiók mindkét HDInsight-fürt tárfiókjaként szolgál. Tudjon meg többet a HDInsight és a Data Lake Storage Gen2 az [Azure HDInsight integrációs Data Lake Storage Gen2.](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
    * Felhasználó által hozzárendelt felügyelt identitás. Ez a fiók hozzáférést biztosít a HDInsight-fürtöknek a Data Lake Storage Gen2 fiókhoz.
    * Egy Apache Spark-fürt. Ez a fürt a nyers adatok karbantartására és átalakítására szolgál.
    * Egy Apache Hive [interaktív lekérdezési](./interactive-query/apache-interactive-query-get-started.md) fürt. Ez a fürt lehetővé teszi az értékesítési adatok lekérdezését és megjelenítését a Power BI-val.
    * A hálózati biztonsági csoport (NSG) szabályai által támogatott Azure virtuális hálózat. Ez a virtuális hálózat lehetővé teszi a fürtök számára a kommunikációt és a kommunikáció biztonságát.

A fürt létrehozása körülbelül 20 percet is igénybe vehet.

A fürtök SSH-hozzáférésének alapértelmezett `Thisisapassword1`jelszava a . Ha módosítani szeretné a jelszót, `./templates/resourcesparameters_remainder.json` nyissa meg a fájlt, `llapClusterLoginPassword`és `llapsshPassword` módosítsa a `sparksshPassword`, `sparkClusterLoginPassword`, és a paraméterek jelszavát.

### <a name="verify-deployment-and-collect-resource-information"></a>A telepítés ellenőrzése és az erőforrásadatok összegyűjtése

1. Ha szeretné ellenőrizni a központi telepítés állapotát, nyissa meg az erőforráscsoportot az Azure Portalon. A **Beállítások csoportban**válassza **a Központi telepítések**lehetőséget, majd a központi telepítést. Itt láthatja a sikeresen üzembe helyezett erőforrásokat és a még folyamatban lévő erőforrásokat.

1. A fürtök nevének megtekintéséhez írja be a következő parancsot:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Az Azure storage-fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. A Data Lake Storage Gen2 fiók és a hozzáférési kulcs megtekintéséhez írja be a következő parancsot:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

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

Az Azure Data Factory-folyamat beállításához hajtsa végre az alábbi parancsot.  Még mindig a `hdinsight-sales-insights-etl` könyvtárban kellene lenned.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Ez a szkript a következő dolgokat teszi:

1. Létrehoz egy egyszerű `Storage Blob Data Contributor` szolgáltatás a Data Lake Storage Gen2 tárfiók engedélyekkel.
1. Beszerez egy hitelesítési jogkivonatot a POST-kérelmek engedélyezéséhez a [Data Lake Storage Gen2 fájlrendszer REST API-jának.](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create)
1. Kitölti a Data Lake Storage Gen2 tárfiók `sparktransform.py` tényleges `query.hql` nevét a és a fájlokban.
1. A Data Lake Storage Gen2 és a Blob storage-fiókok tárolási kulcsok beszerzése.
1. Létrehoz egy másik erőforrás-telepítést az Azure Data Factory-folyamat létrehozásához a kapcsolódó kapcsolódó szolgáltatásokkal és tevékenységekkel. A tárolási kulcsokat paraméterekként továbbítja a sablonfájlba, hogy a csatolt szolgáltatások megfelelően hozzáférhessenek a tárfiókokhoz.

## <a name="run-the-data-pipeline"></a>Az adatfolyamat futtatása

### <a name="trigger-the-data-factory-activities"></a>Indítsa el a Data Factory tevékenységeket

Az első tevékenység a Data Factory folyamat, amely létrehozott helyezi át az adatokat blob storage Data Lake Storage Gen2. A második tevékenység a Spark-átalakításokat alkalmazza az adatokra, és az átalakított .csv fájlokat egy új helyre menti. A teljes folyamat befejezéséhez eltarthat néhány perc.

A Data Factory nevének beolvasásához írja be a következő parancsot:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

A folyamat elindításához a következőket teheti:

* Indítsa el a Data Factory folyamat a PowerShellben. Cserélje `RESOURCEGROUP`le `DataFactoryName` a és a megfelelő értékeket, majd futtassa a következő parancsokat:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Szükség szerint `Get-AzDataFactoryV2PipelineRun` újra végrehajtja a végrehajtást.

    Vagy

* Nyissa meg az adatgyárat, és válassza **a Szerzői & monitor lehetőséget.** Indítsa `IngestAndTransform` el a folyamatot a portálról. A folyamatok portálon keresztüli indításáról az [Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline)az Azure Data Factory használatával című témakörben talál további információt.

A folyamat futásának ellenőrzéséhez tegye az alábbi lépések egyikét:

* Lépjen az adat-előállító **Figyel** szakaszára a portálon keresztül.
* Az Azure Storage Explorerben nyissa meg a Data Lake Storage Gen 2 tárfiókot. Nyissa meg `files` a fájlrendszert, majd `transformed` lépjen a mappába, és ellenőrizze annak tartalmát, hogy a folyamat sikeres volt-e.

Az adatok HDInsight használatával történő átalakításának egyéb módjait [a Jupyter-jegyzetfüzet használatáról szóló cikk ismerteti.](/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tábla létrehozása az Interaktív lekérdezési fürtön a Power BI adatainak megtekintéséhez

1. Másolja `query.hql` a fájlt az LLAP-fürtbe az SCP használatával. Írja be a parancsot:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Emlékeztető: Az alapértelmezett `Thisisapassword1`jelszó .

1. Az SSH használatával érheti el az LLAP-fürtöt. Írja be a parancsot:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. A parancsfájl futtatásához használja a következő parancsot:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Ez a parancsfájl egy felügyelt táblát hoz létre az interaktív lekérdezési fürtön, amelyet a Power BI-ból érhet el.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Power BI-irányítópult létrehozása értékesítési adatokból

1. Nyissa meg a Power BI Desktopot.

1. A menüben keresse meg az >  **Adatok beszedése****Többet...**  >  **Az Azure** > **HDInsight interaktív lekérdezése**.

1. Kattintson a **Csatlakozás** gombra.

1. A **HDInsight interaktív lekérdezése** párbeszédpanelen:
    1. A **Kiszolgáló** mezőbe írja be az LLAP-fürt nevét `https://LLAPCLUSTERNAME.azurehdinsight.net`a formátumban: .
    1. Az **adatbázis** szövegmezőjébe `default`írja be a be írt értéket.
    1. Válassza **az OK gombot.**

1. Az **AzureHive** párbeszédpanelről:
    1. A **Felhasználónév** mezőbe írja `admin`be a mezőbe.
    1. A **Jelszó** mezőbe írja `Thisisapassword1`be a mezőbe a mezőt.
    1. Kattintson a **Csatlakozás** gombra.

1. A **Navigátor**területen válassza a `sales`lehetőséget, és/vagy `sales_raw` tekintse meg az adatok előnézetét. Az adatok betöltése után kísérletezhet a létrehozni kívánt irányítópulttal. A Power BI irányítópultjainak első lépéseiaz alábbi hivatkozásokon található:

* [Irányítópultok a Power BI szolgáltatás tervezői számára – bevezetés](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: A Power BI szolgáltatás ismerkedése](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem fogja tovább használni ezt az alkalmazást, törölje az összes erőforrást a következő paranccsal, hogy ne kelljen fizetnie.

1. Az erőforráscsoport eltávolításához írja be a következő parancsot:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Az egyszerű szolgáltatás eltávolításához írja be a következő parancsokat:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kivonás, átalakítás és betöltés (ETL) nagy méretekben](./hadoop/apache-hadoop-etl-at-scale.md)
