---
title: HDInsight-fürtök létrehozása az Azure Data Lake Storage Gen1 alapértelmezett tárolóként a PowerShell használatával | Microsoft Dokumentumok"
description: HDInsight-fürtök létrehozása és használata az Azure Data Lake Storage Gen1 használatával az Azure PowerShell használatával
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c57a45145d9abc43d0ca79839ea297dfc025db9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66161419"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>HDInsight-fürtök létrehozása az Azure Data Lake Storage Gen1 alapértelmezett tárolóként a PowerShell használatával

> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használata (alapértelmezett tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Megtudhatja, hogy az Azure PowerShell használatával hogyan konfigurálhatja az Azure HDInsight-fürtöket az Azure Data Lake Storage Gen1 alapértelmezett tárolóként. A HDInsight-fürt további tárolóként való létrehozásával kapcsolatos tudnivalókról további tárolóként olvassa el a [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört további tárolóként.](data-lake-store-hdinsight-hadoop-use-powershell.md)

Az alábbiakban a HDInsight data lake storage gen1 szolgáltatással való használatának néhány fontos szempontját olvashatja:

* A HDInsight-fürtök alapértelmezett tárolóként való elérésével rendelkező HDInsight-fürtök létrehozása a HDInsight 3.5-ös és 3.6-os verziójához érhető el.

* A HDInsight-fürtök alapértelmezett tárolóként való elérésével rendelkező HDInsight-fürtök létrehozása *nem érhető el* a HDInsight Prémium szintű fürtök számára.

Ha a HDInsightot a Data Lake Storage Gen1 powershell használatával történő együttműködésre szeretné konfigurálni, kövesse a következő öt szakasz utasításait.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag megkezdése előtt győződjön meg arról, hogy megfelel az alábbi követelményeknek:

* **Azure-előfizetés**: Nyissa meg az [Ingyenes Azure-próbaverziót.](https://azure.microsoft.com/pricing/free-trial/)
* **Azure PowerShell 1.0-s vagy nagyobb:** A [PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakörben látható.
* **Windows szoftverfejlesztői készlet (SDK):** A Windows SDK telepítéséhez nyissa meg [a Letöltések és eszközök a Windows 10-hez című témakört.](https://dev.windows.com/downloads) Az SDK biztonsági tanúsítvány létrehozására szolgál.
* **Azure Active Directory egyszerű szolgáltatásért:** Ez az oktatóanyag ismerteti, hogyan hozhat létre egy egyszerű szolgáltatás az Azure Active Directoryban (Azure AD). Azonban egy egyszerű szolgáltatás létrehozásához, az Azure AD-rendszergazda kell lennie. Ha Ön rendszergazda, kihagyhatja ezt az előfeltételt, és folytathatja az oktatóanyaggal.

    >[!NOTE]
    >Csak akkor hozhat létre egyszerű szolgáltatást, ha Ön az Azure AD-rendszergazda. Az Azure AD-rendszergazdának létre kell hoznia egy egyszerű szolgáltatás, mielőtt hdinsight-fürtöt hozhat létre a Data Lake Storage Gen1 használatával. A szolgáltatásnév kell létrehozni egy tanúsítvány, ahogy azt a [Szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1-fiók létrehozása

Data Lake Storage Gen1 fiók létrehozásához tegye a következőket:

1. Az asztalról nyisson meg egy PowerShell-ablakot, majd írja be az alábbi kódrészleteket. Amikor a rendszer kéri a bejelentkezést, jelentkezzen be az előfizetés egyik rendszergazdájaként vagy tulajdonosaként. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Ha regisztrálja a Data Lake Storage Gen1 erőforrás-szolgáltatót, és a hez hasonló hibaüzenetet kap, előfordulhat, hogy `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`az előfizetés nem szerepel a Data Lake Storage Gen1 engedélyezési listáján. Az Azure-előfizetés engedélyezése a Data Lake Storage Gen1 szolgáltatáshoz kövesse az [Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)című útmutató utasításait az Azure Portal használatával.
    >

2. A Data Lake Storage Gen1 fiók egy Azure-erőforráscsoport társított. Először hozzon létre egy erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    A következőhöz hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott fióknév csak kisbetűket és számokat tartalmazhat.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 name>"
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

4. A Data Lake Storage Gen1 alapértelmezett tárolóként való használata megköveteli egy gyökérelérési utat, amelybe a fürtspecifikus fájlokat másolja a fürt létrehozása során. Gyökérelérési út létrehozásához, amely **a kódrészletben /clusters/hdiadlcluster,** használja a következő parancsmagokat:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Hitelesítés beállítása a Data Lake Storage Gen1 szerepköralapú eléréséhez
Minden Azure-előfizetés egy Azure AD entitáshoz van társítva. Azok a felhasználók és szolgáltatások, amelyek az Azure Portalon vagy az Azure Resource Manager API-n keresztül érik el az előfizetési erőforrásokat, először hitelesíteniük kell magukat az Azure AD-vel. Az Azure-előfizetések és -szolgáltatások számára a hozzáférést úgy biztosítják, hogy hozzárendelik a megfelelő szerepkört egy Azure-erőforráshoz. A szolgáltatások esetében egy egyszerű szolgáltatás azonosítja a szolgáltatást az Azure AD-ben.

Ez a szakasz bemutatja, hogyan adhat hozzáférést egy alkalmazásszolgáltatásnak, például a HDInsightnak egy Azure-erőforráshoz (a korábban létrehozott Data Lake Storage Gen1 fiókhoz). Ehhez hozzon létre egy egyszerű szolgáltatásaz alkalmazáshoz, és szerepkörök hozzárendelése a PowerShellen keresztül.

A Data Lake Storage Gen1 Active Directory-hitelesítésének beállításához hajtsa végre a következő két szakaszban végzett feladatokat.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Mielőtt folytatná a szakaszlépéseit, győződjön meg arról, hogy telepítve van a [Windows SDK.](https://dev.windows.com/en-us/downloads) Létre kell hoznia egy könyvtárat is, például *A C:\mycertdir könyvtárat,* ahol létrehozza a tanúsítványt.

1. A PowerShell ablakban nyissa meg azt a helyet, ahol a Windows SDK-t telepítette (általában *C:\Program Files (x86)\Windows Kits\10\bin\x86),* és a [MakeCert][makecert] segédprogrammal hozzon létre egy önaláírt tanúsítványt és egy személyes kulcsot. Az alábbi parancsokat használja:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    A rendszer kéri a személyes kulcs jelszavának megadását. A parancs sikeres végrehajtása után a **CertFile.cer** és a **mykey.pvk** fájlnak kell lennie a megadott tanúsítványkönyvtárban.
2. A [Pvk2Pfx][pvk2pfx] segédprogrammal konvertálja a MakeCert által létrehozott .pvk és .cer fájlokat .pfx fájllá. Futtassa az alábbi parancsot:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri, írja be a korábban megadott személyes kulcs jelszót. A **-po** paraméterhez megadott érték a .pfx fájlhoz társított jelszó. A parancs sikeres befejezése után a megadott tanúsítványkönyvtárban egy **CertFile.pfx** fájl is megjelenik.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Azure AD és egyszerű szolgáltatás létrehozása
Ebben a szakaszban hozzon létre egy egyszerű szolgáltatás egy Azure AD-alkalmazás, szerepkör hozzárendelése a szolgáltatásnév, és hitelesíti magát, mint a szolgáltatás egyszerű egy tanúsítvány biztosításával. Ha alkalmazást szeretne létrehozni az Azure AD-ben, futtassa a következő parancsokat:

1. Illessze be a következő parancsmagokat a PowerShell konzolablakába. Győződjön meg arról, hogy a **-DisplayName** tulajdonsághoz megadott érték egyedi. A **-HomePage** és **az -IdentiferUris** értékek helyőrző értékek, és nem ellenőrzöttek.

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
3. Adja meg a szolgáltatás egyszerű hozzáférést a Data Lake Storage Gen1 gyökér és az összes mappát a gyökér elérési, korábban megadott. Használja a következő parancsmagokat:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>HdInsight Linux-fürt létrehozása alapértelmezett tárolóként a Data Lake Storage Gen1 szolgáltatással

Ebben a szakaszban hozzon létre egy HDInsight Hadoop Linux-fürt a Data Lake Storage Gen1 alapértelmezett tárolóként. Ebben a kiadásban a HDInsight-fürtnek és a Data Lake Storage Gen1-nek ugyanazon a helyen kell lennie.

1. Az előfizetés bérlői azonosítójának lekérése, és későbbi használatra történő tárolása.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. Hozza létre a HDInsight-fürtöt a következő parancsmagokkal:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStorageGen1Name                         # Data Lake Storage Gen1 account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    A parancsmag sikeres befejezése után meg kell jelennie egy kimenetnek, amely felsorolja a fürt részleteit.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Tesztelési feladatok futtatása a HDInsight-fürtön a Data Lake Storage Gen1 használatához
Miután konfigurált egy HDInsight-fürtöt, futtathat rajta tesztfeladatokat, hogy biztosan hozzáférhessen a Data Lake Storage Gen1 szolgáltatáshoz. Ehhez futtasson egy minta Hive-feladatot egy olyan tábla létrehozásához, amely a Data Lake Storage Gen1-ben már elérhető mintaadatokat használja * \<a fürtgyökér>/example/data/sample.log.*

Ebben a szakaszban egy Biztonságos rendszerhéj (SSH) kapcsolatot hoz létre a létrehozott HDInsight Linux-fürtbe, majd futtat egy minta Hive-lekérdezést.

* Ha Windows-ügyfélprogramot használ SSH-kapcsolat létesítésére a fürtben, olvassa el az [SSH használata Linux alapú Hadoop használatával a HDInsight-on Windows-ból című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Ha Linux-ügyfélprogramot használ SSH-kapcsolat létesítéséhez a fürtben, olvassa el az [SSH használata Linux alapú Hadoop használatával linuxos HDInsight-on.](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. A kapcsolat létrejötte után indítsa el a Hive parancssori felületet (CLI) a következő paranccsal:

        hive
2. A CLI segítségével adja meg a következő állításokat egy járművek **nevű** új tábla létrehozásához a Data Lake Storage Gen1 mintaadatainak használatával:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Meg kell jelennie a lekérdezés kimenetét az SSH konzolon.

    >[!NOTE]
    >Az előző CREATE TABLE parancsban a `adl:///example/data/`mintaadatok elérési útja a , ahol `adl:///` a fürt gyökér. Az oktatóanyagban megadott fürtgyökér példáját követve a `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`parancs a . Használhatja a rövidebb alternatívát, vagy megadhatja a fürtgyökér teljes elérési útját.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Hozzáférés a Data Lake Storage Gen1 szolgáltatáshoz HDFS-parancsokkal
Miután konfigurálta a HDInsight-fürtöt a Data Lake Storage Gen1 használatára, a Hadoop distributed File System (HDFS) rendszerhéj-parancsokkal elérheti az áruházat.

Ebben a szakaszban sSH-kapcsolatot hoz létre a létrehozott HDInsight Linux-fürtbe, majd futtatja a HDFS-parancsokat.

* Ha Windows-ügyfélprogramot használ SSH-kapcsolat létesítésére a fürtben, olvassa el az [SSH használata Linux alapú Hadoop használatával a HDInsight-on Windows-ból című témakört.](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)
* Ha Linux-ügyfélprogramot használ SSH-kapcsolat létesítéséhez a fürtben, olvassa el az [SSH használata Linux alapú Hadoop használatával linuxos HDInsight-on.](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

A kapcsolat létrejötte után sorolja fel a fájlokat a Data Lake Storage Gen1 alkalmazásban a következő HDFS fájlrendszer parancs használatával.

    hdfs dfs -ls adl:///

A paranccsal `hdfs dfs -put` is feltölthet néhány fájlt a Data `hdfs dfs -ls` Lake Storage Gen1 szolgáltatásba, majd ellenőrizheti, hogy a fájlok feltöltése sikeresen megtörtént-e.

## <a name="see-also"></a>Lásd még
* [A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Azure portal: Hozzon létre egy HDInsight-fürtöt a Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
