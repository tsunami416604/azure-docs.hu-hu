---
title: 'PowerShell: Az Azure Data Lake Storage Gen1 kiegészítő tárolóként az Azure HDInsight-fürtben |} A Microsoft Docs'
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f78ad8d58bb1bc760a31b792b44a4a39ed25e1f3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880100"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Az Azure PowerShell használata egy HDInsight-fürt létrehozása az Azure Data Lake Storage Gen1 (kiegészítő tárolóként)

> [!div class="op_single_selector"]
> * [A portál használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [(Az alapértelmezett tároló) a PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [(A további tárhely) PowerShell-lel](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [A Resource Manager használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell használata egy HDInsight-fürt konfigurálása az Azure Data Lake Storage Gen1 **kiegészítő tárolóként**. Egy HDInsight-fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként, lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Ha a Data Lake Storage Gen1 HDInsight-fürt kiegészítő tárolóként használni kívánja, erősen ajánlott, hogy ehhez a fürt létrehozásakor ebben a cikkben leírtak szerint. Data Lake Storage Gen1 kiegészítő tárolóként hozzáadni egy meglévő HDInsight fürt egy bonyolult folyamat, és a hibalehetőség.
>

Támogatott fürttípusok Data Lake Storage Gen1, egy alapértelmezett tároló további tárfiókot is használható. Data Lake Storage Gen1 használata kiegészítő tárolóként, az alapértelmezett tárfiókot, a fürt továbbra is az Azure Storage Blobs (WASB) és az alapértelmezett tárolóba, míg az adatok kívánt továbbra is írja a fürttel kapcsolatos fájlok (például naplók, stb.) folyamatot a Data Lake Storage Gen1 fiókban tárolhatók. Data Lake Storage Gen1 használja, mint egy tárfiókot funkciója nem érinti a teljesítmény vagy a az olvasási/írási a tárolóhoz a fürtből.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight-fürt Storage használatával a Data Lake Storage Gen1

Az alábbiakban néhány HDInsight használata a Data Lake Storage Gen1 fontos szempontjai:

* HDInsight-fürtök létrehozása a Data Lake Storage Gen1 hozzáférést, ha további tárhely elérhető HDInsight-verziók 3.2-es, 3.4-es, 3.5-ös és 3.6-os lehetőség.

HDInsight Data Lake Storage Gen1 dolgozhat konfigurálása, PowerShell-lel az alábbi lépésekből áll:

* Hozzon létre egy Data Lake Storage Gen1 fiókot
* Hitelesítés a Data Lake Storage Gen1 történő, szerepkörön alapuló hozzáférés beállítása
* HDInsight-fürt létrehozása a Data Lake Storage Gen1 hitelesítés
* A tesztfeladat futtatunk a fürtön

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Windows SDK**. A készletet [innen](https://dev.windows.com/en-us/downloads) telepítheti. Ezzel a biztonsági tanúsítvány létrehozásához.
* **Az Azure Active Directory egyszerű szolgáltatás**. Ebben az oktatóanyagban lépések nyújtanak segítséget az egyszerű szolgáltatás létrehozása az Azure ad-ben. Azonban az Azure AD-rendszergazda létrehozhat egy egyszerű szolgáltatást kell lennie. Ha egy Azure AD-rendszergazdát, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyagot.

    **Ha nem Azure AD-rendszergazda**, nem fogjuk tudni elvégezni az egyszerű szolgáltatás létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazda először létre kell hoznia egy egyszerű szolgáltatást a Data Lake Storage Gen1 egy HDInsight-fürt létrehozása előtt. Emellett az egyszerű szolgáltatás segítségével kell létrehozni egy tanúsítványt, ismertetett módon [egyszerű szolgáltatás létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot
Kövesse az alábbi lépéseket egy Data Lake Storage Gen1-fiók létrehozásához.

1. Az asztalon nyissa meg egy új Azure PowerShell-ablakot, és adja meg az alábbi kódrészletet. Amikor a rendszer kéri, jelentkezzen be, győződjön meg arról, hogy az előfizetés rendszergazdája vagy tulajdonosa egyik jelentkezzen be:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Ha a következőhöz hasonló hibaüzenetet kap `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` a Data Lake Storage Gen1 erőforrás-szolgáltató regisztrálásakor a rendszer lehetséges, hogy az előfizetés nem áll-e a Data Lake Storage Gen1 szerepel az engedélyezési listán. Győződjön meg arról, hogy engedélyezi az Azure Data Lake Storage Gen1-előfizetést a következő [utasításokat](data-lake-store-get-started-portal.md).
   >
   >
2. Egy Data Lake Storage Gen1 fiókkal társítva az Azure-erőforráscsoport. Először hozzon létre egy Azure-erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Egy a következőhöz hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott fiók neve csak kisbetűket és számokat tartalmazhat.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    A következőhöz hasonló kimenetnek kell megjelennie:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. Data Lake Storage Gen1 tölthet fel néhány adatot. Használjuk Ez a cikk későbbi részében annak ellenőrzéséhez, hogy az adatokat egy HDInsight-fürtön elérhető. Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Hitelesítés a Data Lake Storage Gen1 történő, szerepkörön alapuló hozzáférés beállítása

Minden Azure-előfizetés társítva az Azure Active Directoryban. Felhasználók és szolgáltatások, az előfizetés használata az Azure portal vagy Azure Resource Manager API-erőforrásokhoz hozzáférő először hitelesíteniük kell, hogy az Azure Active Directoryval. Hozzáférés az Azure-előfizetések és-szolgáltatások az Azure-erőforrás a megfelelő szerepkör hozzárendelésével.  A szolgáltatások esetében egy egyszerű szolgáltatást azonosítja a szolgáltatás az Azure Active Directory (AAD) található. Ez a szakasz bemutatja, hogyan adja meg az alkalmazásszolgáltatás, például a HDInsight, az Azure-erőforrás (a korábban létrehozott Data Lake Storage Gen1 fiók) hozzáférés által az alkalmazáshoz tartozó egyszerű szolgáltatás létrehozása, és rendeljen hozzájuk szerepköröket, amelyek az Azure Powershellen keresztül.

Active Directory-hitelesítés a Data Lake Storage Gen1 beállításával kapcsolatban a következő feladatokat kell végrehajtania.

* Önaláírt tanúsítvány létrehozása
* Egy alkalmazás létrehozása az Azure Active Directory és a egy egyszerű szolgáltatást

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Ellenőrizze, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) telepítve van a jelen szakaszban ismertetett lépések végrehajtása előtt. Kell is létrehozott egy könyvtárat, például **C:\mycertdir**, ahol létrejön a tanúsítványt.

1. A PowerShell-ablakban keresse meg a helyet, ahol telepített Windows SDK (általában `C:\Program Files (x86)\Windows Kits\10\bin\x86` , és használja a [MakeCert] [ makecert] segédprogramot, hozzon létre egy önaláírt tanúsítványt és a egy titkos kulcsot. Használja a következő parancsokat.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Adja meg a titkos kulcs jelszavát kéri. Miután a parancs sikeres végrehajtása, megjelenik egy **CertFile.cer** és **mykey.pvk** a megadott tanúsítvány-könyvtárban.
2. Használja a [Pvk2Pfx] [ pvk2pfx] segédprogramot a MakeCert által létrehozott .pvk, és .cer fájlokat átalakítása egy .pfx fájlba. Futtassa a következő parancsot.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri a titkos kulcs jelszava korábban megadott adja meg. A megadott érték a **-po** paraméter értéke a jelszót a .pfx fájl társított. A parancs sikeres végrehajtása után egy CertFile.pfx a megadott tanúsítvány-könyvtárban is megjelenik.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Egy Azure Active Directory és a egy egyszerű szolgáltatás létrehozása

Ebben a szakaszban hajtsa végre a lépéseket az Azure Active Directory-alkalmazás egyszerű szolgáltatás létrehozása, rendelhet egy szerepkört a szolgáltatásnévhez, és hitelesítse magát a szolgáltatásnév azáltal, hogy a tanúsítvány. A következő parancsokat egy alkalmazás létrehozása az Azure Active Directoryban.

1. Illessze be a következő parancsmagok a PowerShell-konzolablakot. Győződjön meg arról, hogy a megadott érték a **– DisplayName** tulajdonság egyedi. Emellett a tartozó értékeket **- kezdőlap** és **- IdentiferUris** helyőrző értékeket, és nem ellenőrzi.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Hozzon létre egy egyszerű szolgáltatás használatával az alkalmazás azonosítója.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Adja meg a szolgáltatásnév hozzáférhessen a Data Lake Storage Gen1 mappa és a fájlt, amely a HDInsight-fürtből származó férhet hozzá. Az alábbi kódrészlet a Data Lake Storage Gen1 fiók (ahová másolta a mintaadatfájl), a legfelső szintű hozzáférést biztosít, és magát a fájlt.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>HDInsight Linux-fürt létrehozása a Data Lake Storage Gen1 kiegészítő tárolóként

Ebben a szakaszban létrehozunk egy HDInsight Hadoop Linux-fürt a Data Lake Storage Gen1 kiegészítő tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Storage Gen1 fiók ugyanazon a helyen kell lennie.

1. Kezdődhet beolvasása az előfizetés bérlő azonosítóját. Később szüksége lesz, amely.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Ebben a kiadásban egy Hadoop-fürtöt a Data Lake Storage Gen1 csak használható egy kiegészítő tárolóként a fürt számára. Az alapértelmezett tároló is az Azure storage-blobokat (WASB). Ezért először létre kell hoznunk a storage-fiók és a storage-tárolók, a fürt számára szükséges.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. A HDInsight-fürt létrehozásához. Használja a következő parancsmagokat.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    A parancsmag sikeres befejezése után a fürt részletes adatai listázása egy kimenetnek kell megjelennie.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Tesztelési feladatok futtatása a Data Lake Storage Gen1 fiók használatára a HDInsight-fürt
Miután konfigurálta egy HDInsight-fürtöt, a fürtön, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Storage Gen1 teszteléséhez tesztelési feladatok futtatásához. Ehhez egy minta Hive-feladatot, amely létrehoz egy táblát a mintaadatokkal, amelyek korábban a Data Lake Storage Gen1 fiókjába feltöltött fog Futtatás.

Ez a szakasz tartalma SSH, a HDInsight Linux-fürt létrehozása, és futtassa a mintát Hive-lekérdezést.

* Ha a fürthöz az ssh Windows ügyfelet használ, tekintse meg [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha a fürthöz az ssh Linux-ügyfél használ, tekintse meg [az SSH használata a Linux-based Hadoop on HDInsight Linux rendszerben](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Ha csatlakoztatva van, indítsa el a Hive-CLI az alábbi paranccsal:

        hive
2. A parancssori felületről, adja meg az alábbi utasításokat, hozzon létre egy új táblát nevű **járművek** a mintaadatok használatával a Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    A következőhöz hasonló kimenetnek kell megjelennie:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage Gen1 eléréséhez HDFS-parancsok használatával
Miután konfigurálta a HDInsight-fürt használata a Data Lake Storage Gen1, a HDFS-rendszerhéjparancsokat használhatja az áruház.

Ebben a szakaszban SSH létrehozott és a HDFS-parancsok futtatása a HDInsight Linux fürtbe fog.

* Ha a fürthöz az ssh Windows ügyfelet használ, tekintse meg [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha a fürthöz az ssh Linux-ügyfél használ, tekintse meg [az SSH használata a Linux-based Hadoop on HDInsight Linux rendszerben](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

A csatlakozás után a következő HDFS-fájlrendszer parancs segítségével listázza a Data Lake Storage Gen1 fiókban lévő fájlokat.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Megjelenik a Data Lake Storage Gen1 korábban feltöltött fájl.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Is használhatja a `hdfs dfs -put` parancsot néhány fájlok feltöltése a Data Lake Storage Gen1, és hogyan `hdfs dfs -ls` ellenőrizze, hogy a fájl sikeresen feltöltve.

## <a name="see-also"></a>Lásd még:
* [Az Azure HDInsight-fürtök használata a Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: Hozzon létre egy HDInsight-fürt használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
