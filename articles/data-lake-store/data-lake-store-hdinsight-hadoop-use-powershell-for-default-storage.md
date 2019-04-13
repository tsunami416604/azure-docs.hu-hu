---
title: HDInsight fürtök létrehozása az Azure Data Lake Storage Gen1 alapértelmezett tárolóként PowerShell-lel |} A Microsoft Docs
description: Az Azure PowerShell használatával létrehozása és használata HDInsight-fürtök az Azure Data Lake Storage Gen1
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
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524089"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-as-default-storage-by-using-powershell"></a>HDInsight fürtök létrehozása az Azure Data Lake Storage Gen1 alapértelmezett tárolóként PowerShell-lel

> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használata (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Használja a Powershellt (további tároló)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Megtudhatja, hogyan használhatja az Azure Powershellt Azure HDInsight-fürtök konfigurálása az Azure Data Lake Storage Gen1, az alapértelmezett tároló. A Data Lake Storage Gen1 kiegészítő tárolóként egy HDInsight-fürt létrehozásával kapcsolatos útmutatóért lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1 kiegészítő tárolóként](data-lake-store-hdinsight-hadoop-use-powershell.md).

Az alábbiakban néhány HDInsight használata a Data Lake Storage Gen1 fontos szempontjai:

* A Data Lake Storage Gen1 alapértelmezett tárolóként hozzáféréssel rendelkező HDInsight-fürtök létrehozásának lehetősége a HDInsight 3.5-ös és 3.6-os verzió érhető el.

* Létrehozhat HDInsight-fürt a hozzáférést a Data Lake Storage Gen1, alapértelmezett tároló *nem érhető el* HDInsight prémium fürtök.

HDInsight Data Lake Storage Gen1 dolgozhat a PowerShell-lel konfigurálásához kövesse az utasításokat a következő öt szakaszokban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez az oktatóanyag elkezdéséhez győződjön meg arról, hogy megfelel-e az alábbi követelményeknek:

* **Azure-előfizetés**: Lépjen a [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0-ás vagy újabb**: Lásd: [telepítése és konfigurálása PowerShell](/powershell/azure/overview).
* **Windows szoftverfejlesztői készlet (SDK)**: Telepítse a Windows SDK-t, lépjen a [letölti és a Windows 10-es eszközök](https://dev.windows.com/downloads). Az SDK segítségével hozzon létre egy biztonsági tanúsítványt.
* **Az Azure Active Directory egyszerű szolgáltatás**: Ez az oktatóanyag leírja, hogyan hozhat létre egyszerű szolgáltatást az Azure Active Directoryban (Azure AD). Azonban egy szolgáltatásnév létrehozásához, hogy Azure AD-rendszergazda kell lennie. Ha Ön rendszergazda, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyagot.

    >[!NOTE]
    >Létrehozhat egy szolgáltatás egyszerű csak akkor, ha az Azure AD-rendszergazdaként. Az Azure AD-rendszergazda kell egy egyszerű szolgáltatás létrehozása a Data Lake Storage Gen1 egy HDInsight-fürt létrehozása előtt. Az egyszerű szolgáltatás léteznie kell egy tanúsítványt, az ismertetett módon [egyszerű szolgáltatás létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-storage-gen1-account"></a>Hozzon létre egy Data Lake Storage Gen1 fiókot

Hozzon létre egy Data Lake Storage Gen1 fiókot, tegye a következőket:

1. Az asztalon nyissa meg egy PowerShell-ablakot, és adja meg az alábbi kódrészletek. Amikor a rendszer kéri, jelentkezzen be, jelentkezzen be az előfizetés adminisztrátorainak vagy tulajdonosok egyikeként. 

        # Sign in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Ha a Data Lake Storage Gen1 erőforrás-szolgáltató regisztrálása és az alábbihoz hasonló hibaüzenetet `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid`, az előfizetés nem lehet a Data Lake Storage Gen1 szerepel az engedélyezési listán. Ahhoz, hogy az Azure-előfizetését a Data Lake Storage Gen1, kövesse a [használatának első lépései az Azure Data Lake Storage Gen1 az Azure Portalon](data-lake-store-get-started-portal.md).
    >

2. Egy Data Lake Storage Gen1 fiókkal társítva az Azure-erőforráscsoportot. Először hozzon létre egy erőforráscsoportot.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Egy a következőhöz hasonló kimenetnek kell megjelennie:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Hozzon létre egy Data Lake Storage Gen1 fiókot. A megadott fiók neve csak kisbetűket és számokat kell tartalmaznia.

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

4. Az alapértelmezett tároló használata a Data Lake Storage Gen1 megköveteli, hogy a gyökér elérési útját, amelyhez a fürtre jellemző fájlok másolását a fürt létrehozásakor adja meg. A gyökér elérési útját, amely létrehozása **/fürtök/hdiadlcluster** a kódrészletben használja a következő parancsmagokat:

        $myrootdir = "/"
        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Hitelesítés a Data Lake Storage Gen1 történő, szerepkörön alapuló hozzáférés beállítása
Minden Azure-előfizetés entitáshoz társítva az Azure ad-ben. Felhasználók és a szolgáltatások az Azure Portalon vagy az Azure Resource Manager API használatával az előfizetés-erőforrásokhoz hozzáférő először hitelesíteniük kell az Azure ad-ben. Hozzáférés az Azure-előfizetések és-szolgáltatások az Azure-erőforrás a megfelelő szerepkör hozzárendelésével. A szolgáltatások esetében egy egyszerű szolgáltatás a szolgáltatás azonosítja az Azure ad-ben.

Ez a szakasz bemutatja, hogyan adja meg az alkalmazásszolgáltatás, például a HDInsight, a hozzáférés az Azure-erőforrás (a Data Lake Storage Gen1 fiók, amelyet korábban hozott létre). Ehhez az alkalmazás pedig a Powershellen keresztül rá hozzárendelése szerepkör szolgáltatásnév létrehozásához.

Active Directory-hitelesítés a Data Lake Storage Gen1 beállításához hajtsa végre a következő két szakasz a feladatokat.

### <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása
Ellenőrizze, hogy [Windows SDK](https://dev.windows.com/en-us/downloads) telepítve van a jelen szakaszban ismertetett lépések végrehajtása előtt. Kell is létrehozott egy könyvtárat, például *C:\mycertdir*, ahol létrehozhatja a tanúsítványt.

1. A PowerShell ablakban nyissa meg a helyet, amelyre telepítette a Windows SDK (általában *C:\Program Files (x86) \Windows Kits\10\bin\x86*), és használja a [MakeCert] [ makecert] Hozzon létre egy önaláírt tanúsítványt és a egy titkos kulcsot segédprogramot. A következő parancsokat használja:

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Adja meg a titkos kulcs jelszavát kéri. Miután a parancs végrehajtása sikeres, megjelenik **CertFile.cer** és **mykey.pvk** a megadott tanúsítvány-könyvtárban.
2. Használja a [Pvk2Pfx] [ pvk2pfx] segédprogramot a MakeCert által létrehozott .pvk, és .cer fájlokat átalakítása egy .pfx fájlba. Futtassa az alábbi parancsot:

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Amikor a rendszer kéri, adja meg a korábban megadott titkos kulcs jelszava. A megadott érték a **-po** paraméter értéke a PFX-fájllal tartozó jelszót. Miután a parancs végrehajtása sikeresen befejeződött, is meg kell jelennie egy **CertFile.pfx** a megadott tanúsítvány-könyvtárban.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Hozzon létre egy Azure ad-ben és a egy egyszerű szolgáltatást
Ebben a szakaszban egy egyszerű szolgáltatást az Azure AD-alkalmazás létrehozása, rendelhet egy szerepkört a szolgáltatásnévhez, és hitelesítse magát a szolgáltatásnév tanúsítvány megadásával. Alkalmazás létrehozása az Azure ad-ben, futtassa a következő parancsokat:

1. Illessze be a következő parancsmagok a PowerShell-konzolablakot. Győződjön meg arról, hogy az érték azt adja meg a **– DisplayName** tulajdonság egyedi. A tartozó értékeket **- kezdőlap** és **- IdentiferUris** helyőrző értékeket, és nem ellenőrzi.

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
2. Az alkalmazás azonosítója. az egyszerű szolgáltatás létrehozása

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Adja meg a szolgáltatásnév hozzáférhessen a Data Lake Storage Gen1 legfelső szintű és a legfelső szintű korábban megadott elérési úton a mappához. Használja a következő parancsmagokat:

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-the-default-storage"></a>HDInsight Linux-fürt létrehozása a Data Lake Storage Gen1 az alapértelmezett tárolóként

Ebben a szakaszban egy HDInsight Hadoop Linux fürt létrehozása a Data Lake Storage Gen1 az alapértelmezett tárolóként. Ebben a kiadásban a HDInsight-fürt és a Data Lake Storage Gen1 ugyanazon a helyen kell lennie.

1. A bérlő előfizetés-azonosító lekéréséhez, és a későbbi használat céljából tárolja.

        $tenantID = (Get-AzContext).Tenant.TenantId

2. A HDInsight-fürt létrehozása a következő parancsmag használatával:

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

    A parancsmag sikeres befejezése után, amely felsorolja a fürt részletes adatai egy kimenetnek kell megjelennie.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>A HDInsight-fürt használata a Data Lake Storage Gen1 tesztelési feladatok futtatása
Egy HDInsight-fürt konfigurálását követően, győződjön meg arról, hogy hozzá tudjon férni a Data Lake Storage Gen1 és tesztelési feladatok futtatásához. Ehhez hozzon létre egy táblát, amely a mintaadatokat, amely már elérhető a Data Lake Storage Gen1, használja a Hive mintafeladat futtatása  *\<fürt legfelső szintű > /example/data/sample.log*.

Ebben a szakaszban választja ki a létrehozott HDInsight Linux-fürtöt és egy Secure Shell (SSH) kapcsolat, és a egy minta Hive-lekérdezés futtatásával.

* Ha egy Windows-ügyfél, hogy a fürthöz az SSH-kapcsolatot használ, tekintse meg [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha egy Linux-ügyfél, hogy a fürthöz az SSH-kapcsolatot használ, tekintse meg [az SSH használata a Linux-based Hadoop on HDInsight Linux rendszerben](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Miután elvégezte a kapcsolatot, indítsa el a Hive parancssori felület (CLI) használatával a következő parancsot:

        hive
2. A CLI használatával adja meg az alábbi utasításokat, hozzon létre egy új táblát nevű **járművek** a mintaadatok használatával a Data Lake Storage Gen1:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Az SSH-konzolon megtekintheti a lekérdezés kimenetelét.

    >[!NOTE]
    >A mintaadatokat az előző CREATE TABLE parancsban elérési útja `adl:///example/data/`, ahol `adl:///` a fürt legfelső szintű. A példában a fürt legfelső szintű megadott ebben az oktatóanyagban a parancs az alábbi `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Használja a rövidebb alternatív, vagy adja meg a fürt legfelső szintű teljes elérési útja.
    >

## <a name="access-data-lake-storage-gen1-by-using-hdfs-commands"></a>Hozzáférés Data Lake Storage Gen1 HDFS parancs használatával
Miután konfigurálta a HDInsight-fürt használata a Data Lake Storage Gen1, használhatja a Hadoop elosztott fájlrendszer (HDFS) rendszerhéj-parancsok az áruház.

Ebben a szakaszban választja ki a létrehozott HDInsight Linux-fürtöt az SSH-kapcsolatot, és ezt követően a HDFS-parancsokat futtatni.

* Ha egy Windows-ügyfél, hogy a fürthöz az SSH-kapcsolatot használ, tekintse meg [az SSH használata a Linux-alapú Hadooppal a HDInsight, a Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Ha egy Linux-ügyfél, hogy a fürthöz az SSH-kapcsolatot használ, tekintse meg [az SSH használata a Linux-based Hadoop on HDInsight Linux rendszerben](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Miután végzett a kapcsolatot, listázza a Data Lake Storage Gen1 lévő fájlokat a következő paranccsal HDFS fájl rendszer.

    hdfs dfs -ls adl:///

Is használhatja a `hdfs dfs -put` parancsot néhány fájlok feltöltése a Data Lake Storage Gen1, és hogyan `hdfs dfs -ls` ellenőrizze, hogy a fájl sikeresen feltöltve.

## <a name="see-also"></a>Lásd még
* [Az Azure HDInsight-fürtök használata a Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Az Azure Portalon: Hozzon létre egy HDInsight-fürt használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
