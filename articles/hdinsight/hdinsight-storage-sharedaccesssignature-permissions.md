---
title: Hozzáférés korlátozása megosztott hozzáférésű aláírások használatával – Azure HDInsight
description: Megtudhatja, hogy miként korlátozhatja a HDInsight-hozzáférést az Azure storage-blobokban tárolt adatokhoz a megosztott hozzáférés-hozzáférés használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: d68f7dc6368c2b3de7f26f2946c5fb47237a820d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313929"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban

A HDInsight teljes hozzáféréssel rendelkezik a fürthöz társított Azure Storage-fiókok adataihoz. A blob tárolómegosztott hozzáférésű aláírások használatával korlátozhatja az adatokhoz való hozzáférést. A megosztott hozzáférésű aláírások (SAS) az Azure storage-fiókok olyan szolgáltatása, amely lehetővé teszi az adatokhoz való hozzáférés korlátozását. Például írásvédett hozzáférés biztosítása az adatokhoz.

> [!IMPORTANT]  
> Az Apache Ranger t használó megoldások esetén fontolja meg a tartományhoz csatlakozott HDInsight használatát. További információt a [Tartományhoz csatlakozott HDInsight-dokumentum konfigurálása](./domain-joined/apache-domain-joined-configure.md) című dokumentumban talál.

> [!WARNING]  
> A HDInsight nak teljes hozzáféréssel kell rendelkeznie a fürt alapértelmezett tárolójához.

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](./hdinsight-hadoop-linux-use-ssh-unix.md)

* Meglévő [tárolótároló](../storage/blobs/storage-quickstart-blobs-portal.md).  

* A PowerShell használata esetén szüksége lesz az [Az modulra.](https://docs.microsoft.com/powershell/azure/overview)

* Ha használni szeretné az Azure CLI-t, és még nem telepítette, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

* [Python](https://www.python.org/downloads/)használata esetén a 2.7-es vagy újabb verzió.

* Ha C#-ot használ, a Visual Studio-nak 2013-as vagy újabb verziónak kell lennie.

* A tárfiók [URI-séma.](./hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez a `wasb://` séma `abfs://` az Azure Storage, az `adl://` Azure Data Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében lenne. Ha a biztonságos átvitel engedélyezve van `wasbs://`az Azure Storage számára, az URI lesz. Lásd még: [biztonságos átvitel](../storage/common/storage-require-secure-transfer.md).

* Meglévő HDInsight-fürt, amelyhez megosztott hozzáférésű aláírást szeretne hozzáadni. Ha nem, az Azure PowerShell segítségével hozhat létre egy fürtöt, és hozzáadhat egy megosztott hozzáférésű aláírást a fürt létrehozása során.

* A példa [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)fájlokat . Ez a tárház a következő elemeket tartalmazza:

  * Visual Studio-projekt, amely tárolótárolót, tárolt házirendet és SAS-t hozhat létre a HDInsight használatához
  * Python-parancsfájl, amely tárolót, tárolt szabályzatot és SAS-t hozhat létre a HDInsight használatához
  * Egy PowerShell-parancsfájl, amely hdinsight-fürtöt hozhat létre, és konfigurálhatja a SAS használatára. A frissített verziót az alábbiakban használjuk.
  * Mintafájl:`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

A közös hozzáférésű aláírásoknak két formája van:

* `Ad hoc`: A SAS kezdési ideje, lejárati ideje és engedélyei mind meg vannak adva a SAS URI-ban.

* `Stored access policy`: A tárolt hozzáférési szabályzat egy erőforrás-tárolón, például egy blob-tárolón van definiálva. Egy házirend egy vagy több megosztott hozzáférési aláírás megkötéseinek kezelésére használható. Amikor egy SAS-t társít egy tárolt hozzáférési szabályzathoz, a SAS örökli a tárolt hozzáférési szabályzathoz meghatározott megkötéseket – a kezdési időt, a lejárati időt és az engedélyeket.

A két űrlap közötti különbség egy kulcsfontosságú forgatókönyv esetében fontos: visszavonás. A SAS egy URL-cím, így bárki, aki beszerzi a SAS-t, használhatja azt. Nem számít, ki kérte. Ha egy SAS-t nyilvánosan közzétesznek, azt a világon bárki használhatja. A kiosztott SAS addig érvényes, amíg a négy dolog valamelyike meg nem történik:

1. A SAS-ban megadott lejárati idő elérése.

2. A SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati idő elérése. A következő esetekben a lejárati idő elérése érdekében:

    * Az időintervallum eltelt.
    * A tárolt hozzáférési szabályzat úgy módosul, hogy a múltban lejárati idő legyen. A lejárati idő módosítása az egyik módja a SAS visszavonásának.

3. A SAS által hivatkozott tárolt hozzáférési szabályzat törlődik, ami egy másik módja a SAS visszavonásának. Ha újra létrehozza a tárolt hozzáférési szabályzatot ugyanazzal a névvel, az előző szabályzat összes SAS-jogkivonata érvényes (ha a SAS lejárati ideje még nem telt el). Ha vissza kívánja vonni a SAS-t, ügyeljen arra, hogy más nevet használjon, ha a hozzáférési szabályzatot a jövőben lejárati idővel hozza létre újra.

4. A SAS létrehozásához használt fiókkulcs újragenerálódik. A kulcs újragenerálása miatt az előző kulcsot használó összes alkalmazás hitelesítése sikertelen lesz. Frissítse az összes összetevőt az új kulcsra.

> [!IMPORTANT]  
> A megosztott hozzáférés-aláírás URI van társítva a fiókkulcs létrehozásához használt aláírás, és a kapcsolódó tárolt hozzáférési szabályzat (ha van ilyen). Ha nincs megadva tárolt hozzáférési házirend, a megosztott hozzáférésű aláírás visszavonásának egyetlen módja a fiókkulcs módosítása.

Azt javasoljuk, hogy mindig a tárolt hozzáférési szabályzatokat használja. Tárolt házirendek használata esetén visszavonhatja az aláírásokat, vagy szükség szerint meghosszabbíthatja a lejárati dátumot. A jelen dokumentum lépései tárolt hozzáférési házirendek használatával generálják a SAS-t.

A megosztott hozzáférésű aláírásokról a [SAS-modell ismertetése című](../storage/common/storage-dotnet-shared-access-signature-part-1.md)témakörben talál további információt.

## <a name="create-a-stored-policy-and-sas"></a>Tárolt házirend és SAS létrehozása

Mentse az egyes módszerek végén előállított SAS-jogkivonatot. A jogkivonat a következő kimenethez hasonlóan fog kinézni:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>A PowerShell használata

Cserélje `RESOURCEGROUP` `STORAGEACCOUNT`le `STORAGECONTAINER` a , és a meglévő tárolótárolómegfelelő értékeivel. Módosítsa a `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` könyvtárat `-File` a paraméterre, `Set-AzStorageblobcontent`vagy módosítsa azt, hogy az tartalmazza a abszolút elérési útját. Írja be a következő PowerShell-parancsot:

```powershell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

A változók ebben a szakaszban a Windows-környezeten alapul. Kisebb eltérések lesz szükség a bash vagy más környezetben.

1. Cserélje `STORAGEACCOUNT`le `STORAGECONTAINER` a megfelelő értékeket a meglévő tárolótárolóhoz.

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. Állítsa a beolvasott elsődleges kulcsot egy változóra későbbi használatra. Cserélje `PRIMARYKEY` le az előző lépésben beolvasott értékre, majd írja be az alábbi parancsot:

    ```console
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Módosítsa a `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` könyvtárat `--file` a paraméterre, `az storage blob upload`vagy módosítsa azt, hogy az tartalmazza a abszolút elérési útját. A fennmaradó parancsok végrehajtása:

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>A Python használata

Nyissa `SASToken.py` meg a `storage_account_name` `storage_account_key`fájlt, `storage_container_name` és cserélje le a , és a megfelelő értékeket a meglévő tároló, majd futtassa a parancsfájlt.

Előfordulhat, hogy `pip install --upgrade azure-storage` végre kell hajtania, ha megjelenik a hibaüzenet. `ImportError: No module named azure.storage`

### <a name="using-c"></a>A C használata\#

1. Nyissa meg a megoldást a Visual Studióban.

2. A Megoldáskezelőben kattintson a jobb gombbal a **SASExample** projektre, és válassza a **Tulajdonságok parancsot.**

3. Válassza a **Beállítások** lehetőséget, és adja hozzá az értékeket a következő bejegyzésekhez:

    |Elem |Leírás |
    |---|---|
    |StorageConnectionString|A tárolási fiók kapcsolati karakterlánca, amelyhez tárolt szabályzatot és SAS-t szeretne létrehozni. A formátumnak `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` `myaccount` ott kell lennie, ahol `mykey` a tárfiók neve, és a tárfiók kulcsa.|
    |ContainerName|A tároló a tárfiókban, amely a hozzáférést korlátozni szeretné.|
    |SASPolicyName|A tárolt házirend létrehozásához használandó név.|
    |Fájlfeltöltés|A tárolóba feltöltött fájl elérési útja.|

4. Futtassa a projektet. Mentse a SAS-házirend-jogkivonatot, a tárfiók nevét és a tároló nevét. Ezek az értékek a tárfiók és a HDInsight-fürt társításához használatosak.

## <a name="use-the-sas-with-hdinsight"></a>A SAS használata a HDInsight segítségével

HDInsight-fürt létrehozásakor meg kell adnia egy elsődleges tárfiókot. További tárfiókokat is megadhat. A tár hozzáadása mindkét módszer teljes hozzáférést igényel a tárfiókokhoz és a használt tárolókhoz.

A tárolók elérésének korlátozásához használjon megosztott hozzáférésű aláírást. Egyéni bejegyzés hozzáadása a fürt **maghely-konfigurációjához.** A bejegyzést hozzáadhatja a fürt létrehozása során a PowerShell használatával vagy a fürt létrehozása után az Ambari használatával.

### <a name="create-a-cluster-that-uses-the-sas"></a>A SAS-t használó fürt létrehozása

Cserélje `CLUSTERNAME` `RESOURCEGROUP`ki `DEFAULTSTORAGEACCOUNT` `STORAGECONTAINER`, `STORAGEACCOUNT`, `TOKEN` , , és a megfelelő értékeket. Adja meg a PowerShell-parancsokat:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
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

# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig

$config = $config | Add-AzHDInsightConfigValue `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> Amikor a rendszer a HTTP/s vagy SSH felhasználónevet és -jelszót kéri, meg kell adnia egy jelszót, amely megfelel az alábbi feltételeknek:
>
> * Legalább 10 karakter hosszúnak kell lennie.
> * Legalább egy számjegyet kell tartalmaznia.
> * Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
> * Legalább egy kis- vagy nagybetűt tartalmaznia kell.

Eltart egy ideig, amíg ez a szkript befejeződik, általában körülbelül 15 perc. Ha a parancsfájl hiba nélkül befejeződik, a fürt létrejött.

### <a name="use-the-sas-with-an-existing-cluster"></a>A SAS használata meglévő fürttel

Ha már rendelkezik fürttel, a **sas-t** hozzáadhatja a központi hely konfigurációjához az alábbi lépésekkel:

1. Nyissa meg a fürt Ambari webes felhasználói felületét. A lap címe `https://YOURCLUSTERNAME.azurehdinsight.net`. Amikor a rendszer kéri, hitelesítse magát a fürtben a fürt létrehozásakor használt rendszergazdai névvel (rendszergazda) és jelszóval.

1. Nyissa meg a **HDFS** > **Configs** > **Advanced** > Custom**core-site webhelyet.**

1. Bontsa ki az **Egyéni maghely** szakaszt, görgessen a végére, és válassza a **Tulajdonság hozzáadása lehetőséget...**. Használja a következő értékeket a **Kulcs** és **érték:**

    * **Kulcs**:`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
    * **Érték**: A korábban végrehajtott metódusok egyike által visszaadott SAS.

    Cserélje `CONTAINERNAME` le a C# vagy SAS alkalmazással használt tárolónévre. Cserélje `STORAGEACCOUNTNAME` le a használt tárfiók nevére.

    A kulcs és az érték mentéséhez válassza a **Hozzáadás** lehetőséget.

1. A **konfigurációs** módosítások mentéséhez kattintson a Mentés gombra. Amikor a rendszer kéri, adja meg a módosítás leírását (például SAS-tároló-hozzáférés hozzáadása), majd válassza a **Mentés gombot.**

    Válassza az **OK gombot,** ha a módosítások befejeződtek.

   > [!IMPORTANT]  
   > A módosítás életbe lépése előtt több szolgáltatást újra kell indítani.

1. Megjelenik **az Újraindítás** legördülő lista. Válassza az **Összes érintett újraindítása lehetőséget** a legördülő listából, majd erősítse meg az Összes __újraindítását.__

    Ismételje meg ezt a folyamatot a **MapReduce2** és **a YARN esetében.**

1. Miután a szolgáltatások újraindultak, jelölje ki mindegyiket, és tiltsa le a karbantartási módot a **Szolgáltatási műveletek** legördülő menüből.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Az alábbi lépésekkel ellenőrizze, hogy csak a SAS-tárfiók elemeit tudja-e olvasni és listázni.

1. Csatlakozzon a fürthöz. Cserélje `CLUSTERNAME` le a fürt nevét, és írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A tároló tartalmának listázásához használja a következő parancsot a parancssorból:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Cserélje `SASCONTAINER` le a SAS-tárfiókhoz létrehozott tároló nevére. Cserélje `SASACCOUNTNAME` le a SAS-hez használt tárfiók nevét.

    A lista tartalmazza a tároló és a SAS létrehozásakor feltöltött fájlt.

3. A következő paranccsal ellenőrizheti, hogy el tudja-e olvasni a fájl tartalmát. Cserélje `SASCONTAINER` ki `SASACCOUNTNAME` a és az előző lépéshez. Cserélje `sample.log` le az előző parancsban megjelenített fájl nevére:

    ```bash
    hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Ez a parancs felsorolja a fájl tartalmát.

4. A fájl helyi fájlrendszerre való letöltéséhez használja a következő parancsot:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Ez a parancs letölti a fájlt egy **testfile.txt**nevű helyi fájlba.

5. A következő paranccsal feltöltheti a helyi fájlt egy **testupload.txt** nevű új fájlba a SAS-tárolón:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    A következő höz hasonló üzenet jelenik meg:

        put: java.io.IOException

    Ez a hiba azért fordul elő, mert a tárolóhely csak olvasás+lista. A következő paranccsal az adatokat a fürt alapértelmezett tárolójára helyezheti, amely írható:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Ez úttal a művelet nek sikeresen kell befejeződnie.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan adhat hozzá korlátozott hozzáférésű tárhelyet a HDInsight-fürthöz, ismerje meg, hogyan dolgozhat a fürtön lévő adatokkal:

* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hadoop/hdinsight-use-mapreduce.md)