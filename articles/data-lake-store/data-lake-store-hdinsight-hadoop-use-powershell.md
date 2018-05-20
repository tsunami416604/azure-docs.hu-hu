---
title: 'PowerShell: A Data Lake Store bővítmény tárolóként az Azure HDInsight fürt |} Microsoft Docs'
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 4c08dac95a2d2b52f1a1d28f6933b94ad4db10b7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-data-lake-store-as-additional-storage"></a>Azure PowerShell használata a HDInsight-fürtök létrehozása a Data Lake Store (a további tárhely)

> [!div class="op_single_selector"]
> * [A Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használatával (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [(Tárhely) a PowerShell használatával](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használatával](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Azure PowerShell használata a HDInsight-fürtök konfigurálása az Azure Data Lake Store **további tárolóként**. A HDInsight-fürtök létrehozása az Azure Data Lake Store alapértelmezett tárolóként útmutatásért lásd: [HDInsight-fürtök létrehozása a Data Lake Store alapértelmezett tárolóként](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Ha a Data Lake Store-t a HDInsight-fürt kiegészítő tárolójaként tervezi használni, akkor kifejezetten ezt a megoldást javasoljuk, amikor létrehozza a fürtöt az ebben a cikkben leírtaknak megfelelően. Az Azure Data Lake Store-t kiegészítő tárolóként hozzáadni egy meglévő HDInsight-fürthöz bonyolult folyamat, és sok hibalehetőséggel jár.
>

Támogatott fürttípusok Data Lake Store egy alapértelmezett tároló vagy a további tárhely fiókként használható. Ha a Data Lake Store további tárterületet, az alapértelmezett tárfiókot, a fürt továbbra is Azure Storage Blobs (WASB), és a fürt kapcsolatos (például naplói, stb.) továbbra is írja a fájlt, alapértelmezett tárolására, amíg a Data Lake Store-fiók tárolhatja az adatokat, hogy fel szeretné dolgoztatni. További tárhely fiókként használatával a Data Lake Store nem befolyásolja a teljesítményt vagy olvasására vagy írására a tárhelyet a fürtből történő alkalmazásának képességét.

## <a name="using-data-lake-store-for-hdinsight-cluster-storage"></a>Data Lake Store használata a HDInsight-fürt tárolására

Az alábbiakban a HDInsight a Data Lake Store használatára vonatkozó szempontokat:

* A HDInsight-fürtök létrehozása a Data Lake Store elérését, további tárhely áll rendelkezésre a HDInsight-verziókról 3.2-es, 3.4, 3.5-ös és 3.6 lehetőséget.

HDInsight a Data Lake Store működéséhez konfigurálása PowerShell használatával az alábbi lépésekből:

* Hozzon létre egy Azure Data Lake Store
* A szerepköralapú hozzáférés-Data Lake Store-hitelesítés beállítása
* HDInsight-fürt létrehozása a Data Lake Store-hitelesítéssel
* Egy tesztelési feladat futtatása a fürtön

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Windows SDK**. A készletet [innen](https://dev.windows.com/en-us/downloads) telepítheti. Ezzel a biztonsági tanúsítvány létrehozásához.
* **Az Azure Active Directory szolgáltatás egyszerű**. Ez az oktatóanyag lépéseit ad útmutatást az egyszerű szolgáltatás létrehozása az Azure ad-ben. Azonban az Azure AD a rendszergazda létrehozhat egy egyszerű szolgáltatást kell lennie. Ha az Azure AD-rendszergazdaként, hagyja ki ezt az előfeltételt, és az oktatóanyag folytatásához.

    **Ha nem az Azure AD-rendszergazda**, nem fog tudni egy egyszerű szolgáltatásnév létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazda először létre kell hoznia egy egyszerű szolgáltatást a Data Lake Store egy HDInsight-fürt létrehozása előtt. Emellett az egyszerű szolgáltatás segítségével kell létrehozni egy tanúsítványt, részben ismertetett módon [hozzon létre egy egyszerű tanúsítvány](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-an-azure-data-lake-store"></a>Hozzon létre egy Azure Data Lake Store
Kövesse az alábbi lépéseket egy Data Lake Store létrehozásához.

1. Az asztalon nyisson meg egy új Azure PowerShell-ablakot, és adja meg a következő kódrészletet. Amikor a rendszer kéri-e jelentkezni, győződjön meg arról jelentkezik be a rendelkezésre álló az előfizetés rendszergazdája vagy tulajdonosa:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Ha a hibaüzenet hasonló `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` a Data Lake Store erőforrás-szolgáltató regisztrálása, esetén lehetséges, hogy az előfizetés nem szerepel az Azure Data Lake Store az engedélyezési listán. Győződjön meg arról, hogy az Azure-előfizetéshez a Data Lake Store nyilvános előzetes verziójához engedélyezze a következő [utasításokat](data-lake-store-get-started-portal.md).
   >
   >
2. Az Azure Data Lake Store-fiókok egy Azure-erőforráscsoporthoz vannak társítva. Először hozzon létre egy Azure-erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Ez hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Hozzon létre egy Azure Data Lake Store-fiókot. A megadott fiók neve csak kisbetűket és számokat tartalmazhat.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

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

5. Azure Data Lake tölthet fel néhány adatot. Használjuk Ez a cikk későbbi részében annak ellenőrzéséhez, hogy az adatok érhető el a HDInsight-fürtöt. Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

        $myrootdir = "/"
        Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>A szerepköralapú hozzáférés-Data Lake Store-hitelesítés beállítása

Egy Azure Active Directory minden Azure-előfizetés tartozik. Felhasználók és a szolgáltatások, az előfizetés az Azure-portálon vagy az Azure Resource Manager API erőforrásokat elérő először hitelesítenie kell magát, hogy Azure Active Directoryban. Hozzáférés az Azure-előfizetések és-szolgáltatások egy Azure-erőforrás a megfelelő szerepkört hozzárendelésével.  Szolgáltatások esetén egy egyszerű szolgáltatást a szolgáltatás az Azure Active Directory (AAD) a azonosítja. Ez a szakasz bemutatja az alkalmazásszolgáltatás, mint például a HDInsight, egy Azure-erőforrás (a korábban létrehozott Azure Data Lake Store fióknak) való hozzáférés engedélyezése az alkalmazás egyszerű szolgáltatás létrehozása és hozzárendelése a szerepkörök, amelyek Azure PowerShell.

Active Directory-hitelesítés az Azure Data Lake beállításához a következő feladatokat kell elvégeznie.

* Önaláírt tanúsítvány létrehozása
* Létrehoz egy alkalmazást az Azure Active Directory és az egyszerű szolgáltatás

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Győződjön meg arról, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) ebben a szakaszban a lépések végrehajtása előtt. Kell is létrehozott egy könyvtárat, például a **C:\mycertdir**, ahol a tanúsítvány jön létre.

1. A PowerShell ablakban keresse meg a helyet, amelyre telepítette a Windows SDK (általában `C:\Program Files (x86)\Windows Kits\10\bin\x86` , és használja a [MakeCert] [ makecert] segédprogram egy önaláírt tanúsítványt és a titkos kulcs létrehozása. Az alábbi parancsokkal.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    A rendszer bekéri a titkos kulcsok jelszavának megadása. Miután a parancs sikeres végrehajtása során, megtekintheti az egy **CertFile.cer** és **mykey.pvk** a tanúsítvány megadott könyvtárban.
2. Használja a [Pvk2Pfx] [ pvk2pfx] segédprogram a MakeCert létrehozott .pvk és .cer fájl átalakítása egy .pfx fájlba. A következő parancsot.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri a korábban meghatározott titkos kulcsok jelszavának megadása. A megadott érték a **-po** paramétere a jelszót a .pfx fájl társított. Ha a parancs sikeresen befejeződött, emellett meg kell jelennie egy CertFile.pfx a tanúsítvány megadott könyvtárban.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Egy Azure Active Directory és az egyszerű szolgáltatás létrehozása

Ebben a szakaszban hajtsa végre a lépéseket egy egyszerű szolgáltatásnév létrehozása az Azure Active Directory-alkalmazás, a szerepkör hozzárendelése az egyszerű szolgáltatásnév és hitelesítse magát a szolgáltatás egyszerű, adja meg a tanúsítványt. A következő parancsokat az alkalmazás létrehozása az Azure Active Directoryban.

1. A PowerShell-konzolablakot illessze be a következő parancsmagokat. Győződjön meg arról, hogy a megadott érték a **- DisplayName** tulajdonság értéke egyedi. Az is, az értékek **- kezdőlap** és **- IdentiferUris** helyőrző értékeket, és nem ellenőrzi.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Hozzon létre egy egyszerű szolgáltatásnév az alkalmazás azonosítójával.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. A Data Lake Store-mappa és a fájlt, amely akkor érik el a HDInsight-fürt a szolgáltatás egyszerű hozzáférést engedélyez. Az alábbi részlet biztosít hozzáférést a Data Lake Store-fiók (másolásakor a mintaadatfájlokat), a legfelső szintű és magát a fájlt.

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-additional-storage"></a>Egy HDInsight Linux-fürt létrehozása a Data Lake Store további tárhely

Ebben a részben azt egy HDInsight Hadoop Linux fürt létrehozása a Data Lake Store további tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Store ugyanazon a helyen kell lennie.

1. Indítsa el az előfizetés-azonosító. bérlő beolvasása Később szüksége lesz, amely.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId
2. Ebben a kiadásban a Hadoop fürtök a Data Lake Store csak használható további tárterületként a fürthöz. Az alapértelmezett tároló is az Azure storage blobs szolgáltatásban (WASB). Igen először létrehozunk a tárfiók és a tároló a fürt szükséges.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name

        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $containerName -Context $destContext
3. A HDInsight-fürtök létrehozása. A következő parancsmagokat használja.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    A parancsmag sikeres befejezése után, a fürt részleteket felsoroló kimenetnek kell megjelennie.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>A HDInsight-fürt használata a Data Lake Store tesztet-feladatok futtatása
Miután konfigurálta a HDInsight-fürtöt, a teszt feladatok ellenőrzéséhez, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Store a fürtön is futtathatja. Ehhez az szükséges, azt egy minta Hive táblát hoz létre a Data Lake Store korábban feltöltött megadott mintaadatokat használja feladat elindul.

Ebben a szakaszban fogja SSH a HDInsight Linux fürthöz létrehozott, és futtassa a minta Hive-lekérdezések.

* Ha a fürthöz SSH Windows ügyfél használ, tekintse meg [SSH használata a HDInsight Windows Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha a fürthöz az SSH Linux-ügyfél használ, tekintse meg [SSH használata a HDInsight Linux Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. A csatlakozás után a következő parancs használatával indítsa el a Hive CLI:

        hive
2. A parancssori felület használatával adja meg az alábbi állításokat annak nevű új tábla létrehozása **járművekről gyűjtött** által megadott mintaadatokat használja a Data Lake Store-ban:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
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

## <a name="access-data-lake-store-using-hdfs-commands"></a>Hozzáférés Data Lake Store HDFS parancs használatával
Miután konfigurálta a Data Lake Store használata a HDInsight-fürthöz, a HDFS felületparancsokat használhatja az áruház eléréséhez szükséges.

Az itt SSH a HDInsight Linux fürthöz létrehozott és a HDFS parancs futtatása lesz.

* Ha a fürthöz SSH Windows ügyfél használ, tekintse meg [SSH használata a HDInsight Windows Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha a fürthöz az SSH Linux-ügyfél használ, tekintse meg [SSH használata a HDInsight Linux Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

A csatlakozás után a következő HDFS filesystem parancs segítségével a Data Lake Store található fájlok listázása.

    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Megjelenik a korábban a Data Lake Store-bA feltöltött fájl.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

Használhatja a `hdfs dfs -put` parancs egyes fájlok feltöltése a Data Lake Store, és ezután `hdfs dfs -ls` ellenőrzése, hogy a fájlok sikeresen feltöltve.

## <a name="see-also"></a>Lásd még:
* [Használjon Data Lake Store az Azure HDInsight-fürtök](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: Data Lake Store használatára HDInsight-fürtök létrehozása](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
