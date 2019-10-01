---
title: 'Oktatóanyag: Végpontok közötti kinyerési, átalakítási, betöltési (ETL) folyamat létrehozása az értékesítési eredmények származtatása érdekében'
description: Ismerje meg, hogyan hozhat létre ETL-folyamatokat az Azure HDInsight, és hogyan származtathatja az értékesítési adatokból származó információkat a Spark igény szerinti fürtök és a Power BI használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695819"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Oktatóanyag: Végpontok közötti adatfolyamat létrehozása az értékesítési elemzések kinyeréséhez

Ebben az oktatóanyagban egy végpontok közötti adatfolyamatot fog létrehozni, amely kinyerési, átalakítási és betöltési műveleteket hajt végre. A folyamat az Azure HDInsight-on futó Apache Spark és Apache Hive fürtöket fogja használni az adatlekérdezéshez és-kezeléshez. A felhasznált egyéb technológiák közé tartoznak az adattárolás Data Lake Storage Gen2, valamint a vizualizációk Power BI.

Ez az adatfolyamat ötvözi az összes különböző áruházból származó adatmennyiséget, eltávolítja a nemkívánatos adatmennyiséget, hozzáfűzi az új adatokhoz, és visszatölti ezt a tárhelyre az üzleti elemzések megjelenítéséhez. További információ az ETL-folyamatokról [: kinyerési, átalakítási és betöltési (etl) skálán](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-architektúra](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

A [Power bi Desktop](https://www.microsoft.com/download/details.aspx?id=45331) letöltésével megjelenítheti az oktatóanyag végén megjelenő üzleti ismereteket.

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>A tárház klónozása parancsfájlokkal és adatkezeléssel

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. Nyissa meg a Cloud Shell a felső menüsorban. Válassza ki az előfizetését fájlmegosztás létrehozásához, ha a Azure Cloud Shellt kéri.

    ![Az Azure Cloud Shell megnyitása](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Válassza a "bash" lehetőséget a "környezet kiválasztása" legördülő menüből.
1. Jelentkezzen be az Azure-fiókjába, és állítsa be az előfizetést. 
1. Állítsa be a projekthez tartozó erőforráscsoportot.
    1. Válasszon egy egyedi erőforráscsoport-nevet.
    1. Futtassa az alábbi kódrészletet a Azure Cloud Shellban a későbbi lépésekben használt változók megadásához

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Az oktatóanyaghoz tartozó adatok és parancsfájlok letöltéséhez a [HDInsight Sales bepillantást az ETL adattárból](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) , ha beírja a következő parancsokat a Cloud Shell:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Írja be a `ls` parancsot a rendszerhéj parancssorába a következő fájlok és könyvtárak létrehozásához:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>A folyamathoz szükséges Azure-erőforrások üzembe helyezése 

1. Végrehajtási engedélyek hozzáadása a parancsfájlhoz `chmod +x scripts/*.sh`
1. A parancsfájl futtatásához használja a `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` parancsot a következő erőforrások Azure-beli üzembe helyezéséhez:

    1. Azure Blob Storage-fiók – ez a fiók fogja tárolni a vállalati értékesítési adatforgalmat
    2. Egy Azure Data Lake Storage Gen2 fiók – ez a fiók a HDInsight-fürtökhöz tartozó Storage-fiókként fog szolgálni. További információ: HDInsight és Data Lake Storage Gen2 az [Azure HDInsight-integrációban Data Lake Storage Gen2okkal](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Felhasználó által hozzárendelt felügyelt identitás – ez a fiók biztosítja a HDInsight-fürtök számára a Data Lake Storage Gen2 fiók elérését.
    4. Apache Spark-csillogás – ez a fürt a nyers Adattisztítás és-átalakítás során használatos
    5. Egy Apache Hive interaktív lekérdezési fürt – ez a fürt lehetővé teszi, hogy lekérdezze az értékesítési adatmegjelenítést a Power BI
    6. Hálózati biztonsági csoport (NSG) szabályai által támogatott Azure-beli virtuális hálózat – ez a virtuális hálózat lehetővé teszi a fürtök számára a kommunikációt és a kommunikáció biztonságát is. 

A fürt létrehozása körülbelül 20 percet vesz igénybe.

A `resources.sh` parancsfájl a következő parancsot tartalmazza, amely egy Resource Manager-sablont (`resourcestemplate.json`) használ a megadott erőforrások létrehozásához a kívánt konfigurációval.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

A `resources.sh` parancsfájl az értékesítési adatok CSV-fájljait is feltölti az újonnan létrehozott blob Storage-fiókba a paranccsal:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

A fürtök SSH-hozzáféréséhez használt alapértelmezett jelszó `Thisisapassword1`. Ha módosítani szeretné a jelszót, lépjen `resourcesparameters.json` fájlra, és módosítsa a `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` és `llapsshPassword` paraméterek jelszavát.

### <a name="verify-deployment-and-collect-resource-information"></a>Az üzembe helyezés ellenőrzése és az erőforrások adatainak összegyűjtése

1. Ha szeretné megtekinteni az üzemelő példány állapotát, keresse meg az erőforráscsoportot a Azure Portal. A **Beállítások**területen kattintson a **központi telepítések** elemre. Kattintson a központi telepítés nevére `ResourcesDeployment`. Itt láthatja a sikeresen telepített és a még folyamatban lévő erőforrásokat.
1. Miután az üzembe helyezés befejeződött, lépjen a Azure Portal > **erőforráscsoportok** > < RESOURCE_GROUP_NAME >
1. Keresse meg az értékesítési fájlok tárolására létrehozott új Azure Storage-fiókot. A Storage-fiók neve `blob` karakterrel kezdődik, majd véletlenszerű karakterláncot tartalmaz. 
    1. Jegyezze fel a Storage-fiók nevét későbbi használatra.
    1. Kattintson a blob Storage-fiók nevére.
    1. A portál bal oldalán, a **Beállítások**területen kattintson a **hozzáférési kulcsok**elemre.
    1. Másolja a karakterláncot a Box **Key1** , és mentse későbbi használatra.
1. Keresse meg a HDInsight-fürtök számára tárolóként létrehozott Data Lake Storage Gen2 fiókot. Ez a fiók ugyanabban az erőforráscsoportban található, mint a blob Storage-fiók, de a `adlsgen2` karakterrel kezdődik.
    1. Jegyezze fel a Data Lake Storage Gen2 fiók nevét.
    1. Kattintson a Data Lake Storage Gen2 fiók nevére.
    1. A portál bal oldalán, a **Beállítások**alatt kattintson a **hozzáférési kulcsok** elemre.
    1. Másolja a karakterláncot a Box **Key1** , és mentse későbbi használatra.

> [!Note]
> Ha ismeri a Storage-fiókok nevét, a következő parancs használatával is beolvashatja a fiók kulcsait a Azure Cloud Shell parancssorba:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Azure data factory létrehozása

A Azure Data Factory egy olyan eszköz, amely segít automatizálni az Azure-folyamatokat. Ezen feladatok elvégzése nem az egyetlen módszer, de ez nagyszerű módszer a folyamatok automatizálására. A Azure Data Factoryról további információt a további tudnivalókat ismertető [Azure Data Factory dokumentációjában](https://azure.microsoft.com/services/data-factory/)talál. 

Ez a Azure Data Factory egy folyamattal fog rendelkezni két tevékenységgel: 

1. Az első tevékenység az Azure Blob Storageról másolja át az adatait az Data Lake Storage Gen 2 Storage-fiókba az adatok betöltésének utánzásához.
2. A második tevékenység átalakítja a Spark-fürtben található adatfájlokat. A szkript átalakítja az adatmennyiséget a nemkívánatos oszlopok eltávolításával, valamint egy olyan új oszlop hozzáfűzésével, amely kiszámítja egy adott tranzakció által generált bevételt.

A Azure Data Factory folyamat beállításához futtassa a következő szkriptet: `adf.sh`:

1. Végrehajtási engedélyek hozzáadása a fájlhoz `chmod +x adf.sh` használatával
1. Futtassa a szkriptet a `./adf.sh` paranccsal 

A szkript a következő műveleteket végzi el:

1. Egy egyszerű szolgáltatásnevet hoz létre `Storage Blob Data Contributor` engedélyekkel a Data Lake Storage Gen2 Storage-fiókhoz.
1. Beolvas egy hitelesítési jogkivonatot a POST kérések engedélyezéséhez a [Data Lake Storage Gen2 FileSystem REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. A Data Lake Storage Gen2 Storage-fiók tényleges nevét adja meg a `sparktransform.py` és a `query.hql` fájlokban.
1. A Data Lake Storage Gen2 és Blob Storage fiókhoz tartozó tárolási kulcsok beszerzése.
1. Egy másik erőforrás-telepítést hoz létre egy Azure Data Factory-folyamat létrehozásához társított kapcsolódó szolgáltatásokkal és tevékenységekkel. A tárolási kulcsokat paraméterként adja át a sablonfájl számára, hogy a társított szolgáltatások megfelelően hozzáférhessenek a Storage-fiókokhoz.

Az ADF-folyamat üzembe helyezése a következő paranccsal történik:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Az adatfolyamat futtatása

### <a name="trigger-the-adf-activities"></a>Az ADF-tevékenységek kiváltása

A létrehozott ADF-folyamat első tevékenysége áthelyezi az adatait a blob Storage-ból Data Lake Storage Gen2ba. A második tevékenység a Spark-átalakításokat alkalmazza az adatsorokon, és menti az átalakított CSV-fájlokat egy új helyre. A teljes folyamat eltarthat néhány percig.

A folyamatok elindításához a következők közül választhat:

1. A következő parancsok futtatásával aktiválhatja az ADF-folyamatokat a PowerShellben. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Emellett megnyithatja a Data Factoryt, kiválaszthatja a szerző & figyelő elemet, és elindíthatja a másolási folyamatot, a Spark-folyamatot a portálról. Lásd: [igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben a Azure Data Factory használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline) a folyamatok a portálon keresztüli aktiválásával kapcsolatban.

A folyamatok végrehajtásának ellenőrzéséhez hajtsa végre a következő lépések egyikét:

1. Navigáljon a Azure Data Factory **figyelő** szakaszához a portálon keresztül.
2. Lépjen a Data Lake Storage Gen 2 Storage-fiók Storage Explorerben, lépjen a `files` fájlrendszerre, és navigáljon a `transformed` mappához, és ellenőrizze annak tartalmát, hogy a folyamat sikeres volt-e.

Az adatátalakítás egyéb módjai a HDInsight használatával: az [Jupyter notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query) használata

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tábla létrehozása az interaktív lekérdezési fürtön Power BI

1. Másolja a Query. HQL fájlt a LLAP-fürtre SCP használatával:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. SSH-t a LLAP-fürtön a következő parancs használatával, majd adja meg a jelszavát. Ha nem módosította a `resourcesparameters.json` fájlt, a jelszó `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Futtassa a következő parancsot a parancsfájl végrehajtásához.

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ez a szkript egy felügyelt táblát hoz létre az interaktív lekérdezési fürtön, amely a Power BIból érhető el. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Power BI irányítópult létrehozása értékesítési adatokból

1. Power BI Desktop megnyitása
1. Válassza az **Adatok lekérése** lehetőséget.
1. Keresse meg a **HDInsight interaktív lekérdezési fürtöt**.
1. Illessze be a fürthöz tartozó URI-t. Az adatbázishoz `https://<LLAP CLUSTER NAME>.azurehdinsight.net` típusú `default` formátumúnak kell lennie.
1. Adja meg a fürt eléréséhez használt felhasználónevet és jelszót.

Az adatgyűjtés után kísérletezhet a létrehozni kívánt irányítópulttal. Tekintse meg a következő hivatkozásokat az Power BI-irányítópultok használatába:

* [Power BI designerek irányítópultjának bemutatása](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: Ismerkedés a Power BI szolgáltatás @ no__t-0

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az összes erőforrást a következő lépésekkel, hogy ne legyen rájuk felszámítva.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kinyerés, átalakítás és betöltés (ETL) skálán](./hadoop/apache-hadoop-etl-at-scale.md)
