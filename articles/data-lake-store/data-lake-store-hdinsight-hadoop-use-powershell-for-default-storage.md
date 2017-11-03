---
title: "HDInsight-fürtök létrehozásához a Data Lake Store alapértelmezett tárolóként PowerShell használatával |} Microsoft Docs"
description: "Azure PowerShell használatával hozzon létre, és az Azure Data Lake Store a HDInsight-fürtök használata"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 64bd0a3ec0598fd7f78e93e510f0a6443f3edbd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>HDInsight-fürtök létrehozásához a Data Lake Store alapértelmezett tárolóként PowerShell használatával
> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Használja a Powershellt (tárhely)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Megtudhatja, hogyan használhatja az Azure Powershellt Azure HDInsight-fürtök konfigurálása az Azure Data Lake Store, alapértelmezett tárolóként. A HDInsight-fürtök létrehozása a Data Lake Store további tárolóként útmutatásért lásd: [HDInsight-fürtök létrehozása a Data Lake Store további tárolóként](data-lake-store-hdinsight-hadoop-use-powershell.md).

Az alábbiakban a HDInsight a Data Lake Store használatára vonatkozó szempontokat:

* A HDInsight-fürtök létrehozása a Data Lake Store alapértelmezett tárolóként hozzáférési beállítás HDInsight 3.5-ös és 3.6 verzió érhető el.

* Létrehozhat HDInsight-fürtök hozzáféréssel rendelkező Data Lake Store, alapértelmezett tárolási *nem érhető el* a HDInsight prémium fürtök.

A PowerShell használatával a Data Lake Store működéséhez HDInsight konfigurálásához kövesse az utasításokat a következő öt szakaszokban.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag megkezdése előtt győződjön meg arról, hogy teljesülnek-e az alábbi követelményeknek:

* **Azure-előfizetés**: Ugrás [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure PowerShell 1.0-ás vagy újabb**: lásd: [telepítése és konfigurálása a PowerShell](/powershell/azure/overview).
* **A Windows Software Development Kit (SDK)**: Windows SDK telepítéséhez, [letölti és a Windows 10-eszközök](https://dev.windows.com/en-us/downloads). Az SDK segítségével hozzon létre egy biztonsági tanúsítványt.
* **Az Azure Active Directory szolgáltatás egyszerű**: Ez az oktatóanyag ismerteti, hogyan lehet egy egyszerű szolgáltatás létrehozása az Azure Active Directory (Azure AD). Azonban szeretne létrehozni egy egyszerű szolgáltatást, akkor az Azure AD rendszergazdai jogokkal kell rendelkeznie. Ha Ön rendszergazda, hagyja ki ezt az előfeltételt, és az oktatóanyag folytatásához.

    >[!NOTE]
    >Létrehozhat egy szolgáltatás egyszerű csak akkor, ha az Azure AD-rendszergazdaként. Az Azure AD rendszergazdának létre kell hoznia egy szolgáltatás egyszerű HDInsight-fürtök létrehozása a Data Lake Store előtt. A szolgáltatás egyszerű a tanúsítvánnyal kell létrehozni a [hozzon létre egy egyszerű tanúsítvány](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Data Lake Store-fiók létrehozása
A Data Lake Store-fiók létrehozásához tegye a következőket:

1. Az asztalon nyisson meg egy PowerShell-ablakot, és adja meg az alábbi részletek. Amikor bejelentkeznek, jelentkezzen be az előfizetés rendszergazdáihoz vagy tulajdonosok egyikeként kéri. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Ha a Data Lake Store erőforrás-szolgáltató regisztrálása és hasonló hibaüzenetet kap `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, az előfizetés nem feltétlenül szerepel az engedélyezési listán a Data Lake Store. Ahhoz, hogy az Azure-előfizetéshez a Data Lake Store nyilvános előzetes verzióhoz, kövesse az utasításokat a [Ismerkedés az Azure Data Lake Store az Azure portál használatával](data-lake-store-get-started-portal.md).
    >

2. Data Lake Store-fiók tartozik egy Azure-erőforráscsoportot. Először hozzon létre egy erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Ez hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Data Lake Store-fiók létrehozása. A megadott fiók neve csak kisbetűket és számokat kell tartalmaznia.

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

4. A Data Lake Store alapértelmezett tárolási szükség van, hogy adjon meg egy legfelső szintű elérési utat, amelyhez a fürt fájlokat másolni a fürt létrehozása során. Egy legfelső szintű elérési útját, amely létrehozásához **/fürtök/hdiadlcluster** a kódrészletet, a következő parancsmag használatával:

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>A szerepköralapú hozzáférés-Data Lake Store-hitelesítés beállítása
Minden Azure-előfizetés nem tartozik az Azure AD entitás. Felhasználók és a szolgáltatások, amelyek az előfizetéshez kapcsolódó erőforrásokat elérni az Azure-portálon vagy az Azure Resource Manager API-t először hitelesítenie kell magát az Azure AD. Hozzáférés az Azure-előfizetések és-szolgáltatások egy Azure-erőforrás a megfelelő szerepkört hozzárendelésével. Szolgáltatások esetén egy egyszerű szolgáltatás azonosítja a szolgáltatást az Azure ad-ben.

Ez a szakasz bemutatja, hogyan alkalmazásszolgáltatás, például a HDInsight hozzáférést az Azure-erőforrás (a Data Lake Store-fiók korábban létrehozott) megadását. Ehhez az alkalmazás és a PowerShell hozzá hozzárendelése szerepkörök egyszerű szolgáltatás létrehozása.

Active Directory-hitelesítés az Azure Data Lake beállításához hajtsa végre a következő két szakasz a feladatok.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Győződjön meg arról, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) ebben a szakaszban a lépések végrehajtása előtt. Kell is létrehozott egy könyvtárat, például a *C:\mycertdir*, ahol a tanúsítvány létrehozását.

1. A PowerShell ablakban nyissa meg a helyet, amelyre telepítette a Windows SDK (általában *C:\Program Files (x86) \Windows Kits\10\bin\x86*), és használja a [MakeCert] [ makecert] segédprogram egy önaláírt tanúsítványt és a titkos kulcs létrehozása. Az alábbi parancsokat használja:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    A rendszer bekéri a titkos kulcsok jelszavának megadása. Miután a parancs végrehajtása sikeres, megtekintheti az **CertFile.cer** és **mykey.pvk** megadott tanúsítvány könyvtárban található.
2. Használja a [Pvk2Pfx] [ pvk2pfx] segédprogram a MakeCert létrehozott .pvk és .cer fájl átalakítása egy .pfx fájlba. Futtassa az alábbi parancsot:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri, adja meg a korábban megadott titkos kulcs jelszava. A megadott érték a **-po** paramétere a jelszavát, amelyet a .pfx fájl van társítva. A parancs sikeres befejezése után, emellett meg kell jelennie egy **CertFile.pfx** megadott tanúsítvány könyvtárban található.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Hozzon létre egy Azure AD és az egyszerű szolgáltatás
Ebben a szakaszban egy egyszerű szolgáltatást az Azure AD-alkalmazás létrehozása, szerepkör hozzárendelése az egyszerű szolgáltatás, és hitelesítse magát a szolgáltatás egyszerű, adja meg a tanúsítványt. Alkalmazás létrehozása az Azure ad-ben, a következő parancsokat:

1. A PowerShell-konzolablakot illessze be a következő parancsmagokat. Győződjön meg arról, hogy a értéket állít be a **- DisplayName** tulajdonság értéke egyedi. Az értékek **- kezdőlap** és **- IdentiferUris** helyőrző értékeket, és nem ellenőrzi.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

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
2. Hozzon létre egy egyszerű szolgáltatás által az alkalmazás azonosítójával.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Adjon a szolgáltatás egyszerű hozzáférést a Data Lake Store legfelső szintű és a legfelső szintű található, amely korábban meghatározott összes mappa. A következő parancsmagokat használja:

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Egy HDInsight Linux-fürt létrehozása a Data Lake Store az alapértelmezett tároló

Ebben a szakaszban egy HDInsight Hadoop Linux fürt létrehozása a Data Lake Store az alapértelmezett tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Store ugyanazon a helyen kell lennie.

1. A bérlő előfizetés-azonosító lekérése, és a későbbi használat céljából tárolja.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. A HDInsight-fürt létrehozása a következő parancsmag használatával:

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
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

    A parancsmag sikeres befejezése után, akkor a fürt részleteket felsoroló kimenetnek kell megjelennie.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>A HDInsight-fürt használata a Data Lake Store teszt feladatok futtatása
Miután konfigurálta a HDInsight-fürtöt, a teszt feladatokat, és győződjön meg arról, hogy hozzáférhessen Data Lake Store is futtathatja. Ehhez az szükséges, hozzon létre egy táblát, amely a rendelkezésre álló a Data Lake Store: mintaadatok használ egy minta Hive feladat futtatása  *<cluster root>/example/data/sample.log*.

Ebben a szakaszban a Secure Shell (SSH) kapcsolat a HDInsight Linux fürthöz létrehozott, és egy minta Hive-lekérdezést futtassa.

* Ha egy Windows ügyfél használ a fürthöz az SSH-kapcsolat létrehozásához, lásd: [SSH használata a HDInsight Windows Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha egy Linux-ügyfél segítségével ellenőrizze a fürthöz az SSH-kapcsolat, lásd: [SSH használata a HDInsight Linux Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Miután kiválasztotta a kapcsolatot, a következő parancs használatával indítsa el a Hive parancssori felület (CLI):

        hive
2. Adja meg az alábbi állításokat annak nevű új tábla létrehozása a parancssori felület használatával **járművekről gyűjtött** által megadott mintaadatokat használja a Data Lake Store-ban:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Az SSH-konzolon megtekintheti a lekérdezés eredményében.

    >[!NOTE]
    >A mintaadatok az előző CREATE TABLE parancsban szereplő elérési útja `adl:///example/data/`, ahol `adl:///` fürt gyökere. A példa a fürt legfelső szintű megadott ebben az oktatóanyagban a parancs a következő `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. A rövidebb alternatív használja, vagy adja meg a fürt legfelső szintű teljes elérési útja.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Hozzáférés Data Lake Store HDFS parancs használatával
Miután konfigurálta a HDInsight-fürt Data Lake Store használatára, használhatja a Hadoop elosztott fájlrendszerrel (HDFS) felületparancsokat-tárolóhoz.

Ebben a szakaszban a HDInsight Linux fürthöz létrehozott egy SSH-kapcsolatot, és a HDFS parancs futtatásával.

* Ha egy Windows ügyfél használ a fürthöz az SSH-kapcsolat létrehozásához, lásd: [SSH használata a HDInsight Windows Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha egy Linux-ügyfél segítségével ellenőrizze a fürthöz az SSH-kapcsolat, lásd: [SSH használata a HDInsight Linux Linux-alapú Hadooppal](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Miután végzett a kapcsolatot, a Data Lake Store található fájlok listázása a következő paranccsal HDFS fájl rendszer.

    hdfs dfs -ls adl:///

Használhatja a `hdfs dfs -put` parancs egyes fájlok feltöltése a Data Lake Store-ba, és ezután `hdfs dfs -ls` ellenőrzése, hogy a fájlok sikeresen feltöltve.

## <a name="see-also"></a>Lásd még:
* [Azure-portálon: Data Lake Store használatára HDInsight-fürtök létrehozása](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
