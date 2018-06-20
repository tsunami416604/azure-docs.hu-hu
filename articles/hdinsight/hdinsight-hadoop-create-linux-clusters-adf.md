---
title: 'Oktatóanyag: Igény szerinti Hadoop-fürtök létrehozása az Azure HDInsight használatával a Data Factory |} Microsoft Docs'
description: Ismerje meg, igény szerinti Hadoop-fürtök létrehozása a HDInsight az Azure Data Factory használatával.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
ms.openlocfilehash: 9d54d3481176b36a0d13a9b8af2fad03349b81be
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229253"
---
# <a name="tutorial-create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Oktatóanyag: Igény szerinti Hadoop-fürtök létrehozása a Hdinsightban Azure Data Factory használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebből a cikkből megismerheti, hogyan az igény szerinti, az Azure Data Factory használatához Azure HDInsight Hadoop-fürthöz létrehozásához. Ezt követően az adatok adatcsatornákat az Azure Data Factory futtathat Hive-feladatokat, és törölheti a fürtöt. Ez az oktatóanyag végére elsajátíthatja, hogyan azok a big Data típusú adatok feladat futtatása, ahol a fürt létrehozása, a feladat futtatása és a fürt törlését készül ütemezés szerint.

Ez az oktatóanyag a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Azure-tárfiók létrehozása
> * Azure Data Factory tevékenység ismertetése
> * Hozzon létre egy adat-előállító Azure-portál használatával
> * Társított szolgáltatások létrehozása
> * Folyamat létrehozása
> * Indítási folyamat
> * Folyamat monitorozása
> * Kimenet ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure PowerShell. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

* Egy Azure Active Directory szolgáltatás egyszerű. Miután létrehozta a szolgáltatás egyszerű, ügyeljen arra, hogy beolvasni a **Alkalmazásazonosító** és **hitelesítési kulcs** a csatolt cikk utasításai. Az oktatóanyag későbbi részében szüksége ezeket az értékeket. Bizonyosodjon meg arról, hogy az egyszerű szolgáltatás tagja a *közreműködő* szerepkört az előfizetés vagy az erőforráscsoport, amelyben a fürt létrehozását. Lekéri a szükséges értékeket, és a megfelelő szerepköröket útmutatásért lásd: [hozzon létre egy Azure Active Directory szolgáltatás egyszerű](../azure-resource-manager/resource-group-create-service-principal-portal.md).

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Ebben a szakaszban hozzon létre egy tárfiókot hoz létre, igény szerinti HDInsight-fürthöz az alapértelmezett tárolására használandó. Ezt a tárfiókot is HiveQL parancsfájlpéldát tartalmazza (**hivescript.hql**), amelyekkel egy minta Hive-feladatot a fürtön futó szimulálásához.

Ebben a szakaszban az Azure PowerShell-parancsfájl használatával hozzon létre a tárfiók, és másolja a szükséges fájlokat a tárfiókon belül. Az Azure PowerShell-parancsfájlpélda ebben a szakaszban a következő feladatokat hajtja végre:

1. Naplófájlok az Azure-bA.
2. Egy Azure-erőforráscsoportot hoz létre.
3. Létrehoz egy Azure Storage-fiókot.
4. Hozza létre, a tárfiók a Blob-tároló
5. Másolja át a minta HiveQL-parancsfájlt (**hivescript.hql**) a Blob-tároló. A parancsfájl található [ https://hditutorialdata.blob.core.windows.net/adfv2hiveactivity/hivescripts/hivescript.hql ](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql). A parancsfájlpéldát már egy másik nyilvános blobtárolóban található. Az alábbi PowerShell-parancsfájlt hoz létre az Azure Storage figyelembe ezeket a fájlokat másolatot készít.


**Hozzon létre egy tárfiókot, és az Azure PowerShell használatával másolhat fájlokat:**
> [!IMPORTANT]
> Adjon meg nevet az Azure erőforráscsoport és az Azure storage-fiók, a parancsfájl által létrehozott.
> Írja le **erőforráscsoport-név**, **tárfióknév**, és **tárfiók kulcsa** outputted a parancsfájl által. Már szükség a következő szakaszban.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
Login-AzureRmAccount
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

**A storage-fiók létrehozása ellenőrzése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **erőforráscsoportok** a bal oldali ablaktáblán.
3. Kattintson duplán a PowerShell parancsfájl létrehozott az erőforráscsoport neve. A szűrő használja, ha túl sok erőforrás-csoportok szerepel a listában.
4. Az a **erőforrások** csempe, megjelenik egy erőforrást, kivéve, ha az erőforráscsoport megosztása más projektek felsorolt. Adott erőforrás a korábban megadott nevű tárfiók. Válassza ki a tárfiók nevét.
5. Válassza ki a **Blobok** csempék.
6. Válassza ki a **adfgetstarted** tároló. Megjelenik egy nevű mappát **hivescripts**.
7. Nyissa meg a mappát, és ellenőrizze, hogy tartalmaz-e a minta parancsfájlt **hivescript.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Az Azure Data Factory tevékenység ismertetése

[Az Azure Data Factory](../data-factory/introduction.md) koordinálja és automatizálja az adatátviteli és az adatok átalakítása. Az Azure Data Factory hozhat létre egy HDInsight Hadoop fürthöz közvetlenül az időponthoz kötött egy bemeneti adatszelet feldolgozni, és törölheti a fürtöt, ha feldolgozása befejeződött. 

Az Azure Data Factoryben adat-előállító rendelkezhet egy vagy több adat folyamatok. Adatok folyamat rendelkezik egy vagy több tevékenységet. A tevékenységek két típusa van:

* [Adatok mozgása tevékenységek](../data-factory/copy-activity-overview.md) -adatok mozgása tevékenységek segítségével tárolt adatok mozgatása a forrás-tárolóban a cél-tárolóban.
* [Adatok átalakítása tevékenységek](../data-factory/transform-data.md). Adatok átalakítása tevékenységek segítségével átalakítási/folyamat adatokat. HDInsight Hive tevékenység a Data Factory által támogatott átalakítása tevékenységek egyike. Ebben az oktatóanyagban a Hive átalakítási tevékenységet használja.

Ebben a cikkben egy igény szerinti HDInsight Hadoop-fürt létrehozása Hive tevékenység konfigurálása. Ha a tevékenység futtatása adatfeldolgozásra történő, ez történik:

1. HDInsight Hadoop-fürt automatikusan létrejön, közvetlenül az időponthoz kötött a szelet feldolgozása. 

2. A bemeneti adatokat dolgozza fel a fürtön fut egy HiveQL-parancsfájlt. Ebben az oktatóanyagban a hive tevékenységhez társított HiveQL-parancsfájlt az alábbi műveleteket hajtja végre:

    * Használja a meglévő táblázat (*hivesampletable*) egy másik tábla létrehozásához **HiveSampleOut**.
    * Tölti fel a **HiveSampleOut** az eredeti csak adott oszlopokkal rendelkező táblát *hivesampletable*.

3. A HDInsight Hadoop-fürtök törlése a feldolgozás befejezése után, a fürt üresjáratban a beállított időn (timeToLive-beállítást). A timeToLive üresjárati idő feldolgozás a következő adatszelet érhető el, ha ugyanabban a fürtben szolgál a szelet feldolgozása.  

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki az Azure-portálon **hozzon létre egy erőforrást** > **adatok + analitika** > **adat-előállító**.

    ![A portál Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Azure Data Factory a portálon")

2. Adja meg, vagy válassza ki az értékeket, az alábbi képernyőfelvételen látható módon:

    ![Hozzon létre Azure Data Factory Azure-portál használatával](./media/hdinsight-hadoop-create-linux-clusters-adf/create-data-factory-portal.png "hozzon létre Azure Data Factory Azure-portál használatával")

    Adja meg vagy válassza ki a következő értékeket:
    
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Name (Név)** |  Adja meg a data factory nevét. Ez a név globálisan egyedinek kell lennie.|
    |**Előfizetés**     |  Válassza ki az Azure-előfizetését. |
    |**Erőforráscsoport**     | Válassza ki **meglévő** , és válassza ki a PowerShell-parancsfájl használatával létrehozott erőforráscsoportot. |
    |**Verzió**     | Válassza ki **V2 (előzetes verzió)** |
    |**Hely**     | A hely értéke automatikusan az erőforráscsoport korábbi létrehozásakor megadott helyen. A jelen oktatóanyag esetében a hely értéke **USA keleti régiója 2**. |
    

3. Válassza ki **rögzítés az irányítópulton**, majd válassza ki **létrehozása**. A portál irányítópultján egy új csempe jelenik meg **Üzembe helyezés beküldése** címmel. Egy adat-előállító létrehozása eltarthat bárhol 2 – 4 perc között.

    ![Sablon központi telepítésének állapotáról](./media/hdinsight-hadoop-create-linux-clusters-adf/deployment-progress-tile.png "sablon központi telepítésének állapotáról") 
 
4. A data factory létrehozása után a portál az adat-előállító a áttekintése látható.

    ![Az Azure Data Factory áttekintése](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Azure Data Factory – áttekintés")

5. Válassza ki **Szerző & figyelő** elindíthatja az Azure Data Factory jelentéskészítő és -portál figyelését.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ez a szakasz két összekapcsolt szolgáltatások belül a data factory létrehozásához.

- Egy **Azure Storage-beli társított szolgáltatást**, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ezt a tárterületet csak az igény szerinti HDInsight-fürt használja. A Hive-parancsfájl a fürtön futó is tartalmaz.
- Egy **igény szerinti HDInsight társított szolgáltatást**. Az Azure Data Factory automatikusan HDInsight-fürtöt hoz létre, és a Hive-parancsfájl futtatása. Ezt követően pedig törli a HDInsight-fürtöt, miután a fürt egy előre meghatározott ideig tétlen volt.

###  <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. A bal oldali ablaktáblán, a **lássunk** lapon jelölje be a **szerkesztése** ikonra.

    ![Hozzon létre egy Azure Data Factory kapcsolt szolgáltatás](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Azure Data Factory kapcsolt szolgáltatás létrehozása")

2. Válassza ki **kapcsolatok** az ablakot, és válassza ki a bal alsó sarkába a **+ új**.

    ![Kapcsolatok létrehozása az Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "kapcsolatok létrehozása az Azure Data Factory")

3. A a **új társított szolgáltatás** párbeszédpanelen jelölje ki **Azure Blob Storage** majd **Folytatás**.

    ![Hozzon létre Azure Storage társított szolgáltatásnak az adat-előállító](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "létrehozása az Azure Storage társított szolgáltatásnak az adat-előállító")

4. Adja meg a tárolás társított szolgáltatásának nevét, a létrehozott Azure Storage-fiók a PowerShell parancsfájl részeként, majd válassza ki és **Befejezés**.

    ![Adjon meg nevet az Azure Storage társított szolgáltatásnak](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "adjon meg nevet az Azure Storage társított szolgáltatásnak")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Igény szerinti HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét az **+ Új** gombra egy további társított szolgáltatás létrehozásához.

2. Az **Új társított szolgáltatás** ablakban válassza a **Compute** > **Azure HDInsight** lehetőséget, majd kattintson a **Folytatás** gombra.

    ![Create HDInsight társított szolgáltatásnak az Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "létrehozása HDInsight társított szolgáltatásnak az Azure Data Factory")

3. Az a **új társított szolgáltatás** ablakban adja meg a szükséges értékeket.

    ![Adjon meg értékeket a HDInsight társított szolgáltatás](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "adjon meg értékeket a HDInsight társított szolgáltatás")

    Adja meg a következő értékeket, és a többi alapértelmezett maradjon.

    | Tulajdonság | Leírás |
    | --- | --- |
    | Name (Név) | Adjon meg egy nevet a HDInsight társított szolgáltatás |
    | Típus | Válassza ki **igény szerinti HDInsight** |
    | Azure Storage társított szolgáltatás | Válassza ki a korábban létrehozott tárolás társított szolgáltatása. |
    | Fürttípus | Válassza ki **hadoop** |
    | Élettartam | Adja meg a legyen megelőzően elfoglalt automatikusan elérhető legyen a HDInsight-fürt időtartama.|
    | Szolgáltatásnév azonosítója | Adja meg az alkalmazás Azonosítóját, az Azure Active Directory szolgáltatás egyszerű hozza létre, ha az Előfeltételek részeként: |
    | Szolgáltatás elsődleges kulcsa | Adja meg a hitelesítési kulcsát az Azure Active Directory szolgáltatás egyszerű |
    | Fürt nevének előtagja | Adjon meg egy érték, amely fog elé adat-előállító által létrehozott minden fürt típus |
    | Erőforráscsoport | A korábban használt PowerShell-parancsfájl részeként létrehozott erőforráscsoport kiválasztása| 
    | Fürt SSH-felhasználónév | Adjon meg egy SSH-felhasználónév |
    | Fürt SSH-jelszó | Adjon meg egy jelszót az SSH-felhasználó |

    Válassza a **Finish** (Befejezés) elemet.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Válassza a **+** (plusz) gombot, majd a **Folyamat** elemet.

    ![Hozzon létre egy folyamatot az Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "folyamatokat létrehozni az Azure Data Factory")

2. Az a **tevékenységek** eszközkészlet, bontsa ki a **HDInsight**, és húzza a **Hive** tevékenység a csővezeték-Tervező felületére. Az a **általános** lapra, adja meg a tevékenység nevét.

    ![Tevékenységek hozzáadása a Data Factory-folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "tevékenységek hozzáadása a Data Factory-folyamat")

3. Győződjön meg arról, hogy a kiválasztott Hive tevékenység, jelölje be a **HDI-fürtnek** lapon pedig a **HDInsight társított szolgáltatás** legördülő listából válassza ki a korábban létrehozott hdinsight társított szolgáltatást.

    ![A HDInsight fürt részletesen a tölcsér](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "adja meg a HDInsight fürt adatait a következő feldolgozási sor")

4. Válassza ki a **parancsfájl** lapra, és kövesse az alábbi lépéseket:

    a. A **parancsfájl társított szolgáltatás**, jelölje be **HDIStorageLinkedService**. Ez az érték a korábban létrehozott tárolás társított szolgáltatásának.

    b. A **fájl elérési útját**, jelölje be **Tallózás tárolási** , és keresse meg a helyet, ahol a minta Hive parancsfájl érhető el. Ha korábban már futott a PowerShell-parancsfájlt, ezen a helyen kell lennie `adfgetstarted/hivescripts/hivescript.hql`.

    ![Adja meg a Hive parancsprogram részleteinek a tölcsér](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "adja meg a Hive parancsfájl részletei a következő feldolgozási sor")

    c. A **speciális** > **paraméterek**, jelölje be **automatikus kitöltés parancsfájlból**. Ez a beállítás keresi a Hive-parancsfájl a paramétereket, futási időben értékeket igényelnek. A parancsfájl (**hivescript.hql**) rendelkezik egy **kimeneti** paraméter. Adja meg az értéket a formátum `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` úgy, hogy az Azure Storage meg egy létező mappára mutasson. Az elérési út megkülönbözteti a kis- és nagybetűket. Ez az az elérési út a parancsfájl a rendszer hol tárolja.

    ![Adja meg a paramétereket a Hive parancsfájl](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "paraméterek megadása a Hive-parancsfájl")

5. Válassza ki **ellenőrzése** a feldolgozási sor érvényesítéséhez. Az érvényesítési ablak bezárásához kattintson a **>>** (jobbra mutató nyíl) gombra.

    ![Ellenőrizze az Azure Data Factory-folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "ellenőrzése az Azure Data Factory-folyamat")

5. Végül válassza ki **összes** az összetevők közzététele az Azure Data Factory.

    ![Az Azure Data Factory-folyamathoz közzététele](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "közzététele az Azure Data Factory-folyamat")

## <a name="trigger-a-pipeline"></a>Indítási folyamat

1. A Tervező felületére az eszköztáron válassza **eseményindító** > **eseményindító most**.

    ![Indítás, az Azure Data Factory-folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "indul el, az Azure Data Factory-folyamat")

2. Válassza ki **Befejezés** az előugró oldalsó sávon.

## <a name="monitor-a-pipeline"></a>Folyamat monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futása megjelenik a **Pipeline Runs** (Folyamatfuttatások) listában. Figyelje meg a futtatáshoz állapotának a **állapot** oszlop.

    ![Az Azure Data Factory-folyamat figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "az Azure Data Factory-folyamat figyelése")

2. Az állapot frissítéséhez kattintson a **Frissítés** gombra.

3. Igény szerint kiválaszthatja a **nézet tevékenység fut** ikonjára a tevékenységfuttatási társított a folyamatot. Az alábbi képernyőfelvételen látható csak egy tevékenység futtatható, mert az csak egy tevékenység létrehozott folyamatban van. Váltás az előző nézetével, jelölje be **folyamatok** felé a lap tetején.

    ![Az Azure Data Factory-folyamat figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "az Azure Data Factory-folyamat figyelése")


## <a name="verify-the-output"></a>Kimenet ellenőrzése

1. A kimeneti ellenőrzéséhez az Azure portálon lépjen a tárfiókhoz ebben az oktatóanyagban használt. A következő mappák vagy a tárolókat kell megjelennie:

    - Megjelenik egy **adfgerstarted/outputfolder** , amely tartalmazza a Hive parancsfájl, amely a folyamat részeként futott.

    - Megjelenik egy **adfhdidatafactory -\<kapcsolódó szolgáltatás-neve >-\<időbélyeg >** tároló. Ebben a tárolóban az alapértelmezett tárolási helyét a feldolgozási sor Futtatás részeként létrehozott HDInsight-fürthöz.

    - Megjelenik egy **adfjobs** tárolóelemet, amelyhez az Azure Data Factory feladat naplózza.  

        ![Ellenőrizze az Azure Data Factory-folyamat kimeneti](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "az Azure Data Factory-folyamat kimeneti ellenőrzése")


## <a name="clean-up-the-tutorial"></a>Az oktatóanyag tartalmának törlése

Az a a-deman HDInsight-fürt létrehozása, akkor nem kell explicit módon törli a HDInsight-fürthöz. A fürtök törlése a feldolgozási sor létrehozásakor megadott konfigurációján alapul. Azonban a fürtök törlése után is a fürt társított tárfiókok továbbra is létezik. Ez szándékosan van így, hogy a hálózati adaptere esetében megtarthatja az adatok érintetlenül maradnak. Ha nem szeretné megőrizni az adatokat, azonban előfordulhat, hogy a létrehozott tárfiók törlése.

Azt is megteheti törölheti a teljes erőforráscsoport ehhez az oktatóanyaghoz létrehozott. Ezzel törli a tárfiókot és az Azure Data Factory létrehozott.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **erőforráscsoportok** a bal oldali ablaktáblán.
3. Válassza ki a PowerShell parancsfájl létrehozott az erőforráscsoport neve. A szűrő használja, ha túl sok erőforrás-csoportok szerepel a listában. Az erőforráscsoport nyílik meg.
4. Az a **erőforrások** csempe, akkor az alapértelmezett tárfiók és rendelkezik a data factory, kivéve, ha az erőforráscsoport megosztása más projektek felsorolt.
5. Válassza az **Erőforráscsoport törlése** elemet. Ezzel törli a tárfiókot és a storage-fiókban tárolt adatok.

    ![Csoport törléséhez](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "erőforrás csoport törlése")

6. Adja meg a törlés jóváhagyásához, és jelölje ki az erőforráscsoport neve **törlése**.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használható az Azure Data Factory igény szerinti HDInsight-fürt létrehozása, és futtathat Hive-feladatokat. Az útmutató a HDInsight-fürtök egyéni konfiguráció létrehozásához a következő artciel továbblépés.

> [!div class="nextstepaction"]
>[Egyéni konfiguráció Azure HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)


