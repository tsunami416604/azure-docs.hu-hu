---
title: 'Oktatóanyag: Igény szerinti fürtök az Azure HDInsightban a Data Factory segítségével'
description: Oktatóanyag – Ismerje meg, hogyan hozhat létre igény szerinti Apache Hadoop-fürtöket a HDInsightban az Azure Data Factory használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/18/2020
ms.openlocfilehash: b184a42c52384440445181ac44c616c3139e064f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130689"
---
# <a name="tutorial-create-on-demand-apache-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Oktatóanyag: Igény szerinti Apache Hadoop-fürtök létrehozása a HDInsightban az Azure Data Factory használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre [apache Hadoop-fürtöt](./hadoop/apache-hadoop-introduction.md) igény szerint az Azure HDInsightban az Azure Data Factory használatával. Ezután az Azure Data Factory adatfolyamatait használja a Hive-feladatok futtatásához és a fürt törléséhez. Az oktatóanyag végére megtudhatja, hogyan futtathat egy big data-feladatot, ahol a fürt létrehozása, a feladatfuttatása és a fürt törlése ütemezés szerint történik.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Storage-fiók létrehozása
> * Az Azure Data Factory tevékenységének megismerése
> * Adatgyár létrehozása az Azure Portal használatával
> * Társított szolgáltatások létrehozása
> * Folyamat létrehozása
> * Folyamat aktiválása
> * Folyamat monitorozása
> * Kimenet ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* A PowerShell [Az modul](https://docs.microsoft.com/powershell/azure/overview) telepítve van.

* Egy Azure Active Directory egyszerű szolgáltatás. Miután létrehozta a szolgáltatásnév, győződjön meg róla, hogy lekéri az **alkalmazás azonosítóját** és **a hitelesítési kulcsot** a csatolt cikk utasításait használva. Ezekre az értékekre az oktatóanyag későbbi részében van szüksége. Győződjön meg arról is, hogy az egyszerű szolgáltatás az előfizetés *közreműködői* szerepkörének vagy a fürt létrehozásának a lapjában lévő erőforráscsoport nak a tagja. A szükséges értékek beolvasására és a megfelelő szerepkörök hozzárendelésére vonatkozó útmutatásért olvassa el az [Azure Active Directory egyszerű szolgáltatásának létrehozása című témakört.](../active-directory/develop/howto-create-service-principal-portal.md)

## <a name="create-preliminary-azure-objects"></a>Előzetes Azure-objektumok létrehozása

Ebben a szakaszban különböző objektumokat hoz létre, amelyeket az igény szerint létrehozott HDInsight-fürthöz fog használni. A létrehozott tárfiók tartalmazza a minta `partitionweblogs.hql` [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancsfájlt, amely segítségével szimulálja a minta Apache Hive-feladat, amely fut a fürtön.

Ez a szakasz egy Azure PowerShell-parancsfájlt használ a tárfiók létrehozásához és a tárfiókon belüli szükséges fájlok másolásához. Az Azure PowerShell-mintparancsfájl ebben a szakaszban a következő feladatokat hajtja végre:

1. Bejelentkezik az Azure-ba.
2. Létrehoz egy Azure-erőforráscsoportot.
3. Létrehoz egy Azure Storage-fiókot.
4. Blob-tároló létrehozása a tárfiókban
5. Másolja a minta HiveQL parancsfájl (**partitionweblogs.hql**) a Blob tároló. A parancsfájl elérhető [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)a. A mintaparancsfájl már elérhető egy másik nyilvános Blob-tárolóban. Az alábbi PowerShell-parancsfájl másolatot készít ezekről a fájlokról az általa létrehozott Azure Storage-fiókba.

### <a name="create-storage-account-and-copy-files"></a>Tárfiók létrehozása és fájlok másolása

> [!IMPORTANT]  
> Adja meg az Azure erőforráscsoport és a parancsfájl által létrehozott Azure storage-fiók nevét.
> Írja le **az erőforráscsoport nevét,** **a tárfiók nevét**és a parancsfájl által kimenetelét lekimenetelt **tárfiókkulcsot.** Szükséged van rájuk a következő részben.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfv2hiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $location

New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -Kind StorageV2 `
    -Location $location `
    -SkuName Standard_LRS `
    -EnableHttpsTrafficOnly 1

$destStorageAccountKey = (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous

$destContext = New-AzStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzStorageContainer `
    -Name $destContainerName `
    -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName `
    -Blob "hivescripts\hivescript.hql"

$blobs|Start-AzStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName `
    -DestBlob "hivescripts\partitionweblogs.hql"

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzStorageBlob `
    -Context $destContext `
    -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

### <a name="verify-storage-account"></a>Tárfiók ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Bal oldalon keresse meg az **Összes szolgáltatás** > **általános** > **erőforráscsoportját.**
1. Válassza ki a PowerShell-parancsfájlban létrehozott erőforráscsoport nevét. Használja a szűrőt, ha túl sok erőforráscsoport szerepel a listában.
1. Az **Áttekintés** nézetben egy erőforrás jelenik meg a listában, hacsak nem osztja meg az erőforráscsoportot más projektekkel. Ez az erőforrás a korábban megadott névvel rendelkező tárfiók. Válassza ki a tárfiók nevét.
1. Válassza a **Tárolók csempét.**
1. Válassza ki az **adfgetstarted** tárolót. Megjelenik egy **hivescripts**nevű mappa.
1. Nyissa meg a mappát, és győződjön meg róla, hogy tartalmazza a minta script fájlt, **partitionweblogs.hql**.

## <a name="understand-the-azure-data-factory-activity"></a>Ismerje meg az Azure Data Factory tevékenységét

[Az Azure Data Factory](../data-factory/introduction.md) vezényli és automatizálja az adatok mozgását és átalakítását. Az Azure Data Factory létrehozhat egy HDInsight Hadoop-fürtöt, amely éppen időben dolgozfel egy bemeneti adatszeletet, és törölheti a fürtöt, amikor a feldolgozás befejeződött.

Az Azure Data Factoryban egy adat-előállító egy vagy több adatfolyamattal rendelkezhet. Az adatfolyamat egy vagy több tevékenységet is rendelkezik. Kétféle tevékenység létezik:

* [Adatmozgatási tevékenységek](../data-factory/copy-activity-overview.md). Az adatmozgatási tevékenységek segítségével adatokat helyezhet át a forrásadattárból a céladattárba.
* [Adatátalakítási tevékenységek](../data-factory/transform-data.md). Az adatátalakítási tevékenységek segítségével alakítja át/dolgozza fel az adatokat. A HDInsight Hive-tevékenység a Data Factory által támogatott átalakítási tevékenységek egyike. A Hive átalakítási tevékenység ebben az oktatóanyagban.

Ebben a cikkben konfigurálja a Hive-tevékenység et egy igény szerinti HDInsight Hadoop-fürt létrehozásához. Amikor a tevékenység adatok feldolgozására fut, a következők történnek:

1. A HDInsight Hadoop-fürt automatikusan létrejön a szelet feldolgozásához.

2. A bemeneti adatok feldolgozása egy HiveQL parancsfájl futtatásával történik a fürtön. Ebben az oktatóanyagban a hive-tevékenységhez társított HiveQL-parancsfájl a következő műveleteket hajtja végre:

    * A meglévő tábla (*hivesampletable*) segítségével hozzon létre egy másik táblát **HiveSampleOut**.
    * Feltölti a **HiveSampleOut** táblát, amely csak az eredeti *hivesampletable*adott oszlopaival jelenik meg.

3. A HDInsight Hadoop-fürt a feldolgozás befejezése után törlődik, és a fürt a beállított ideig (timeToLive beállítás) tétlen. Ha a következő adatszelet jelenleg használhatóAz Élő tétlen idő vel történő feldolgozáshoz, akkor ugyanaz ta-fürt lesz a szelet feldolgozásához.  

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. A bal oldali menüben keresse meg a **+ Erőforrás-elemzési** > **Analytics** > **adatgyár**közötti navigálást.

    ![Az Azure Data Factory a portálon](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-azure-portal.png "Az Azure Data Factory a portálon")

3. Adja meg vagy jelölje ki az **Új adatgyár** csempe következő értékeit:

    |Tulajdonság  |Érték  |
    |---------|---------|
    |Név | Adja meg az adat-előállító nevét. A névnek globálisan egyedinek kell lennie.|
    |Verzió | Hagyjuk a **V2-nél.** |
    |Előfizetés | Válassza ki az Azure-előfizetését. |
    |Erőforráscsoport | Válassza ki a PowerShell-parancsfájl használatával létrehozott erőforráscsoportot. |
    |Hely | A hely automatikusan a megadott helyre lesz beállítva az erőforráscsoport korábbi létrehozásakor. Ebben az oktatóanyagban a hely **az USA keleti részére**van állítva. |
    |GIT engedélyezése|Törölje a jelet a jelölőnégyzetből.|

    ![Az Azure Data Factory létrehozása az Azure Portal használatával](./media/hdinsight-hadoop-create-linux-clusters-adf/azure-portal-create-data-factory.png "Az Azure Data Factory létrehozása az Azure Portal használatával")

4. Kattintson a **Létrehozás** gombra. Az adat-előállító létrehozása 2–4 percet is igénybe vehet.

5. Az adat-előállító létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás **az erőforrásra** gombbal.  Válassza **az Ugrás az erőforrásra** lehetőséget a Data Factory alapértelmezett nézetének megnyitásához.

6. Válassza **a Szerzői & figyelő** lehetőséget az Azure Data Factory szerzői és figyelési portál elindításához.

    ![Az Azure Data Factory portál áttekintése](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-portal-overview.png "Az Azure Data Factory áttekintése")

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Ebben a szakaszban két összekapcsolt szolgáltatást hoz össze az adat-előállítón belül.

* Egy **Azure Storage-beli társított szolgáltatást**, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ezt a tárterületet csak az igény szerinti HDInsight-fürt használja. A fürtön futtatott Hive-parancsfájlt is tartalmazza.
* Igény **szerinti HDInsight-kapcsolt szolgáltatás.** Az Azure Data Factory automatikusan létrehoz egy HDInsight-fürtöt, és futtatja a Hive-parancsfájlt. Ezt követően pedig törli a HDInsight-fürtöt, miután a fürt egy előre meghatározott ideig tétlen volt.

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. A **Let's get's started** lap bal oldali ablaktáblájában válassza a **Szerző** ikont.

    ![Azure Data Factory-kapcsolattal összekapcsolt szolgáltatás létrehozása](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-edit-tab.png "Azure Data Factory-kapcsolattal összekapcsolt szolgáltatás létrehozása")

2. Válassza a **Kapcsolatok** lehetőséget az ablak bal alsó sarkában, majd a **+Új**lehetőséget.

    ![Kapcsolatok létrehozása az Azure Data Factoryban](./media/hdinsight-hadoop-create-linux-clusters-adf/data-factory-create-new-connection.png "Kapcsolatok létrehozása az Azure Data Factoryban")

3. Az **Új csatolt szolgáltatás** párbeszédpanelen válassza az **Azure Blob Storage** lehetőséget, majd kattintson a Folytatás **gombra.**

    ![Azure Storage-alapú szolgáltatás létrehozása adatgyárhoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service.png "Azure Storage-alapú szolgáltatás létrehozása adatgyárhoz")

4. Adja meg a következő értékeket a tárolóhoz csatolt szolgáltatáshoz:

    |Tulajdonság |Érték |
    |---|---|
    |Név |Írja be a `HDIStorageLinkedService` (igen) kifejezést.|
    |Azure-előfizetés |Válassza ki az előfizetést a legördülő listából.|
    |Storage account name (Tárfiók neve) |Válassza ki a PowerShell-parancsfájl részeként létrehozott Azure Storage-fiók.|

    Válassza **a Kapcsolat tesztelése** lehetőséget, majd ha sikeres, válassza a **Létrehozás lehetőséget.**

    ![Az Azure Storage-hoz csatolt szolgáltatás nevének megadására](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-storage-linked-service-details.png "Az Azure Storage-hoz csatolt szolgáltatás nevének megadására")

### <a name="create-an-on-demand-hdinsight-linked-service"></a>Igény szerinti HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét az **+ Új** gombra egy további társított szolgáltatás létrehozásához.

2. Az **Új csatolt szolgáltatás** ablakban válassza a **Számítás** lapot.

3. Válassza az **Azure HDInsight**lehetőséget, majd a **Folytatás**lehetőséget.

    ![HDInsight-csatolt szolgáltatás létrehozása az Azure Data Factory számára](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service.png "HDInsight-csatolt szolgáltatás létrehozása az Azure Data Factory számára")

4. Az **Új csatolt szolgáltatás** ablakban adja meg a következő értékeket, a többit pedig hagyja alapértelmezettként:

    | Tulajdonság | Érték |
    | --- | --- |
    | Név | Írja be a `HDInsightLinkedService` (igen) kifejezést.|
    | Típus | Válassza **az Igény szerinti HDInsight lehetőséget.** |
    | Azure Storage társított szolgáltatás | Válassza a(z) `HDIStorageLinkedService` lehetőséget. |
    | Fürt típusa | **Hadoop** kiválasztása |
    | Élettartam | Adja meg azt az időtartamot, amelynek a HDInsight-fürt elérhetővé válik az automatikus törlés előtt.|
    | Egyszerű szolgáltatás azonosítója | Adja meg az azure Active Directory egyszerű szolgáltatás az előfeltételek részeként létrehozott alkalmazásazonosítóját. |
    | Egyszerű szolgáltatáskulcs | Adja meg az Azure Active Directory egyszerű szolgáltatáshitelesítési kulcsát. |
    | Fürtnév előtagja | Adjon meg egy értéket, amely elő lesz rögzítve az adat-előállító által létrehozott összes fürttípushoz. |
    |Előfizetés |Válassza ki az előfizetést a legördülő listából.|
    | Erőforráscsoport kiválasztása | Válassza ki a korábban használt PowerShell-parancsfájl részeként létrehozott erőforráscsoportot.|
    | Operációs rendszer típusa/Fürt SSH-felhasználóneve | Gyakran adjon meg egy `sshuser`SSH-felhasználónevet. |
    | Operációs rendszer típusa/Fürt SSH-jelszava | Jelszó megadása az SSH-felhasználó számára |
    | Operációs rendszer típusa/fürt felhasználóneve | Gyakran adja meg a `admin`fürt felhasználónevét. |
    | Operációs rendszer típusa/fürtjel | Adjon meg jelszót a fürtfelhasználószámára. |

    Ezután válassza **a Létrehozás lehetőséget.**

    ![Értékek biztosítása a HDInsight-csatolt szolgáltatáshoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-linked-service-details.png "Értékek biztosítása a HDInsight-csatolt szolgáltatáshoz")

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Válassza **+** a (plusz) gombot, majd a **Folyamat**lehetőséget.

    ![Folyamat létrehozása az Azure Data Factoryban](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-create-pipeline.png "Folyamat létrehozása az Azure Data Factoryban")

1. A **Tevékenységek** eszközkészletben bontsa ki a **HDInsight**csomópontot, és húzza a **Hive-tevékenységet** a folyamattervező felületére. Az **Általános** lapon adja meg a tevékenység nevét.

    ![Tevékenységek hozzáadása a Data Factory folyamathoz](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-add-hive-pipeline.png "Tevékenységek hozzáadása a Data Factory folyamathoz")

1. Győződjön meg arról, hogy a Hive-tevékenység ki van jelölve, válassza ki a **HDI-fürt** lapot, és a **HDInsight csatolt szolgáltatás** legördülő listájában válassza ki a korábban létrehozott csatolt szolgáltatást, **a HDInsight-szolgáltatást**a HDInsight számára.

    ![A folyamat HDInsight-fürtjének részleteinek biztosítása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-hive-activity-select-hdinsight-linked-service.png "A folyamat HDInsight-fürtjének részleteinek biztosítása")

1. Válassza a **Parancsfájl** lapot, és hajtsa végre a következő lépéseket:

    1. Parancsfájlalapú **szolgáltatás**esetén válassza a **HDIStorageLinkedService** elemet a legördülő listából. Ez az érték a korábban létrehozott, tárolóhoz csatolt szolgáltatás.

    1. A **Fájlelérési út**esetében válassza a **Tároló tallózása** lehetőséget, és keresse meg azt a helyet, ahol a minta Hive-parancsfájl elérhető. Ha korábban futtatta a PowerShell-parancsfájlt, ennek a helynek kell lennie. `adfgetstarted/hivescripts/partitionweblogs.hql`

        ![A folyamat Hive-parancsfájl részleteinek biztosítása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-path.png "A folyamat Hive-parancsfájl részleteinek biztosítása")

    1. A Speciális > **paraméterek** **csoportban**válassza az **Automatikus kitöltés parancsfájlból**lehetőséget. Ez a beállítás a Hive-parancsfájlban olyan paramétereket keres, amelyek futásidőben értékeket igényelnek.

    1. Az **érték** mezőbe írja be a meglévő `wasbs://adfgetstarted@<StorageAccount>.blob.core.windows.net/outputfolder/`mappát a formátumba. Az elérési út megkülönbözteti a kis- és nagybetűket. Ez az az elérési út, ahol a parancsfájl kimenete tárolódik. A `wasbs` séma azért szükséges, mert a tárfiókok most már a biztonságos átvitel alapértelmezés szerint engedélyezve van.

        ![A Hive-parancsfájl paramétereinek megadása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-provide-script-parameters.png "A Hive-parancsfájl paramétereinek megadása")

1. Válassza **az Érvényesítés** lehetőséget a folyamat érvényesítéséhez. Az **>>** érvényesítési ablak bezárásához kattintson a (jobbra nyíl) gombra.

    ![Az Azure Data Factory-folyamat ellenőrzése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-validate-all.png "Az Azure Data Factory-folyamat ellenőrzése")

1. Végül válassza **az összes közzététele** az összetevők az Azure Data Factory közzétételéhez.

    ![Az Azure Data Factory-folyamat közzététele](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-publish-pipeline.png "Az Azure Data Factory-folyamat közzététele")

## <a name="trigger-a-pipeline"></a>Folyamat aktiválása

1. A tervezőfelület eszköztárán válassza az **Eseményindító** > hozzáadása**most**lehetőséget.

    ![Az Azure Data Factory folyamatának aktiválása](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-trigger-pipeline.png "Az Azure Data Factory folyamatának aktiválása")

2. Az előugró ablak sávjában válassza az **OK gombot.**

## <a name="monitor-a-pipeline"></a>Folyamat monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Ekkor a folyamat futása megjelenik a **Pipeline Runs** (Folyamatfuttatások) listában. Figyelje meg a futtatás állapotát az **Állapot** oszlopban.

    ![Az Azure Data Factory-folyamat figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline.png "Az Azure Data Factory-folyamat figyelése")

1. Az állapot frissítéséhez kattintson a **Frissítés** gombra.

1. A **Tevékenység futtatásak megtekintése** ikont is kiválaszthatja a folyamathoz társított tevékenységfuttatás megtekintéséhez. Az alábbi képernyőképen csak egy tevékenységfuttatáslátható, mivel csak egy tevékenység van a létrehozott folyamatban. Az előző nézetre való visszaváltáshoz válassza a **Folyamatok** elemet a lap tetejének felé.

    ![Az Azure Data Factory folyamattevékenységének figyelése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-monitor-pipeline-activity.png "Az Azure Data Factory folyamattevékenységének figyelése")

## <a name="verify-the-output"></a>Kimenet ellenőrzése

1. A kimenet ellenőrzéséhez az Azure Portalon keresse meg az oktatóanyaghoz használt tárfiókot. A következő mappákat vagy tárolókat kell látnia:

    * Megjelenik egy **adfgerstarted/outputfolder,** amely a folyamat részeként futtatott Hive-parancsfájl kimenetét tartalmazza.

    * Megjelenik egy **adfhdidatafactory-\<csatolt szolgáltatás-név>-\<időbélyeg>** tároló. Ez a tároló a folyamatfuttatása részeként létrehozott HDInsight-fürt alapértelmezett tárolási helye.

    * Megjelenik egy **adfjobs** tároló, amely rendelkezik az Azure Data Factory feladat naplók.  

        ![Az Azure Data Factory folyamatkimenetének ellenőrzése](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-data-factory-verify-output.png "Az Azure Data Factory folyamatkimenetének ellenőrzése")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az igény szerinti HDInsight-fürt létrehozása esetén nem kell explicit módon törölnie a HDInsight-fürtöt. A fürt törlődik a folyamat létrehozásakor megadott konfiguráció alapján. Azonban még a fürt törlése után is léteznek a fürthöz társított tárfiókok. Ez a viselkedés szándékosan, hogy az adatok érintetlenek maradhassanak. Ha azonban nem szeretné megtartani az adatokat, törölheti a létrehozott tárfiókot.

Azt is megteheti, hogy törli az oktatóanyaghoz létrehozott teljes erőforráscsoportot. Ez törli a tárfiókot és az Azure Data Factory, amely a létrehozott.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **az Erőforráscsoportok lehetőséget** a bal oldali ablaktáblán.
1. Válassza ki a PowerShell-parancsfájlban létrehozott erőforráscsoport nevét. Használja a szűrőt, ha túl sok erőforráscsoport szerepel a listában. Megnyitja az erőforráscsoportot.
1. Az **Erőforrások** csempén az alapértelmezett tárfiókkal és az adat-előállítóval kell rendelkeznie, hacsak nem osztja meg az erőforráscsoportot más projektekkel.
1. Válassza az **Erőforráscsoport törlése** elemet. Ezzel törli a tárfiókot és a tárfiókban tárolt adatokat.

    ![Az Azure Portal erőforráscsoport törlése](./media/hdinsight-hadoop-create-linux-clusters-adf/delete-resource-group.png "Erőforráscsoport törlése")

1. A törlés megerősítéséhez adja meg az erőforráscsoport nevét, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja az Azure Data Factory t igény szerinti HDInsight-fürt létrehozásához és [az Apache Hive-feladatok](https://hive.apache.org/) futtatásához. A következő cikkből megtudhatja, hogyan hozhat létre HDInsight-fürtöket egyéni konfigurációval.

> [!div class="nextstepaction"]
> [Azure HDInsight-fürtök létrehozása egyéni konfigurációval](hdinsight-hadoop-provision-linux-clusters.md)
