---
title: "Igény szerinti használatával a Data Factory - Azure HDInsight Hadoop-fürtök létrehozása |} Microsoft Docs"
description: "Ismerje meg, igény szerinti Hadoop-fürtök létrehozása a HDInsight az Azure Data Factory használatával."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: c1061811d205494969047fa3f91cbf449a25d8ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Igény szerinti Hadoop-fürtök létrehozása a Hdinsightban Azure Data Factory használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Az Azure Data Factory](../data-factory/introduction.md) egy felhőalapú integrációs szolgáltatás koordinálja és automatizálja az adatátviteli és az adatok átalakítása. Létrehozhat egy HDInsight Hadoop fürthöz közvetlenül az időponthoz kötött egy bemeneti adatszelet feldolgozni, és törölheti a fürtöt, ha feldolgozása befejeződött. Egy igény szerinti HDInsight Hadoop-fürt használatának előnyei a következők:

- Ön egyetlen fizetési idő feladat fut. a HDInsight Hadoop-fürt (és egy rövid konfigurálható üresjárati idő). A HDInsight-fürtök számlázása pro-értékelés percenként történik, függetlenül attól, hogy azokat, vagy nem. A Data Factory egy igény szerinti HDInsight kapcsolódó szolgáltatás használatakor a fürtök igény jönnek létre. És a fürt automatikusan törlődnek a feladatok elvégzésekor. Ezért csak kell fizetnie a feladat fut, és a rövid üresjárati idő (live idő beállítása).
- Használatával a Data Factory-folyamathoz Munkafolyamat létrehozásához. Például lehet egy helyi SQL Server adatainak másolása az Azure blob Storage tárolóban, az adatok feldolgozása a Hive parancsfájlok és a Pig-parancsprogram futtatásával az igény szerinti HDInsight Hadoop-fürt folyamat. Ezután másolja az eredményadatok Azure SQL Data Warehouse BI alkalmazások felhasználását.
- A munkafolyamat futtatását időnként (óránként, naponta, hetente, havonta, stb.) is ütemezheti.

Az Azure Data Factoryben adat-előállító rendelkezhet egy vagy több adat folyamatok. Adatok folyamat rendelkezik egy vagy több tevékenységet. Tevékenységek két típusa van: [adatok mozgása tevékenységek](../data-factory/copy-activity-overview.md) és [adatok átalakítása tevékenységek](../data-factory/transform-data.md). Adatok mozgása tevékenységek (jelenleg csak másolási tevékenység) használatával helyezze át az adatokat egy forrás adattárból a cél-tárolóban. Adatok átalakítása tevékenységek segítségével átalakítási/folyamat adatokat. HDInsight Hive tevékenység a Data Factory által támogatott átalakítása tevékenységek egyike. Ebben az oktatóanyagban a Hive átalakítási tevékenységet használja.

A hive tevékenység saját HDInsight Hadoop-fürt vagy egy igény szerinti HDInsight Hadoop-fürt használatára konfigurálhatja. Ebben az oktatóanyagban a data factory-folyamat a Hive tevékenység igény szerinti HDInsight-fürt használatára van konfigurálva. Ezért amikor a tevékenység fut egy adatszelet feldolgozni, ez történik:

1. A HDInsight Hadoop-fürt automatikusan létrejön, közvetlenül az időponthoz kötött a szelet feldolgozása.  
2. A bemeneti adatokat dolgozza fel a fürtön fut egy HiveQL-parancsfájlt.
3. A HDInsight Hadoop-fürtök törlése a feldolgozás befejezése után, a fürt üresjáratban a beállított időn (timeToLive-beállítást). A timeToLive üresjárati idő feldolgozás a következő adatszelet érhető el, ha ugyanabban a fürtben szolgál a szelet feldolgozása.  

Ebben az oktatóanyagban a hive tevékenységhez társított HiveQL-parancsfájlt az alábbi műveleteket hajtja végre:

1. Létrehoz egy külső táblát, amely a nyers webes naplóadatokat, egy Azure Blob storage-ban tárolt hivatkozik.
2. Partíciók évben és hónapban a nyers adatok.
3. A particionált adatot tárol az Azure blob storage.

Ebben az oktatóanyagban a HiveQL-parancsfájlt a hive tevékenységhez társított létrehoz egy külső táblát, amely a nyers webes naplóadatokat, az Azure Blob Storage tárolóban tárolt hivatkozik. Az alábbiakban a minta sorok minden hónapban a bemeneti fájl.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

A HiveQL-parancsfájlt a nyers adatok particionálja a hónap és év szerint. Három kimeneti mappa az előző bemeneti alapján hoz létre. Minden mappa minden hónap bejegyzéseinek fájlt tartalmaz.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Adat-előállító átalakítása tevékenységek struktúrát tevékenység mellett listájáért lásd: [alakít át és elemez az Azure Data Factory használatával](../data-factory/transform-data.md).

> [!NOTE]
> Jelenleg csak létrehozhat HDInsight fürt 3.2-es verziójú Azure Data Factory.

## <a name="prerequisites"></a>Előfeltételek
A jelen cikkben lévő utasítások megkezdése előtt rendelkeznie kell a következő elemek:

* [Azure-előfizetés](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Készítse elő a storage-fiók
Ebben a forgatókönyvben legfeljebb három storage-fiókok is használhatja:

- a HDInsight-fürthöz az alapértelmezett tárfiók
- a bemeneti adatok tárfiók
- a kimeneti adatok Storage-fiók

Az oktatóanyag leegyszerűsítése segítségével egy tárfiókot a három célokra szolgál. Az Azure PowerShell-parancsfájlpélda ebben a szakaszban található a következő feladatokat hajtja végre:

1. Jelentkezzen be az Azure-bA.
2. Azure-erőforráscsoport létrehozása
3. Hozzon létre egy Azure Storage-fiókot.
4. A tárfiók egy Blob-tároló létrehozása
5. A következő két fájlt másolja a Blob-tároló:

   * A bemeneti adatfájlt: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * HiveQL-parancsfájlt: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Mindkét fájljai a következő nyilvános blobtárolóban.


**A tárterület előkészítése és Azure PowerShell használatával másolhat fájlokat:**
> [!IMPORTANT]
> Adjon meg nevet az Azure erőforráscsoport és az Azure storage-fiók, a parancsfájl által létrehozott.
> Írja le **erőforráscsoport-név**, **tárfióknév**, és **tárfiók kulcsa** outputted a parancsfájl által. Már szükség a következő szakaszban.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Ha a PowerShell parancsfájl segítségre van szüksége, tekintse meg a [az Azure PowerShell használata az Azure Storage](../storage/common/storage-powershell-guide-full.md). Ha szeretné, hogy az Azure parancssori felület használata, tekintse meg a [függelék](#appendix) szakaszban az Azure CLI-parancsfájlt.

**A tárfiók és a tartalom vizsgálata**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforráscsoportok** a bal oldali ablaktáblán.
3. Kattintson duplán a PowerShell parancsfájl létrehozott az erőforráscsoport neve. A szűrő használja, ha túl sok erőforrás-csoportok szerepel a listában.
4. Az a **erőforrások** csempe, kell rendelkeznie kivéve, ha az erőforráscsoport megosztása más projektek felsorolt egy erőforrást. Adott erőforrás a korábban megadott nevű tárfiók. Kattintson a tárfiók nevét.
5. Kattintson a **Blobok** csempék.
6. Kattintson a **adfgetstarted** tároló. Két mappák látja: **inputdata** és **parancsfájl**.
7. Nyissa meg a mappát, és a mappákban található fájlokat. A inputdata tartalmazza a input.log fájl bemeneti adatokkal, és a parancsfájl mappa tartalmazza a HiveQL-parancsfájlt.

## <a name="create-a-data-factory-using-resource-manager-template"></a>A Resource Manager sablonnal adat-előállító létrehozása
A tárfiók, a bemeneti adatok és a HiveQL-parancsfájlt készített készen áll az Azure data factory létrehozása. Többféleképpen az adat-előállító létrehozása. Ebben az oktatóanyagban létrehoz egy adat-előállító telepítésével az Azure Resource Manager-sablon az Azure portál használatával. A Resource Manager-sablon használatával is telepítheti [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) és [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Más data factory létrehozási módszert, lásd: [oktatóanyag: az első adat-előállító létrehozása](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon. A sablon https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json helyezkedik el. Tekintse meg a [adat-előállító bejegyzései szerepelnek a sablon](#data-factory-entities-in-the-template) szakasz a sablonban definiált entitások kapcsolatos részletes információkat. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Válassza ki **használata meglévő** választás a **erőforráscsoport** beállításával, és válassza ki a nevét (a PowerShell-parancsfájl használatával), az előző lépésben létrehozott erőforráscsoportot.
3. Adjon meg egy nevet az adat-előállítóban (**adat-előállító**). Ez a név globálisan egyedinek kell lennie.
4. Adja meg a **tárfióknév** és **tárfiók kulcsa** az előző lépésben megírt.
5. Válassza ki **elfogadom a feltételeket és kikötéseket** elolvasása után említettük **feltételek és kikötések**.
6. Válassza ki **rögzítés az irányítópulton** lehetőséget.
6. Kattintson a **beszerzés/létrehozása**. Megjelenik az Irányítópulton egy csempére nevű **telepítése sablon-üzembehelyezés**. Várjon, amíg a **erőforráscsoport** az erőforráscsoport panel nyílik meg. A csempe jelenik meg az erőforráscsoport neve az erőforráscsoport panel megnyitásához, is kattinthat.
6. Kattintson a csempére kattintva nyissa meg az erőforráscsoportot, ha az erőforráscsoport panel még nincs megnyitva. Most látni fog egy további adatokat előállító szereplő erőforrásra a tárolási fiók erőforrások mellett.
7. Kattintson a data factory nevét (a megadott értékre a **adat-előállító** paraméter).
8. A Data Factory panelen kattintson a **Diagram** csempére. Az ábrán látható egy bemeneti adatkészlet, és egy kimeneti adatkészlet egy tevékenységet:

    ![Az Azure Data Factory HDInsight igény Hive tevékenység folyamat diagramja](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    A nevek a Resource Manager-sablon vannak definiálva.
9. Kattintson duplán a **AzureBlobOutput**.
10. Az a **Recent frissítése szeletek**, ekkor megjelenik egy szelet. Ha az állapot **folyamatban**, várjon, amíg értékre módosul **készen**. Általában vesz igénybe **20 perc** hozhat létre HDInsight-fürtöt.

### <a name="check-the-data-factory-output"></a>Ellenőrizze a data factory kimenet

1. A legutóbbi munkamenet ugyanazzal az eljárással használatával ellenőrizze a tárolók a adfgetstarted tároló. Nincsenek kívül két új tárolók **adfgetsarted**:

   * Egy tároló megadásával, amely mintát követi: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Ebben a tárolóban az alapértelmezett tároló a HDInsight-fürthöz.
   * adfjobs: Ebben a tárolóban az ADF feladatnaplóit az alkalmazása tárolója.

     A data factory kimeneti tárolódik **afgetstarted** a Resource Manager sablon konfigurált.
2. Kattintson a **adfgetstarted**.
3. Kattintson duplán a **partitioneddata**. Megjelenik egy **év = 2014** mappa, mert a webalkalmazás-naplók dátuma 2014-ben.

    ![Az Azure Data Factory HDInsight igény Hive tevékenység folyamat kimeneti](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Ha a lista részletező, január, február és március kell tekintse meg a három mappa. És minden hónapban van egy naplófájl.

    ![Az Azure Data Factory HDInsight igény Hive tevékenység folyamat kimeneti](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Data Factory-entitások a sablonban
Itt látható, hogyan néz egy adat-előállító legfelső szintű erőforrás-kezelő sablonjának:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Data Factory definiálása
A data factoryt a Resource Manager-sablonban definiálhatja az alábbi minta szerint:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
A dataFactoryName, adja meg a sablon telepítésekor adat-előállító nevét. Adat-előállító jelenleg csak az USA keleti régiója, USA nyugati régiója és Észak-Európa régiókban támogatott.

### <a name="defining-entities-within-the-data-factory"></a>Az adat-előállítóban belüli definiálása
Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva:

* [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
* [HDInsight igény szerinti társított szolgáltatás](#hdinsight-on-demand-linked-service)
* [Azure blobbemeneti adatkészlet](#azure-blob-input-dataset)
* [Azure blobkimeneti adatkészlet](#azure-blob-output-dataset)
* [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Az Azure Storage társított szolgáltatás az Azure tárfiókot társítja az adat-előállítóval. Ebben az oktatóanyagban ugyanabban a tárfiókban lesz az alapértelmezett HDInsight tárfiókot, a bemeneti adatokat tároló és a kimeneti adatok tárolási. Ezért adja meg csak egy Azure Storage társított szolgáltatásnak. A társított szolgáltatás definíciójának neve és kulcsa az Azure storage-fiókot kell megadni. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](../data-factory/connector-azure-blob-storage.md) című szakaszt.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** a storageAccountName és storageAccountKey paramétereket használja. A sablon telepítése során adja meg a paraméterek értékeit.  

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight igény szerinti társított szolgáltatás
Igény szerinti HDInsight kapcsolódó szolgáltatásdefinícióban meg futásidőben egy HDInsight Hadoop-fürt létrehozása a Data Factory szolgáltatásnak által használt konfigurációs paraméterek értékét. A HDInsight igény szerinti társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg a [Számítási társított szolgáltatás](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) című cikket.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Vegye figyelembe a következő szempontokat:

* A Data Factory létrehoz egy **Linux-alapú** meg HDInsight-fürthöz.
* A HDInsight Hadoop-fürt és a tárfiók ugyanabban a régióban jön létre.
* Figyelje meg a *timeToLive* beállítást. A data factory automatikusan törli a fürt, miután a fürt 30 percig inaktív alatt.
* A HDInsight-fürt létrehoz egy **alapértelmezett tárolót** a JSON-fájlban megadott blob-tárolóban (**linkedServiceName**). A fürt törlésekor a HDInsight nem törli ezt a tárolót. Ez a működésmód szándékos. Igény szerinti HDInsight társított szolgáltatás esetén a rendszer mindig létrehoz egy HDInsight-fürt, amikor fel kell dolgozni egy szeletet, kivéve, ha van meglévő élő fürt (**timeToLive**), majd a feldolgozás végén a rendszer törli a fürtöt.

További információkért lásd: [On-demand HDInsight Linked Service](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) (Igény szerinti HDInsight társított szolgáltatás).

> [!IMPORTANT]
> Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: „adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”. Az Azure Blob Storage-tárból olyan eszközökkel törölheti a tárolókat, mint például a [Microsoft Storage Explorer](http://storageexplorer.com/).

#### <a name="azure-blob-input-dataset"></a>Azure blobbemeneti adatkészlet
A bemeneti adatkészlet-definícióban blob tároló mappa és a bemeneti adatokat tartalmazó fájl nevét adja meg. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](../data-factory/connector-azure-blob-storage.md) című szakaszt.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Figyelje meg a JSON-definícióból adott alábbi beállításait:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet
A kimeneti adatkészlet-definícióban és a blob tároló a kimeneti adatokat tartalmazó mappa nevét kell megadni. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](../data-factory/connector-azure-blob-storage.md) című szakaszt.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

A folderPath határozza meg a mappa elérési útját, amely tárolja az adatokat:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

A [adatkészlet rendelkezésre állási](../data-factory/concepts-datasets-linked-services.md) beállítás a következőképpen történik:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

Az Azure Data Factoryben kimeneti adatkészlet rendelkezésre állási meghajtók a folyamatot. Ebben a példában a szelet (EndOfInterval) hónap utolsó napján havonta jön létre. 

#### <a name="data-pipeline"></a>Adatfolyamat
Megadhatja egy folyamatot, amely átalakítja az adatok igény szerinti Azure HDInsight-fürtök a Hive parancsfájl futtatásával. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](../data-factory/concepts-pipelines-activities.md) című szakaszt.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

A folyamat egy tevékenységet, HDInsightHive tevékenységet tartalmaz. Mivel a kezdő és záró dátumát 2016. január adatokat (a szeletek) feldolgozva csak egy hónap. Mindkét *start* és *end* múltbeli dátum, a tevékenység rendelkezik, így a Data Factory az adatokat közvetlenül az adott hónap dolgozza fel. Ha vége a jövőben, az adat-előállítóban létrehoz egy másik szelet Ha idő. További információkért lásd: [Data Factory ütemezés és a végrehajtási](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Az oktatóanyag tartalmának törlése

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>A blob-tárolók hozta létre igény szerinti HDInsight-fürt törlése
Az igény szerinti HDInsight kapcsolódó szolgáltatás használata esetén egy HDInsight-fürt létrehozása minden alkalommal, amikor a szelet kell feldolgozni, kivéve, ha egy meglévő élő fürthöz (élettartam); és a fürt akkor törlődnek, ha a feldolgozás történik. Az egyes fürtökön Azure Data Factory egy blob-tároló az Azure blob storage a fürthöz az alapértelmezett stroage fiókként használt hoz létre. Annak ellenére, hogy a HDInsight-fürtök törlése az alapértelmezett blob tárolókat, és a kapcsolódó tárfiók nem törlődik. Ez a működésmód szándékos. Ahogy egyre több szelet lesz feldolgozva, egyre több tároló jelenik meg az Azure Blob Storage-tárban. Ha nincs szüksége rájuk a feladatokkal kapcsolatos hibaelhárításhoz, törölheti őket a tárolási költségek csökkentése érdekében. A tárolók neve a következő mintát követi: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Törölje a **adfjobs** és **adfyourdatafactoryname-linkedservicename-datetimestamp** mappák. A adfjobs tároló feladatnaplóit az Azure Data Factory tartalmaz.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése
[Az Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) központi telepítése, kezelése és a megoldás csoportként figyelésére szolgál.  Erőforráscsoport törlése összetevőit a csoporton belül.  

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforráscsoportok** a bal oldali ablaktáblán.
3. Kattintson a PowerShell parancsfájl létrehozott az erőforráscsoport neve. A szűrő használja, ha túl sok erőforrás-csoportok szerepel a listában. Az erőforráscsoport egy új panelen nyitja meg.
4. Az a **erőforrások** csempe, akkor az alapértelmezett tárfiók és rendelkezik a data factory, kivéve, ha az erőforráscsoport megosztása más projektek felsorolt.
5. Kattintson a **törlése** a panel tetején. Ezzel törli a tárfiókot és a storage-fiókban tárolt adatok.
6. Adja meg a törlés jóváhagyásához, és kattintson az erőforráscsoport neve **törlése**.

Abban az esetben, ha nem szeretné törölni a tárfiókot, ha törli az erőforráscsoportot, fontolja meg a következő architektúra által az üzleti adatok mappától az alapértelmezett tárfiók. Ebben az esetben egy erőforráscsoportot a tárfiók az üzleti adatok rendelkezik, és az alapértelmezett tárfiókot, a HDInsight tartozó többi erőforráscsoport kapcsolódó szolgáltatás és az adat-előállítóban. A második erőforráscsoport törlése nem érinti az üzleti adatok tárfiók. Ehhez tegye a következőket:

* Adja hozzá a következő a legfelső szintű erőforráscsoporttal együtt a Resource Manager-sablon az Microsoft.DataFactory/datafactories erőforrás. Létrehoz egy tárfiókot:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Vegyen fel egy új társított szolgáltatás pontot az új tárolási fiók:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Konfigurálja a HDInsight kapcsolódó ondemand-szolgáltatás egy további dependsOn és egy additionalLinkedServiceNames:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Következő lépések
Ebben a cikkben rendelkezik megtanulta, hogyan használható az Azure Data Factory igény szerinti HDInsight-fürt feldolgozni a Hive-feladatok létrehozása. Ha többet:

* [Hadoop oktatóanyag: hdinsight Linux-alapú Hadoop használatának megkezdése](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Linux-alapú Hadoop-fürtök létrehozása a Hdinsightban](hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight-dokumentáció](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Data factory dokumentáció](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Függelék:

### <a name="azure-cli-script"></a>Az Azure CLI-parancsfájlt
Az oktatóanyag elvégzéséhez Azure PowerShell használata helyett az Azure parancssori felület is használhatja. Az Azure parancssori felület használatához először telepítenie kell az Azure parancssori felület szerint az alábbi utasításokat:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>A tárterület előkészítése, és másolja a fájlokat az Azure parancssori felület használatával

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

A tároló neve *adfgetstarted*. Legyen ez. Ellenkező esetben frissítenie kell a Resource Manager-sablon. Ha a parancssori felület parancsfájllal segítségre van szüksége, tekintse meg [az Azure parancssori felület használatával az Azure Storage](../storage/common/storage-azure-cli.md).
