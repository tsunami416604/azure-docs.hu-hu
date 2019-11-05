---
title: Hozzáférés korlátozása közös hozzáférési aláírások használatával – Azure HDInsight
description: Megtudhatja, hogyan használhatja a közös hozzáférési aláírásokat az Azure Storage-blobokban tárolt HDInsight való hozzáférés korlátozására.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 031498119eb4f9feb92046d7d7a86cfd77f8f368
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498128"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Az Azure Storage közös hozzáférésű aláírásainak használata a HDInsight tárolt adathozzáférések korlátozására

A HDInsight teljes hozzáféréssel rendelkezik a fürthöz társított Azure Storage-fiókokban lévő összes adathoz. A blob-tárolón a közös hozzáférési aláírások használatával korlátozhatja az adathozzáférést. A közös hozzáférésű aláírások (SAS) az Azure Storage-fiókok egyik funkciója, amely lehetővé teszi az adathozzáférés korlátozását. Például csak olvasási hozzáférést biztosít az adateléréshez.

> [!IMPORTANT]  
> Az Apache Rangert használó megoldások esetében érdemes lehet tartományhoz csatlakoztatott HDInsight használni. További információ: [tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-configure.md) -dokumentum konfigurálása.

> [!WARNING]  
> A HDInsight teljes hozzáféréssel kell rendelkeznie a fürt alapértelmezett tárolóhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy meglévő [Storage-tároló](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Ha a PowerShellt használja, szüksége lesz az az [modulra](https://docs.microsoft.com/powershell/azure/overview).

* Ha az Azure CLI-t szeretné használni, és még nem telepítette, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

* [Python](https://www.python.org/downloads/), 2,7 vagy újabb verzió használata esetén.

* A használata C#esetén a Visual Studio 2013-es vagy újabb verziójának kell lennie.

* A Storage-fiók [URI-sémája](./hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez `wasb://` az Azure Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1 számára. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI `wasbs://`lesz. Lásd még: [biztonságos átvitel](../storage/common/storage-require-secure-transfer.md).

* Egy meglévő HDInsight-fürt megosztott hozzáférési aláírás hozzáadásához a következőhöz:. Ha nem, akkor a Azure PowerShell használatával létrehozhat egy fürtöt, és hozzáadhat egy közös hozzáférési aláírást a fürt létrehozása során.

* A példában szereplő fájlok [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ez a tárház a következő elemeket tartalmazza:

  * Egy Visual Studio-projekt, amely létrehoz egy Storage-tárolót, tárolt házirendet és SAS-t a HDInsight-mel való használatra
  * Egy Python-szkript, amely létrehoz egy Storage-tárolót, tárolt házirendet és SAS-t a HDInsight-hez való használatra
  * Egy PowerShell-parancsfájl, amely létrehozhat egy HDInsight-fürtöt, és konfigurálhatja az SAS használatára. A frissített verziók alább láthatók.
  * Egy minta fájl: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

A közös hozzáférésű aláírások két formája létezik:

* Ad hoc: az SAS URI-ja a kezdési időt, a lejárati időt és a SAS-engedélyeket is megadja.

* Tárolt hozzáférési szabályzat: egy tárolt hozzáférési házirend van definiálva egy erőforrás-tárolón, például egy blob-tárolón. Egy házirend használható egy vagy több megosztott hozzáférési aláírás korlátozásának kezelésére. Ha a SAS-t egy tárolt hozzáférési házirenddel társítja, az SAS örökli a megkötéseket – a kezdési időt, a lejárati időt és az engedélyeket a tárolt hozzáférési házirendhez.

A két űrlap közötti különbség fontos az egyik kulcsfontosságú forgatókönyv esetén: visszavonás. Az SAS egy URL-cím, így bárki, aki beolvassa az SAS-t, használhatja azt, függetlenül attól, hogy ki kérte a kezdését. Ha egy SAS közzé van téve nyilvánosan, a világ bármely tagja használhatja. Egy elosztott SAS-alkalmazás a négy dolog egyike esetén érvényes:

1. A rendszer elérte az SAS-ben megadott lejárati időt.

2. A rendszer elérte az SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati időt. A lejárati időt a következő esetekben lehet elérni:

    * Az időtartam eltelt.
    * A tárolt hozzáférési szabályzat úgy módosul, hogy a múltban lejárjon a lejárati idő. A lejárati idő módosítása az SAS visszavonásának egyik módja.

3. A SAS által hivatkozott tárolt hozzáférési szabályzat törölve lett, ami egy másik módszer az SAS visszavonására. Ha a tárolt hozzáférési szabályzatot ugyanazzal a névvel hozza létre, az előző szabályzathoz tartozó SAS-jogkivonatok érvényesek (ha az SAS lejárati ideje nem lett átadva). Ha vissza kívánja vonni az SAS-t, ne felejtsen el másik nevet használni, ha később újra létrehozza a hozzáférési szabályzatot.

4. Az SAS létrehozásához használt fiók kulcsa újra lett létrehozva. A kulcs újragenerálása azt eredményezi, hogy az előző kulcsot használó alkalmazások nem tudják hitelesíteni a hitelesítést. Frissítse az összes összetevőt az új kulcsra.

> [!IMPORTANT]  
> A közös hozzáférésű aláírás URI-ja társítva van az aláírás létrehozásához használt fiók kulcsával és a hozzá tartozó tárolt hozzáférési szabályzattal (ha van ilyen). Ha nincs megadva tárolt hozzáférési házirend, a közös hozzáférésű aláírás visszavonásának egyetlen módja a fiók kulcsának módosítása.

Javasoljuk, hogy mindig használjon tárolt hozzáférési házirendeket. Tárolt házirendek használatakor visszavonhatja az aláírásokat, vagy igény szerint kiterjesztheti a lejárati dátumot. A jelen dokumentumban szereplő lépések a tárolt hozzáférési szabályzatokat használják a SAS létrehozásához.

A közös hozzáférésű aláírásokkal kapcsolatos további információkért lásd [az SAS-modell megismerése](../storage/common/storage-dotnet-shared-access-signature-part-1.md)című témakört.

## <a name="create-a-stored-policy-and-sas"></a>Tárolt szabályzat és SAS létrehozása

Mentse az egyes módszerek végén létrehozott SAS-jogkivonatot. A jogkivonat a következőhöz hasonlóan fog kinézni:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>A PowerShell használata

Cserélje le a `RESOURCEGROUP`, `STORAGEACCOUNT`és `STORAGECONTAINER` értéket a meglévő tároló megfelelő értékeivel. Módosítsa a könyvtárat úgy, hogy `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa a `-File` paramétert, hogy tartalmazza a `Set-AzStorageblobcontent`abszolút elérési útját. Adja meg a következő PowerShell-parancsot:

```PowerShell
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

Az ebben a szakaszban szereplő változók használata egy Windows-környezetben alapul. A bash vagy más környezetek esetében kisebb eltérésekre lesz szükség.

1. Cserélje le a `STORAGEACCOUNT`t, és `STORAGECONTAINER` a meglévő Storage-tároló megfelelő értékeivel.

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

2. A beolvasott elsődleges kulcs beállítása egy változóra későbbi használatra. Cserélje le a `PRIMARYKEY`t az előző lépésben lekért értékre, majd írja be az alábbi parancsot:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Módosítsa a könyvtárat úgy, hogy `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa a `--file` paramétert, hogy tartalmazza a `az storage blob upload`abszolút elérési útját. Hajtsa végre a többi parancsot:

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

Nyissa meg a `SASToken.py` fájlt, és cserélje le a `storage_account_name`, `storage_account_key`és `storage_container_name` értéket a meglévő tároló megfelelő értékeire, majd futtassa a parancsfájlt.

Előfordulhat, hogy végre kell hajtania `pip install --upgrade azure-storage`, ha `ImportError: No module named azure.storage`hibaüzenetet kap.

### <a name="using-c"></a>A C# használata

1. Nyissa meg a megoldást a Visual Studióban.

2. Megoldáskezelő kattintson a jobb gombbal a **SASExample** projektre, és válassza a **Tulajdonságok**lehetőséget.

3. Válassza a **Beállítások** lehetőséget, és adjon hozzá értékeket a következő bejegyzésekhez:

   * StorageConnectionString: a Storage-fiókhoz tartozó, tárolt házirendet és SAS-t létrehozni kívánó kapcsolódási karakterlánc. A formátumnak `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` kell lennie, ahol a `myaccount` a Storage-fiók neve, a `mykey` pedig a Storage-fiók kulcsa.

   * ContainerName: a Storage-fiók azon tárolója, amelyhez korlátozni kívánja a hozzáférést.

   * SASPolicyName: az a név, amelyet a tárolt házirend létrehozásához használni kell.

   * FileToUpload: a tárolóba feltöltött fájl elérési útja.

4. Futtassa a projektet. Mentse a SAS-szabályzat tokenjét, a Storage-fiók nevét és a tároló nevét. Ezeket az értékeket használja a rendszer a HDInsight-fürthöz tartozó Storage-fiók társításához.

## <a name="use-the-sas-with-hdinsight"></a>Az SAS használata a HDInsight

HDInsight-fürt létrehozásakor meg kell adnia egy elsődleges Storage-fiókot, és opcionálisan további tárolási fiókokat is megadhat. A tárterület hozzáadásának mindkét módszere teljes hozzáférést igényel a használt Storage-fiókokhoz és tárolóhoz.

Ha közös hozzáférésű aláírást szeretne használni a tárolóhoz való hozzáférés korlátozásához, adjon hozzá egy egyéni bejegyzést a fürt **alapvető hely** konfigurációjához. A bejegyzést felveheti a fürt létrehozása során a PowerShell használatával, vagy a fürt létrehozása után a Ambari használatával.

### <a name="create-a-cluster-that-uses-the-sas"></a>SAS-t használó fürt létrehozása

Cserélje le a `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`és `TOKEN` értékeket a megfelelő értékekre. Adja meg a PowerShell-parancsokat:

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

$config = $config | Add-AzHDInsightConfigValues `
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
> Ha a rendszer kéri a HTTP/s vagy SSH-Felhasználónév és-jelszó megadását, meg kell adnia egy jelszót, amely megfelel a következő feltételeknek:
>
> * Legalább 10 karakter hosszúnak kell lennie.
> * Legalább egy számjegyet tartalmaznia kell.
> * Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
> * Legalább egy kis-és nagybetűt tartalmaznia kell.

Eltarthat egy ideig, amíg ez a szkript befejeződik, általában körülbelül 15 percet vesz igénybe. Ha a parancsfájl hibák nélkül fejeződik be, a fürt létrejött.

### <a name="use-the-sas-with-an-existing-cluster"></a>Az SAS használata meglévő fürttel

Ha meglévő fürttel rendelkezik, a következő lépésekkel adhatja hozzá az SAS **-t a Core-site** konfigurációhoz:

1. Nyissa meg a Ambari webes felhasználói felületét a fürthöz. A lap címe `https://YOURCLUSTERNAME.azurehdinsight.net`. Ha a rendszer kéri, végezzen hitelesítést a fürtön a fürt létrehozásakor használt rendszergazdai név (rendszergazda) és jelszó használatával.

2. A Ambari webes felhasználói felületének bal oldalán válassza a **HDFS** elemet, majd kattintson a lap közepén található **konfigurációk** fülre.

3. Válassza a **speciális** fület, majd görgessen addig, amíg meg nem találja az **Egyéni Core-site** szakaszt.

4. Bontsa ki az **Egyéni Core-site** szakaszt, majd görgessen a végére, és válassza a **tulajdonság hozzáadása...** hivatkozást. Használja a következő értékeket a **kulcs** és **érték** mezőkhöz:

   * **Kulcs**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Érték**: a korábban végrehajtott metódusok egyike által visszaadott sas.

     Cserélje le a `CONTAINERNAME`t a C# vagy sas-alkalmazással használt tároló nevére. Cserélje le a `STORAGEACCOUNTNAME`t a használt Storage-fiók nevére.

5. Kattintson a **Hozzáadás** gombra a kulcs és az érték mentéséhez, majd kattintson a **Save (Mentés** ) gombra a konfigurációs módosítások mentéséhez. Ha a rendszer kéri, adja meg a módosítás leírását (például "SAS-tároló-hozzáférés hozzáadása"), majd kattintson a **Mentés**gombra.

    A módosítások befejeződése után kattintson **az OK gombra** .

   > [!IMPORTANT]  
   > A módosítás érvénybe léptetéséhez több szolgáltatást is újra kell indítania.

6. A Ambari webes FELÜLETén válassza a bal oldali listából a **HDFS** elemet, majd kattintson a jobb oldalon található **szolgáltatási műveletek** legördülő listából az **összes érintett újraindítása** elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__lehetőséget.

    Ismételje meg ezt a folyamatot a MapReduce2 és a fonal esetében.

7. A szolgáltatások újraindítása után válassza ki mindegyiket, és tiltsa le a karbantartási módot a **szolgáltatási műveletek** legördülő listából.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Az alábbi lépések végrehajtásával ellenőrizheti, hogy csak az SAS-Storage-fiókban lévő elemeket tudja-e olvasni és listázni.

1. Kapcsolódjon a fürthöz. Cserélje le a `CLUSTERNAME`t a fürt nevére, és írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A tároló tartalmának listázásához használja a következő parancsot a parancssorból:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Cserélje le a `SASCONTAINER`t az SAS Storage-fiókhoz létrehozott tároló nevére. Cserélje le a `SASACCOUNTNAME`t a SAS-hez használt Storage-fiók nevére.

    A lista tartalmazza a tároló és SAS létrehozásakor feltöltött fájlt.

3. A következő parancs használatával ellenőrizheti, hogy el tudja-e olvasni a fájl tartalmát. Cserélje le a `SASCONTAINER`t, és `SASACCOUNTNAME` az előző lépésben. Cserélje le a `sample.log`t az előző parancsban megjelenő fájl nevére:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Ez a parancs felsorolja a fájl tartalmát.

4. A következő parancs használatával töltse le a fájlt a helyi fájlrendszerbe:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Ez a parancs letölti a fájlt egy **TESTFILE. txt**nevű helyi fájlba.

5. A következő parancs használatával töltse fel a helyi fájlt egy **testupload. txt** nevű új fájlba az SAS-tárolóban:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    A következő szöveghez hasonló üzenet jelenik meg:

        put: java.io.IOException

    Ez a hiba azért fordul elő, mert a tárolási hely csak olvasható + lista. A következő paranccsal helyezheti el a fürt alapértelmezett tárolójában lévő adattárolást, amely írható:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Ezúttal a művelet végrehajtása sikeresen befejeződött.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan adhat hozzá korlátozott hozzáférésű tárolót a HDInsight-fürthöz, megismerheti a fürtön tárolt adatkezelés egyéb módjait:

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

