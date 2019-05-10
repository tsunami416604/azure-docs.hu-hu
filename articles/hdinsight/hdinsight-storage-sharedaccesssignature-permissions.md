---
title: Közös hozzáférésű Jogosultságkódokat – Azure HDInsight-hozzáférés korlátozása
description: Ismerje meg, hogyan lehet közös hozzáférési aláírások használata a HDInsight-hozzáférés korlátozása az Azure storage blobokban tárolt adatokat.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 7f7f6fe31afe35d9ccfd6ee33617bd7e4fbe46b7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409559"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Az Azure Storage közös hozzáférési aláírások használatával a HDInsight adatokhoz hozzáférésének korlátozása

HDInsight a fürthöz társított Azure Storage-fiókokat az adatok teljes hozzáféréssel rendelkezik. A blob-tároló a közös hozzáférési aláírások használatával korlátozza a hozzáférést az adatokat. A közös hozzáférésű Jogosultságkódok (SAS) érhetők el Azure storage-fiókot, amely lehetővé teszi, hogy az adatokhoz való hozzáférés korlátozására. Például villámgyors csak olvasható adatok.

> [!IMPORTANT]  
> Az Apache Ranger segítségével megoldások érdemes lehet tartományhoz csatlakoztatott HDInsight. További információkért lásd: a [tartományhoz csatlakoztatott HDInsight konfigurálása](./domain-joined/apache-domain-joined-configure.md) dokumentumot.

> [!WARNING]  
> HDInsight a fürthöz az alapértelmezett tároló teljes hozzáféréssel kell rendelkeznie.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](./hdinsight-hadoop-linux-use-ssh-unix.md).

* Egy meglévő [tároló](../storage/blobs/storage-quickstart-blobs-portal.md).  

* Ha a PowerShell használatával, akkor a [Az modul](https://docs.microsoft.com/powershell/azure/overview).

* Ha szeretnék használni az Azure CLI-vel, és még nem telepítette azt, lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Ha használ [Python](https://www.python.org/downloads/), 2.7-es vagy újabb verziója.

* Ha használ C#, a Visual Studio 2013-as vagy újabb verzióját kell lennie.

* A [URI-séma](./hdinsight-hadoop-linux-information.md#URI-and-scheme) a tárfiók számára. Ez akkor lehet `wasb://` az Azure Storage esetében `abfs://` az Azure Data Lake Storage Gen2 vagy `adl://` az Azure Data Lake Storage Gen1. Ha biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2, az URI lesz `wasbs://` vagy `abfss://`, illetve lásd még a [biztonságos átvitelre](../storage/common/storage-require-secure-transfer.md).

* Egy meglévő HDInsight-fürtön adja hozzá a közös hozzáférési aláírásokat. Ha nem, az Azure PowerShell segítségével hozzon létre egy fürtöt, és a egy közös hozzáférésű Jogosultságkód hozzáadása a fürt létrehozása során.

* A példa fájlt [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ez a tárház a következő elemeket tartalmazza:

  * Visual Studio-projekt, amely egy storage-tárolót, a tárolt házirend és a SAS hozhat létre, a HDInsight segítségével
  * Egy Python-szkriptet, amely egy storage-tárolót, a tárolt házirend és a SAS hozhat létre, a HDInsight segítségével
  * Egy PowerShell-parancsprogram, amely egy HDInsight-fürt létrehozása és konfigurálása, hogy a SAS kódot használja. Egy frissített verziót használja használja a lentebb.
  * Mintafájl: `hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok

Kétféle közös hozzáférési aláírások van:

* Ad hoc: A kezdési idő, lejárati időpontot és engedélyeket a SAS az összes megadott SAS URI-n.

* Tárolt hozzáférési szabályzatot: Egy tárolt hozzáférési szabályzat van definiálva egy erőforrás-tárolón, például egy blob-tárolóba. A házirend legalább egy közös hozzáférési aláírások megkötések kezelésére használható. SAS társítása egy tárolt hozzáférési szabályzatot, az SAS örökli a megkötések - a kezdési idő, lejárati idő és a tárolt hozzáférési szabályzat definiált - engedélyek.

A különbség a két formáját fontos egyik legfontosabb előny a: visszavont tanúsítványok. SAS egy URL-címe, így bárki, aki szerzi be a SAS használatával is, először aki kérte, függetlenül. Ha SAS nyilvánosan közzé van téve, használat szerint, a világ bármely részéről. Elosztott SAS nem érvényes, amíg a négy dolog történik:

1. Az SAS a megadott lejárati idő elérésekor.

2. A SAS által hivatkozott tárolt hozzáférési házirendben megadott lejárati idő elérésekor. A következő esetekben okozhat a lejárati időpont érhető el:

    * Lejárt az időtartam alatt.
    * A tárolt hozzáférési szabályzat módosul, egy lejárati dátuma a múltban van. A lejárati időpont módosítjuk az egyik módja visszavonja az SAS.

3. A tárolt hozzáférési szabályzatot a SAS által hivatkozott törlődik, amely visszavonja a SAS egy másik módja. Ha újra létrehozza ugyanazzal a névvel a tárolt hozzáférési szabályzatot, az előző házirend minden SAS-jogkivonatok érvényesek (Ha nem ment át az SAS a lejárati ideje). Ha visszavonja a SAS szeretne, mindenképpen használjon másik nevet, ha a hozzáférési szabályzat a későbbiekben egy lejárati idővel hozza létre újra.

4. A fiók SAS létrehozásához használt kulcs újragenerálása van. A kulcs újragenerálása hatására a minden alkalmazás, amely a korábbi kulcs használatával a hitelesítése sikertelen legyen. Frissítse az összes összetevő az új kulccsal.

> [!IMPORTANT]  
> A fiókkulcs aláírásának létrehozására használt társítva egy közös hozzáférésű jogosultságkód URI-t, és a társított tárolt hozzáférési szabályzat (ha van). Ha nincs tárolt hozzáférési szabályzat van megadva, a csak visszavonása a közös hozzáférésű jogosultságkód módja a fiókkulcs módosításához.

Javasoljuk, hogy mindig használjon tárolt hozzáférési szabályzatok. Tárolt szabályzatok használatakor az aláírások visszavonása, vagy kiterjesztheti a lejárati dátum, igény szerint. Ez a dokumentum használatát a lépések tárolt hozzáférési szabályzatok SAS előállítása.

A közös hozzáférésű Jogosultságkódokat további információkért lásd: [a SAS-modell ismertetése](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

## <a name="create-a-stored-policy-and-sas"></a>Hozzon létre egy tárolt házirend és a SAS

Mentse a SAS-jogkivonat, amely az egyes módszerek végén jön létre. A jogkivonat az alábbihoz hasonlóan fog kinézni:

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>A PowerShell használata

Cserélje le `RESOURCEGROUP`, `STORAGEACCOUNT`, és `STORAGECONTAINER` a meglévő storage-tároló a megfelelő értékekkel. Módosítsa a könyvtárat `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa a `-File` abszolút elérési útját tartalmazó paraméter `Set-AzStorageblobcontent`. Adja meg a következő PowerShell-parancsot:

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

Ez a szakasz a változók használata a Windows-környezet alapján történik. A bash- vagy más környezetekben enyhe változata lesz szükség.

1. Cserélje le `STORAGEACCOUNT`, és `STORAGECONTAINER` a meglévő storage-tároló a megfelelő értékekkel.

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

2. Állítsa be a lekért elsődleges kulcsot egy változóhoz későbbi használatra. Cserélje le `PRIMARYKEY` a beolvasott az értékét az előző lépésben, és adja meg az alábbi parancsot:

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. Módosítsa a könyvtárat `hdinsight-dotnet-python-azure-storage-shared-access-signature-master` vagy módosítsa a `--file` abszolút elérési útját tartalmazó paraméter `az storage blob upload`. Hajtsa végre a fennmaradó parancsokat:

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

Nyissa meg a `SASToken.py` fájlt, és cserélje le `storage_account_name`, `storage_account_key`, és `storage_container_name` a meglévő storage-tárolót, és futtassa a parancsfájlt a megfelelő értékekkel.

Szükség lehet végrehajtani `pip install --upgrade azure-storage` , ha a hibaüzenet `ImportError: No module named azure.storage`.

### <a name="using-c"></a>A C# használata

1. Nyissa meg a megoldást a Visual Studióban.

2. A Megoldáskezelőben kattintson a jobb gombbal a a **SASExample** projektre, és válassza **tulajdonságok**.

3. Válassza ki **beállítások** , és adja hozzá az értékeket az alábbi bejegyzéseket:

   * StorageConnectionString: Azt szeretné, hozzon létre egy tárolt házirend és az SAS-tároló tárfiók kapcsolati karakterláncát. A formátum `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` ahol `myaccount` a tárfiók neve és `mykey` a tárfiók kulcsa.

   * ContainerName: A tároló, amely korlátozza a hozzáférést a kívánt storage-fiókban.

   * SASPolicyName: A nevét, a tárolt házirend létrehozásához.

   * FileToUpload: A tárolóba feltöltött fájl elérési útja.

4. Futtassa a projektet. Mentse a SAS házirend jogkivonat, a tárfiók nevét és a tároló neve. A storage-fiókot társít a HDInsight-fürt használja ezeket az értékeket.

## <a name="use-the-sas-with-hdinsight"></a>SAS használata a HDInsight

Egy HDInsight-fürt létrehozásakor meg kell adnia egy elsődleges tárfiók, és megadhat további tárfiókok. Mindkét módszer a tárolóeszközök hozzáadása a storage-fiókok és a használt tárolók teljes hozzáférést igényelnek.

Egy közös hozzáférésű Jogosultságkód használatával korlátozhatja a hozzáférést a tárolóhoz, adjon hozzá egy egyéni bejegyzést a **hely** a fürt konfigurációját. PowerShell-lel a fürt létrehozásakor, vagy az Ambari fürt létrehozása után hozzáadhat a bejegyzést.

### <a name="create-a-cluster-that-uses-the-sas"></a>A SAS kódot használó fürt létrehozása

Cserélje le `CLUSTERNAME`, `RESOURCEGROUP`, `DEFAULTSTORAGEACCOUNT`, `STORAGECONTAINER`, `STORAGEACCOUNT`, és `TOKEN` megfelelő értékeivel. Adja meg a PowerShell-parancsokat:

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
> Ha a HTTP/HTTPS vagy SSH-felhasználónevet és jelszót kér, meg kell adnia egy jelszót, amely megfelel a következő feltételeknek:
>
> * Legalább 10 karakter hosszúságúnak kell lennie.
> * Legalább egy számjegyet kell tartalmaznia.
> * Legalább egy nem alfanumerikus karaktert kell tartalmaznia.
> * Legalább egy nagy- vagy kisbetűt tartalmaznia kell.

Míg ez a szkript végrehajtásához, általában körülbelül 15 percet vesz igénybe. Miután a parancsfájl futása befejeződött, hibák nélkül, a fürt létrejött.

### <a name="use-the-sas-with-an-existing-cluster"></a>SAS használata egy meglévő fürthöz

Ha rendelkezik egy meglévő fürthöz, adhat hozzá az SAS a **hely** -konfigurációjának az alábbi lépéseket követve:

1. Nyissa meg az Ambari webes felhasználói felület a fürt számára. Ez az oldal címe `https://YOURCLUSTERNAME.azurehdinsight.net`. Amikor a rendszer kéri, a fürthöz hitelesíteni kívánt rendszergazda neve (rendszergazdai) és a használt jelszót, amikor a fürt létrehozásához.

2. Az Ambari webes felhasználói felület a bal oldali menüjében válassza **HDFS** majd válassza ki a **Configs** fülre az oldal közepén.

3. Válassza ki a **speciális** lapra, és görgessen, amíg meg nem találja a **egyéni hely** szakaszban.

4. Bontsa ki a **egyéni hely** szakaszban, majd görgessen lefelé a végfelhasználók, és válassza a **tulajdonság hozzáadása...**  hivatkozásra. A következő értékeket használja a **kulcs** és **érték** mezők:

   * **kulcs**: `fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **Érték**: A korábban végrehajtott módszerek által visszaadott SAS.

     Cserélje le `CONTAINERNAME` az a tároló nevét, a használt a C# vagy SAS-alkalmazás. Cserélje le `STORAGEACCOUNTNAME` a a használt tárfiók nevét.

5. Kattintson a **Hozzáadás** gombra kattintva mentse a kulcs-érték, majd kattintson a **mentése** gombot a konfigurációs módosítások mentéséhez. Amikor a rendszer kéri, adjon meg egy leírást a változás ("Hozzáadás SAS-tároló hozzáférés" például), és kattintson a **mentése**.

    Kattintson a **OK** Ha végrehajtotta a változásokat.

   > [!IMPORTANT]  
   > A módosítás előtt újra kell indítania több szolgáltatást.

6. Az Ambari webes felhasználói felületen válassza **HDFS** a bal oldali listából, majd **indítsa újra az összes érintett** a a **szolgáltatás műveletek** legördülő listában a jobb oldalon. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

    Ismételje meg ezt a folyamatot MapReduce2 és YARN.

7. A szolgáltatás újraindítása, ha egyenként válassza ki, és tiltsa le a karbantartási módnak a **szolgáltatás műveletek** legördülő menü.

## <a name="test-restricted-access"></a>Korlátozott hozzáférés tesztelése

Az alábbi lépések segítségével győződjön meg arról, hogy a tárfiók SAS csak olvasási és a lista elemek is.

1. Csatlakozás a fürthöz. Cserélje le `CLUSTERNAME` a fürt nevére, és adja meg a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A tároló tartalmának listázásához használja a következő parancsot a parancssorba:

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Cserélje le `SASCONTAINER` a SAS-tárfiók létrehozása a tároló nevére. Cserélje le `SASACCOUNTNAME` SAS használt tárfiók nevére.

    A lista tartalmazza a fájl feltöltése a tárolóba, és a SAS létrehozása.

3. A következő parancs használatával győződjön meg arról, hogy a fájl tartalmát áttekintheti. Cserélje le a `SASCONTAINER` és `SASACCOUNTNAME` ahogy az előző lépésben. Cserélje le `sample.log` a fájlt az előző parancsban megjelenített nevére:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    Ez a parancs felsorolja a fájl tartalmát.

4. A következő paranccsal töltse le a fájlt a helyi fájlrendszerben:

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    Ez a parancs letölti a fájlt egy helyi fájlba nevű **példa.txt**.

5. A következő paranccsal egy új fájlt a helyi fájl feltöltése **testupload.txt** SAS tárolására:

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Az alábbi szöveghez hasonló üzenetet kapja:

        put: java.io.IOException

    Ez a hiba oka az, hogy a tárolási hely olvasási + csak lista. A következő paranccsal helyezze az adatokat a fürthöz, írható alapértelmezett tárolására:

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    Ebben az esetben a művelet sikeresen befejeződik.

## <a name="next-steps"></a>További lépések

Most, hogy, hogyan korlátozott hozzáférésű tár hozzáadása a HDInsight-fürthöz, ismerje meg, hogyan más adatokkal szeretne dolgozni a fürtön:

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

