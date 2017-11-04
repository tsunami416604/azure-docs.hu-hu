---
title: "A HDInsight az Azure Resource Manager eszközeinek át |} Microsoft Docs"
description: "Azure Resource Manager Fejlesztőeszközök a HDInsight-fürtök áttelepítése"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 9fec253fbf9136cf86a84a6c7b407ea522996226
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>A Fejlesztőeszközök Azure Resource Manager-alapú HDInsight-fürtök áttelepítése

A HDInsight-ból kivezettük való Azure Service Manager ASM-alapú eszközök hdinsight. Ha használta az Azure PowerShell, az Azure parancssori felület vagy a HDInsight .NET SDK a HDInsight-fürtök együttműködni, hosszúan az Azure Resource Manager ARM-alapú verziói PowerShell parancssori felület és továbbítja a .NET SDK használatával. Ez a cikk mutatók áttelepítése az új ARM-alapú módszert biztosít. Megfelelő esetben ez a cikk is rámutat, a címterület-kezelési és ARM közötti különbségekről megközelítések hdinsight.

> [!IMPORTANT]
> A címterület-kezelési támogatás PowerShell parancssori felület, és a .NET SDK nem küld a **2017. január 1.**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Az Azure CLI áttelepítése az Azure Resource Manager számára
Az Azure parancssori felület most alapértelmezés szerint az Azure Resource Managerrel (ARM) módot, kivéve, ha frissíti a korábbi telepítés; Ebben az esetben előfordulhat, hogy kell használnia a `azure config mode arm` parancs futtatásával váltson az ARM üzemmódban.

Az alapszintű Azure Service Management (ASM) használatával történő együttműködésre a HDInsight az Azure parancssori felület megadott parancsok esetén ugyanazt az ARM; azonban bizonyos paraméterek és kapcsolók előfordulhat, hogy új neve lehet, és nincsenek sok új paraméter elérhető ARM használatakor. Például használhatja `azure hdinsight cluster create` a Azure virtuális hálózat, amely egy fürt, létre kell hozni, vagy Hive- és Oozie metaadattárhoz megadásához.

A HDInsight Azure Resource Manageren keresztül használatához alapvető parancsok a következők:

* `azure hdinsight cluster create`-hoz létre egy új HDInsight-fürt
* `azure hdinsight cluster delete`– egy meglévő HDInsight-fürt törlése
* `azure hdinsight cluster show`– egy meglévő fürthöz kapcsolatos információk megjelenítése
* `azure hdinsight cluster list`– ismerteti az Azure-előfizetés a HDInsight-fürtök

Használja a `-h` kapcsolót, hogy a paraméterek és kapcsolók érhető el minden egyes parancsnál.

### <a name="new-commands"></a>Új parancsok
Elérhető az Azure Resource Manager új parancsok a következők:

* `azure hdinsight cluster resize`-dinamikusan változik adhatja meg a fürt feldolgozó csomópontjainak számát
* `azure hdinsight cluster enable-http-access`-HTTPs-hozzáférést biztosít a fürt (az alapértelmezés)
* `azure hdinsight cluster disable-http-access`-letiltja a HTTPs-hozzáférés a fürthöz
* `azure hdinsight script-action`-parancsokat biztosít létrehozása vagy kezelése Parancsfájlműveletek egy fürtön
* `azure hdinsight config`-parancsok biztosít a konfigurációs fájl létrehozása, amely használható a `hdinsight cluster create` parancs használatával adja meg a konfigurációs adatokat.

### <a name="deprecated-commands"></a>Elavult parancsok
Ha használja a `azure hdinsight job` parancsok elküldeni a HDInsight-fürtjét, feladatok ezek nem állnak rendelkezésre az ARM-parancsok használatával. Ha programozottan feladatok elküldéséhez a HDInsight parancsfájlok van szüksége, helyette használja a HDInsight által biztosított REST API-k. A REST API-k használatával feladatok elküldésekor további információkért lásd a következő dokumentumokat.

* [Hadoop MapReduce-feladatok a HDInsight használata cURL használatával futtassa](hdinsight-hadoop-use-mapreduce-curl.md)
* [A Hadoop Hive-lekérdezések futtatása a HDInsight használata cURL használatával](hdinsight-hadoop-use-hive-curl.md)
* [Pig-feladatokhoz Hadoop on HDInsight használata cURL használatával futtassa](hdinsight-hadoop-use-pig-curl.md)

Információk más módjairól MapReduce futtatásához, struktúra, és interaktív módon sertésfelmérés, lásd: [használata MapReduce a Hadoop on HDInsight](hdinsight-use-mapreduce.md), [használata a hdinsight Hadoop Hive](hdinsight-use-hive.md), és [a Pig használata a hadooppal HDInsight](hdinsight-use-pig.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

* Régi parancs (ASM)-`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Új parancs (ARM)-`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**A fürtök törlésével**

* Régi parancs (ASM)-`azure hdinsight cluster delete myhdicluster`
* Új parancs (ARM)-`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista fürtök**

* Régi parancs (ASM)-`azure hdinsight cluster list`
* Új parancs (ARM)-`azure hdinsight cluster list`

> [!NOTE]
> A parancs megadásával az erőforrás csoport használatával `-g` csak a fürtök ad vissza a megadott erőforráscsoportban.
> 
> 

**Fürt információ megjelenítése**

* Régi parancs (ASM)-`azure hdinsight cluster show myhdicluster`
* Új parancs (ARM)-`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Az Azure PowerShell áttelepítése az Azure Resource Manager számára
Az Azure PowerShell általános információk az Azure Resource Managerrel (ARM) módban található [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md).

Az Azure PowerShell ARM-parancsmagok telepített-mellé a címterület-kezelési parancsmagokkal is lehet. A két mód a parancsmag elkülönítsék névvel.  Az ARM üzemmódban van *AzureRmHDInsight* való hasonlítás parancsmag nevében szereplő *AzureHDInsight* a címterület-kezelési módban.  Például *New-AzureRmHDInsightCluster* vs. *Új AzureHDInsightCluster*. Paraméterek és kapcsolók hírek nevük lehet, hogy legyen, és nincsenek sok új paraméter elérhető ARM használatakor.  Például több parancsmagok szükséges nevű új kapcsoló *- ResourceGroupName*. 

A HDInsight-parancsmagokat használhatja, csatlakozzon az Azure-fiókjával, és az új erőforráscsoport létrehozása:

* Login-AzureRmAccount vagy [válasszon-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Lásd: [hitelesítéséhez az Azure Resource Manager egyszerű szolgáltatás](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Új-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok a Windows PowerShell-konzolban listázásához:

    help *azurermhdinsight*

A következő táblázat a címterület-kezelési parancsmagok és a nevek a ARM üzemmódban.

| Címterület-kezelési parancsmagok | ARM-parancsmagok |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Adja hozzá AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Adja hozzá AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Adja hozzá AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Adja hozzá AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Támogatás-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Támogatás-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[Új AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[Új AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[Új AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[Új AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[Új AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[Új AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Új AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[A visszavonási-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[A visszavonási-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[STOP-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Használjon-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Várjon, amíg-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Új parancsmagok
Az alábbiakban az új parancsmagok, amelyek csak a ARM módban érhető el. 

**A kapcsolódó parancsmagok parancsfájlművelet:**

* **Get-AzureRmHDInsightPersistedScriptAction**: lekérdezi a megőrzött Parancsfájlműveletek fürt és időrendben sorolja fel azokat, vagy egy megadott megőrzött parancsfájl művelet lekérdezi a részletek. 
* **Get-AzureRmHDInsightScriptActionHistory**: a parancsfájlművelet előzményeinek lekérdezi a fürt, fordított időrendben sorolja fel, és lekérdezi a korábban végrehajtott parancsfájlművelet részleteit. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: egy megőrzött parancsfájl művelet eltávolítja a HDInsight-fürtöt.
* **Set-AzureRmHDInsightPersistedScriptAction**: egy megőrzött parancsfájl műveletet kell azelőtti parancsfájlművelet állítja be.
* **Küldje el AzureRmHDInsightScriptAction**: egy új parancsfájlművelet Azure HDInsight-fürtöt ad meg. 

További használati információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md).

**A kapcsolódó parancsmagok Clsuter identitás:**

* **Adja hozzá AzureRmHDInsightClusterIdentity**: ad hozzá egy fürt identitását egy fürt konfigurációs objektumot, hogy a HDInsight-fürt elérhessék az Azure Data Lake tárolja. Lásd: [HDInsight-fürtök létrehozása az Azure PowerShell használatával a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

Régi parancs (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Új parancs (ARM):

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Fürt törlése**

Régi parancs (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Új parancs (ARM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista fürt**

Régi parancs (ASM):

    Get-AzureHDInsightCluster

Új parancs (ARM):

    Get-AzureRmHDInsightCluster 

**Fürt megjelenítése**

Régi parancs (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Új parancs (ARM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Más minták
* [A HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Küldje el a Hive-feladatok](hdinsight-hadoop-use-hive-powershell.md)
* [Küldje el a Pig-feladatokhoz](hdinsight-hadoop-use-pig-powershell.md)
* [Sqoop feladatok elküldéséhez](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Az ARM-alapú HDInsight .NET SDK áttelepítése
Az Azure Szolgáltatáskezelés-alapú [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) elavult. Hosszúan használja az Azure Resource Manager-alapú [(ARM) a HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx). A következő ASM-alapú HDInsight-csomagok elavulttá válnak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ez a témakör mutatókat biztosít a további információt az ARM-alapú SDK segítségével bizonyos feladatok elvégzéséhez.

| Hogyan... az ARM-alapú HDInsight SDK használatával | Hivatkozások |
| --- | --- |
| .NET SDK használatával a HDInsight-fürtök létrehozása |Lásd: [HDInsight-fürtök létrehozása .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| A fürt parancsfájlművelet .NET SDK-val testreszabása |Lásd: [testreszabása HDInsight Linux clusters parancsfájlművelet használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Alkalmazások interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [.NET SDK használatával futtassa Hive lekérdezések](hdinsight-hadoop-use-hive-dotnet-sdk.md). Ebben a cikkben a kódrészletet használja az interaktív hitelesítési módszerrel. |
| Alkalmazások nem interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [a HDInsight nem interaktív alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK használatával Hive feladat elküldése |Lásd: [elküldeni a Hive-feladatok](hdinsight-hadoop-use-hive-dotnet-sdk.md) |
| Elküldeni a Pig feladatot .NET SDK használatával |Lásd: [elküldeni a Pig-feladatokhoz](hdinsight-hadoop-use-pig-dotnet-sdk.md) |
| .NET SDK használatával Sqoop feladat elküldése |Lásd: [nyújt Sqoop feladatok](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista HDInsight-fürtök .NET SDK használatával |Lásd: [lista HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| A HDInsight-fürtök .NET SDK használatával méretezése |Lásd: [méretezési HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| A HDInsight-fürtök .NET SDK használatával engedélyezéshez/visszavonáshoz elérésére |Lásd: [Grant/revoke access HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP felhasználó hitelesítő adatait a HDInsight-fürtök .NET SDK használatával |Lásd: [frissítés HTTP felhasználói hitelesítő adatokat a HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Az alapértelmezett tárfiók keresése a HDInsight-fürtök .NET SDK használatával |Lásd: [található az alapértelmezett tárfiókot, a HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK használatával a HDInsight-fürtök törlése |Lásd: [törlése HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Példák
A következő példák a hogyan van egy művelet alapján történik a címterület-kezelési alapú SDK és az azzal egyenértékű kódrészletet az ARM-alapú SDK-ban.

**A fürt CRUD-ügyfél létrehozása**

* Régi parancs (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Új parancs (ARM) (egyszerű szolgáltatás engedélyezése)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Új parancs (ARM) (felhasználói engedélyezési)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Fürt létrehozása**

* Régi parancs (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Új parancs (ARM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP-hozzáférés engedélyezése**

* Régi parancs (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Új parancs (ARM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**A fürtök törlésével**

* Régi parancs (ASM)
  
        client.DeleteCluster(dnsName);
* Új parancs (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);

