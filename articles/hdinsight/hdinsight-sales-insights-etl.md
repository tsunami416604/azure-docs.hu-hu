---
title: 'Oktatóanyag: teljes körű ETL-folyamat létrehozása értékesítési ismeretek kinyeréséhez'
description: Ismerje meg, hogyan hozhat létre ETL-folyamatokat az Azure HDInsight, és hogyan származtathatja az értékesítési adatokból származó információkat a Spark igény szerinti fürtök és a Power BI használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: b9bcaf4b7497e8beba377eb7e47a44a6eb061299
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178006"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Oktatóanyag: végpontok közötti adatfolyamat létrehozása értékesítési elemzések készítéséhez

Ebben az oktatóanyagban egy végpontok közötti adatfolyamatot fog kiépíteni, amely kinyerési, átalakítási és betöltési (ETL) műveleteket hajt végre. A folyamat az Azure HDInsight-on futó Apache Spark és Apache Hive fürtöket fogja használni az adatlekérdezéshez és-kezeléshez. Emellett olyan technológiákat is használhat, mint a Azure Data Lake Storage Gen2 az adattároláshoz, és Power BI a vizualizációhoz.

Ez az adatfolyamat a különböző áruházakból származó adatok összegyűjtését, a nemkívánatos adatok eltávolítását, az új adatok hozzáfűzését és a tárhelyre való visszatöltését az üzleti elemzések megjelenítéséhez. További információ a [kinyerési, átalakítási és betöltési (etl)](./hadoop/apache-hadoop-etl-at-scale.md)folyamatokról a skálán.

![ETL-architektúra](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

A [Power bi Desktop](https://www.microsoft.com/download/details.aspx?id=45331) letöltésével megjelenítheti az oktatóanyag végén megjelenő üzleti ismereteket.

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="clone-the-repository-with-scripts-and-data"></a>A tárház klónozása parancsfájlokkal és adatkezeléssel

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Nyissa meg Azure Cloud Shell a felső menüsorban. Válassza ki az előfizetését fájlmegosztás létrehozásához, ha Cloud Shell kéri.

   ![Az Azure Cloud Shell megnyitása](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. A **környezet kiválasztása** legördülő menüben válassza a **bash**elemet.
1. Jelentkezzen be az Azure-fiókjába, és állítsa be az előfizetést. 
1. Állítsa be a projekthez tartozó erőforráscsoportot.
   1. Adjon egyedi nevet az erőforráscsoportnak.
   1. Futtassa az alábbi kódrészletet a Cloud Shellban a későbbi lépésekben használt változók megadásához:

       ```azurecli-interactive 
       resourceGroup="<RESOURCE GROUP NAME>"
       subscriptionID="<SUBSCRIPTION ID>"
        
       az account set --subscription $subscriptionID
       az group create --name $resourceGroup --location westus
       ```

1. Töltse le az oktatóanyaghoz tartozó adatokat és parancsfájlokat a [HDInsight Sales bepillantást az ETL adattárból](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) az alábbi parancsok megadásával Cloud Shellban:

    ```azurecli-interactive 
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Írja be a `ls` értéket a rendszerhéj parancssorába, és ellenőrizze, hogy a következő fájlok és könyvtárak lettek-e létrehozva:

   ```output
   /salesdata/
   /scripts/
   /templates/
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>A folyamathoz szükséges Azure-erőforrások üzembe helyezése 

1. Adja hozzá a `chmod +x scripts/*.sh` parancsfájl végrehajtási engedélyeit.
1. A parancsfájl futtatásához használja a `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` parancsot a következő erőforrások Azure-beli üzembe helyezéséhez:

   1. Egy Azure Blob Storage-fiók. Ez a fiók fogja tárolni a vállalati értékesítési adatforgalmat.
   2. Egy Azure Data Lake Storage Gen2-fiók. Ez a fiók a HDInsight-fürtökhöz tartozó Storage-fiókként fog szolgálni. További információ: HDInsight és Data Lake Storage Gen2 az [Azure HDInsight-integrációban Data Lake Storage Gen2okkal](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
   3. Felhasználó által hozzárendelt felügyelt identitás. Ez a fiók biztosítja a HDInsight-fürtök számára a Data Lake Storage Gen2 fiók elérését.
   4. Egy Apache Spark-fürt. Ezt a fürtöt a rendszer a nyers Adattisztításra és átalakításra használja.
   5. Egy Apache Hive interaktív lekérdezési fürt. Ez a fürt lehetővé teszi az értékesítési adatlekérdezést és a Power BIsal való megjelenítését.
   6. Hálózati biztonsági csoport (NSG) szabályai által támogatott Azure-beli virtuális hálózat. Ez a virtuális hálózat lehetővé teszi, hogy a fürtök kommunikálhassanak és biztonságossá tegye a kommunikációt. 

A fürt létrehozása körülbelül 20 percet vesz igénybe.

A `resources.sh` parancsfájl a következő parancsot tartalmazza. Ez a parancs egy Azure Resource Manager sablon (`resourcestemplate.json`) használatával hozza létre a megadott erőforrásokat a kívánt konfigurációval.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

A `resources.sh` parancsfájl az értékesítési adatok. csv fájljait is feltölti az újonnan létrehozott blob Storage-fiókba a következő parancs használatával:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

A fürtök SSH-hozzáférésének alapértelmezett jelszava `Thisisapassword1`. Ha módosítani szeretné a jelszót, nyissa meg a `resourcesparameters.json` fájlt, és módosítsa a `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` és `llapsshPassword` paraméterek jelszavát.

### <a name="verify-deployment-and-collect-resource-information"></a>Az üzembe helyezés ellenőrzése és az erőforrások adatainak összegyűjtése

1. Ha szeretné megtekinteni az üzemelő példány állapotát, nyissa meg az erőforráscsoportot a Azure Portal. A **Beállítások**területen válassza a **központi telepítések** lehetőséget. Válassza ki az üzemelő példány nevét, `ResourcesDeployment`. Itt láthatja a sikeresen telepített erőforrásokat és a még folyamatban lévő erőforrásokat.
1. A telepítés befejezése után lépjen a Azure Portal > **erőforráscsoportok** > < RESOURCE_GROUP_NAME >.
1. Keresse meg az értékesítési fájlok tárolására létrehozott új Azure Storage-fiókot. A Storage-fiók neve `blob` karakterrel kezdődik, majd véletlenszerű karakterláncot tartalmaz. Tegye a következőket:
   1. Jegyezze fel a Storage-fiók nevét későbbi használatra.
   1. Válassza ki a blob Storage-fiók nevét.
   1. A portál bal oldalán, a **Beállítások**területen válassza a **hozzáférési kulcsok**elemet.
   1. Másolja a karakterláncot a **Key1** mezőbe, és mentse későbbi használatra.
1. Keresse meg a HDInsight-fürtök számára tárolóként létrehozott Data Lake Storage Gen2 fiókot. Ez a fiók ugyanabban az erőforráscsoportban található, mint a blob Storage-fiók, de a `adlsgen2` karakterrel kezdődik. Tegye a következőket:
   1. Jegyezze fel a Data Lake Storage Gen2 fiók nevét.
   1. Válassza ki a Data Lake Storage Gen2 fiók nevét.
   1. A portál bal oldalán, a **Beállítások**területen válassza a **hozzáférési kulcsok**elemet.
   1. Másolja a karakterláncot a **Key1** mezőbe, és mentse későbbi használatra.

> [!Note]
> Ha ismeri a Storage-fiókok nevét, a következő paranccsal kérheti le a fiók kulcsait a Azure Cloud Shell parancssorban:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-a-data-factory"></a>Data factory létrehozása

A Azure Data Factory egy olyan eszköz, amely segít automatizálni az Azure-folyamatokat. Ezen feladatok elvégzése nem az egyetlen módszer, de ez nagyszerű módszer a folyamatok automatizálására. A Azure Data Factoryről a [Azure Data Factory dokumentációjában](https://azure.microsoft.com/en-us/services/data-factory/)talál további információt. 

Ez az adatfeldolgozó egyetlen folyamattal rendelkezik, amely két tevékenységgel rendelkezik: 

- Az első tevékenység az Azure Blob Storage-ból származó adatok másolását a Data Lake Storage Gen 2 Storage-fiókba másolja az adatfeldolgozási műveletek elvégzéséhez.
- A második tevékenység átalakítja a Spark-fürtben található adatfájlokat. A parancsfájl a nemkívánatos oszlopok eltávolításával átalakítja az adattípust. Egy új oszlopot is hozzáfűz, amely kiszámítja az egyetlen tranzakció által generált bevételt.

A Azure Data Factory folyamat beállításához futtassa a `adf.sh` parancsfájlt:

1. A fájlhoz a `chmod +x adf.sh` paranccsal adhat hozzá végrehajtási engedélyeket.
1. A szkript futtatásához használja a `./adf.sh` parancsot. 

A szkript a következő műveleteket végzi el:

1. Egy egyszerű szolgáltatásnevet hoz létre `Storage Blob Data Contributor` engedélyekkel a Data Lake Storage Gen2 Storage-fiókhoz.
1. Hitelesítési jogkivonat beszerzése a POST kérések engedélyezéséhez a [Data Lake Storage Gen2 fájlrendszer REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. A Data Lake Storage Gen2 Storage-fiók tényleges nevét adja meg a `sparktransform.py` és a `query.hql` fájlokban.
1. A Data Lake Storage Gen2-és blob Storage-fiókokhoz tartozó tárolási kulcsok beszerzése.
1. Egy másik erőforrás-telepítést hoz létre egy Azure Data Factory-folyamat létrehozásához társított kapcsolódó szolgáltatásokkal és tevékenységekkel. A tárolási kulcsokat paraméterként adja át a sablonfájl számára, hogy a társított szolgáltatások megfelelően hozzáférhessenek a Storage-fiókokhoz.

A Data Factory folyamat a következő paranccsal telepíthető:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Az adatfolyamat futtatása

### <a name="trigger-the-data-factory-activities"></a>A Data Factory tevékenységek kiváltása

A létrehozott Data Factory folyamat első tevékenysége áthelyezi az adatait a blob Storage-ból a Data Lake Storage Gen2ba. A második tevékenység alkalmazza a Spark-átalakításokat az adathalmazon, és menti az átalakított. csv fájlokat egy új helyre. A teljes folyamat eltarthat néhány percig.

A folyamatok elindításához a következők közül választhat:

- Futtassa az alábbi parancsokat a Data Factory folyamatok PowerShellben való elindításához: 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

- Nyissa meg az adatelőállítót, és válassza a **szerző & figyelő**elemet. Aktiválja a másolási folyamatot, majd a Spark-folyamatot a portálról. A folyamatoknak a portálon keresztüli aktiválásával kapcsolatos további információkért lásd: [igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

A folyamatok futtatásának ellenőrzéséhez hajtsa végre a következő lépések egyikét:

- A portálon keresztül nyissa meg a **monitoring** szakaszt az adatgyárban.
- A Azure Storage Explorerban nyissa meg a Data Lake Storage 2. generációs Storage-fiókot. Lépjen a `files` fájlrendszerre, majd nyissa meg a `transformed` mappát, és ellenőrizze annak tartalmát, hogy látható-e a folyamat sikeres volt-e.

Az HDInsight használatával történő adatátalakítás egyéb módjairól [ebben a cikkben olvashat a Jupyter notebook használatáról](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Tábla létrehozása az interaktív lekérdezési fürtön Power BI

1. Másolja a `query.hql` fájlt a LLAP-fürtre SCP használatával:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Használja az SSH-t a LLAP-fürt eléréséhez a következő parancs használatával, majd adja meg a jelszavát. Ha nem módosította a `resourcesparameters.json` fájlt, a jelszó `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. A parancsfájl futtatásához használja a következő parancsot:

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Ez a szkript egy felügyelt táblát hoz létre az interaktív lekérdezési fürtön, amely a Power BIból érhető el. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Power BI irányítópult létrehozása értékesítési adatokból

1. Nyissa meg a Power BI Desktopot.
1. Válassza az **Adatok lekérése** lehetőséget.
1. Keresse meg a **HDInsight interaktív lekérdezési fürtöt**.
1. Illessze be a fürthöz tartozó URI-t. A következő formátumban kell lennie: `https://<LLAP CLUSTER NAME>.azurehdinsight.net`.

   Adja meg a `default` értéket az adatbázishoz.
1. Adja meg a fürt eléréséhez használt felhasználónevet és jelszót.

Az adatgyűjtés után kísérletezhet a létrehozni kívánt irányítópulttal. A Power BI-irányítópultok megismeréséhez tekintse meg az alábbi hivatkozásokat:

* [Power BI designerek irányítópultjának bemutatása](https://docs.microsoft.com/power-bi/service-dashboards)
* [Oktatóanyag: a Power BI szolgáltatás első lépései](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi parancs használatával törölje az összes erőforrást, hogy ne legyen rájuk felszámítva.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kinyerés, átalakítás és betöltés (ETL) skálán](./hadoop/apache-hadoop-etl-at-scale.md)
