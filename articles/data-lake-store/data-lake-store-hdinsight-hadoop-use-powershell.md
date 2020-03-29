---
title: 'PowerShell: Azure HDInsight-fürt az Azure Data Lake Storage Gen1 bővítménytárolóként | Microsoft dokumentumok'
description: Megtudhatja, hogy az Azure PowerShell használatával konfigurálhat egy HDInsight-fürtöt az Azure Data Lake Storage Gen1 használatával további tárhelyként.
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
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970139"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Az Azure PowerShell használatával HDInsight-fürtöt hozhat létre az Azure Data Lake Storage Gen1 használatával (további tárhelyként)

> [!div class="op_single_selector"]
> * [A portál használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (alapértelmezett tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Az Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Megtudhatja, hogy az Azure PowerShell használatával miként konfigurálhat hdinsight-fürtöt az Azure Data Lake Storage Gen1 szolgáltatással **további tárhelyként.** A HDInsight-fürt alapértelmezett tárolóként való létrehozásáról a [HDInsight-fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)című témakörben olvashat.

> [!NOTE]
> Ha a Data Lake Storage Gen1-et a HDInsight-fürt további tárolójaként fogja használni, javasoljuk, hogy ezt a fürt létrehozása közben tegye meg a jelen cikkben leírtak szerint. A Data Lake Storage Gen1 hozzáadása további tárolóként egy meglévő HDInsight-fürthöz bonyolult folyamat, és hajlamos a hibákra.
>

Támogatott fürttípusok esetén a Data Lake Storage Gen1 alapértelmezett tárolóként vagy további tárfiókként használható. Ha a Data Lake Storage Gen1-et további tárolóként használja, a fürtök alapértelmezett tárfiókja továbbra is az Azure Storage Blobs (WASB) lesz, és a fürthöz kapcsolódó fájlok (például naplók stb.) továbbra is az alapértelmezett tárolóba kerülnek, míg a kívánt adatok folyamat tárolható a Data Lake Storage Gen1 fiókban. A Data Lake Storage Gen1 használata további tárfiókként nem befolyásolja a teljesítményt, és nem képes írni/olvasni a fürtből a tárolóba.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>A Data Lake Storage Gen1 használata a HDInsight-fürttároláshoz

Az alábbiakban a HDInsight data lake storage gen1 szolgáltatással való használatának néhány fontos szempontját olvashatja:

* HdInsight-fürtök et hozhat létre a Data Lake Storage Gen1 szolgáltatáshoz való hozzáféréssel, mivel további tárhely érhető el a HDInsight 3.2-es, 3.4-es, 3.5-ös és 3.6-os verziójához.

A HDInsight konfigurálása a Data Lake Storage Gen1 powershell használatával történő együttműködésre a következő lépésekre van haszükséges:

* Data Lake Storage Gen1-fiók létrehozása
* Hitelesítés beállítása a Data Lake Storage Gen1 szerepköralapú eléréséhez
* HDInsight-fürt létrehozása hitelesítéssel a Data Lake Storage Gen1 szolgáltatással
* Tesztfeladat futtatása a fürtön

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Az **Azure PowerShell 1.0-s vagy újabb verziója**. Lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).
* **Windows SDK**. A készletet [innen](https://dev.windows.com/en-us/downloads) telepítheti. Ezzel biztonsági tanúsítványt hozhat létre.
* **Az Azure Active Directory szolgáltatásnév .** Ebben az oktatóanyagban az egyszerű szolgáltatás létrehozása az Azure AD-ben. Azonban egy Azure AD-rendszergazdai kell lennie, hogy hozzon létre egy egyszerű szolgáltatás. Ha Ön Az Azure AD-rendszergazda, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyag.

    **Ha nem Azure AD-rendszergazda,** nem fogja tudni végrehajtani az egyszerű szolgáltatás létrehozásához szükséges lépéseket. Ebben az esetben az Azure AD-rendszergazdának először létre kell hoznia egy egyszerű szolgáltatásszolgáltatást, mielőtt hdinsight-fürtöt hozhat létre a Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnév kell létrehozni egy tanúsítvány, ahogy azt a [Szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)című részen leírtak szerint.

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása
A Data Lake Storage Gen1 fiók létrehozásához kövesse az alábbi lépéseket.

1. Az asztalról nyisson meg egy új Azure PowerShell-ablakot, és írja be a következő kódrészletet. Amikor a rendszer kéri a bejelentkezést, jelentkezzen be az előfizetés egyik rendszergazdájaként/tulajdonosaként:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Ha a Data Lake `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` Storage Gen1 erőforrás-szolgáltató regisztrálásakor hasonló hibaüzenetjelenik meg, lehetséges, hogy az előfizetés nem szerepel a Data Lake Storage Gen1 engedélyezési listáján. Győződjön meg arról, hogy engedélyezi az Azure-előfizetést a Data Lake Storage Gen1 számára az alábbi [utasításokat](data-lake-store-get-started-portal.md)követve.
   >
   >
2. A Data Lake Storage Gen1 fiók egy Azure Resource Group társított. Először hozzon létre egy Azure-erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    A következőhöz hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott fióknév csak kisbetűket és számokat tartalmazhat.

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

5. Töltsön fel néhány mintaadatot a Data Lake Storage Gen1 szolgáltatásba. Ezt a cikket a cikk későbbi részében annak ellenőrzésére használjuk, hogy az adatok elérhetők-e egy HDInsight-fürtről. Ha feltölthető mintaadatokra van szüksége, használhatja az [Azure Data Lake Git-tárában](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) található **Ambulance Data** mappát.

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Hitelesítés beállítása a Data Lake Storage Gen1 szerepköralapú eléréséhez

Minden Azure-előfizetés egy Azure Active Directoryhoz van társítva. Azoknak a felhasználóknak és szolgáltatásoknak, amelyek az Azure Portalon vagy az Azure Resource Manager API-n keresztül érik el az előfizetés erőforrásait, először hitelesíteniük kell magukat az adott Azure Active Directoryval. Az Azure-előfizetések és -szolgáltatások számára a hozzáférést úgy biztosítják, hogy hozzárendelik a megfelelő szerepkört egy Azure-erőforráshoz.  Szolgáltatások esetén egy egyszerű szolgáltatás azonosítja a szolgáltatást az Azure Active Directoryban (AAD). Ez a szakasz bemutatja, hogyan adhat hozzáférést egy alkalmazásszolgáltatásnak, például a HDInsightnak egy Azure-erőforráshoz (a korábban létrehozott Data Lake Storage Gen1 fiókhoz) egy egyszerű szolgáltatás létrehozásával az alkalmazáshoz, és szerepkörök hozzárendelésével az Azure PowerShellen keresztül.

A Data Lake Storage Gen1 Active Directory-hitelesítésének beállításához a következő feladatokat kell végrehajtania.

* Önaláírt tanúsítvány létrehozása
* Alkalmazás létrehozása az Azure Active Directoryban és egyszerű szolgáltatás

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Mielőtt folytatná a szakaszlépéseit, győződjön meg arról, hogy telepítve van a [Windows SDK.](https://dev.windows.com/en-us/downloads) Létre kell hoznia egy könyvtárat is, például **a C:\mycertdir könyvtárat,** ahol a tanúsítvány létrejön.

1. A PowerShell ablakban keresse meg azt a helyet, ahol `C:\Program Files (x86)\Windows Kits\10\bin\x86` a Windows SDK-t telepítette (általában a [MakeCert][makecert] segédprogrammal hozzon létre egy önaláírt tanúsítványt és egy személyes kulcsot. Használja a következő parancsokat.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    A rendszer kéri a személyes kulcs jelszavának megadását. A parancs sikeres végrehajtása után a megadott tanúsítványkönyvtárban megjelenik egy **CertFile.cer** és **mykey.pvk** fájl.
2. A [Pvk2Pfx][pvk2pfx] segédprogrammal konvertálja a MakeCert által létrehozott .pvk és .cer fájlokat .pfx fájllá. Futtassa a következő parancsot.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri, adja meg a korábban megadott személyes kulcsjelszót. A **-po** paraméterhez megadott érték a .pfx fájlhoz társított jelszó. A parancs sikeres befejezése után a megadott tanúsítványkönyvtárban egy CertFile.pfx fájl is megjelenik.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Azure Active Directory és egyszerű szolgáltatás létrehozása

Ebben a szakaszban hajtsa végre a lépéseket egy egyszerű szolgáltatás létrehozása egy Azure Active Directory-alkalmazáshoz, rendeljen hozzá egy szerepkört az egyszerű szolgáltatáshoz, és hitelesítse magát egyszerű szolgáltatásként egy tanúsítvány biztosításával. Futtassa a következő parancsokat egy alkalmazás létrehozásához az Azure Active Directoryban.

1. Illessze be a következő parancsmagokat a PowerShell konzolablakába. Győződjön meg arról, hogy a **-DisplayName** tulajdonsághoz megadott érték egyedi. Emellett a **-HomePage** és az **-IdentiferUris** értékek helyőrző értékek, és nem ellenőrzöttek.

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
2. Hozzon létre egy egyszerű szolgáltatás az alkalmazásazonosító használatával.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Adja meg a szolgáltatás egyszerű hozzáférést a Data Lake Storage Gen1 mappához és a HDInsight-fürtből elérendő fájlhoz. Az alábbi kódrészlet hozzáférést biztosít a Data Lake Storage Gen1 fiók (a mintaadatfájl másolása) és maga a fájl gyökeréhez.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>HdInsight Linux-fürt létrehozása a Data Lake Storage Gen1 további tárolóként

Ebben a szakaszban hozzon létre egy HDInsight Hadoop Linux-fürt a Data Lake Storage Gen1 további tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Storage Gen1 fióknak ugyanazon a helyen kell lennie.

1. Kezdje az előfizetés-bérlői azonosító lekérésével. Erre később szükséged lesz.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Ebben a kiadásban a Hadoop-fürt esetében a Data Lake Storage Gen1 csak a fürt további tárolójaként használható. Az alapértelmezett tároló továbbra is az Azure storage blobok (WASB). Ezért először hozzon létre a fürthöz szükséges tárfiókot és tárolótárolókat.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Hozza létre a HDInsight-fürtöt. Használja a következő parancsmagokat.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Miután a parancsmag sikeresen befejeződött, meg kell jelennie egy kimenet, amely felsorolja a fürt részleteit.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 fiók használatához
Miután konfigurált egy HDInsight-fürtöt, futtathat tesztfeladatokat a fürtön annak teszteléséhez, hogy a HDInsight-fürt hozzáférhet-e a Data Lake Storage Gen1 szolgáltatáshoz. Ehhez egy minta Hive-feladatot futtatunk, amely létrehoz egy táblát a Data Lake Storage Gen1-fiókjába korábban feltöltött mintaadatok használatával.

Ebben a szakaszban lesz SSH a HDInsight Linux-fürt höz létrehozott és futtatja a minta Hive-lekérdezést.

* Ha Windows-ügyfélprogramot használ az SSH-hoz a fürtbe, olvassa el az [SSH használata Linux alapú Hadoop használatával a HDInsight-on Windows-ból című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Ha Egy Linux kliens t használ a fürtsú SSH-hoz, olvassa el az [SSH használata Linux alapú Hadoop segítségével a HDInsight-on Linuxról](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. A csatlakozás után indítsa el a Hive CLI-t a következő paranccsal:

        hive
2. A CLI használatával adja meg a következő állításokat egy **járművek** nevű új tábla létrehozásához a Data Lake Storage Gen1 mintaadatainak használatával:

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

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Hozzáférés a Data Lake Storage Gen1 szolgáltatáshoz HDFS-parancsokkal
Miután konfigurálta a HDInsight-fürtet a Data Lake Storage Gen1 használatára, a HDFS rendszerhéj-parancsokkal elérheti az áruházat.

Ebben a szakaszban lesz SSH a HDInsight Linux-fürt létrehozott és futtatja a HDFS parancsokat.

* Ha Windows-ügyfélprogramot használ az SSH-hoz a fürtbe, olvassa el az [SSH használata Linux alapú Hadoop használatával a HDInsight-on Windows-ból című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Ha Egy Linux kliens t használ a fürtsú SSH-hoz, olvassa el az [SSH használata Linux alapú Hadoop segítségével a HDInsight-on Linuxról](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

A csatlakozás után a következő HDFS fájlrendszer paranccsal sorolja fel a fájlokat a Data Lake Storage Gen1 fiókban.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Ez tartalmazza a Data Lake Storage Gen1-nek korábban feltöltött fájlt.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

A paranccsal `hdfs dfs -put` is feltölthet néhány fájlt a Data `hdfs dfs -ls` Lake Storage Gen1 szolgáltatásba, majd ellenőrizheti, hogy a fájlok feltöltése sikeresen megtörtént-e.

## <a name="see-also"></a>Lásd még:
* [A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portál: HDInsight-fürt létrehozása a Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
