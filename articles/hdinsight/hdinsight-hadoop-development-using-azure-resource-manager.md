---
title: A HDInsight az Azure Resource Manager eszközeinek át |} Microsoft Docs
description: Azure Resource Manager Fejlesztőeszközök a HDInsight-fürtök áttelepítése
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: a18d045815a65953aafd690674fb9b5ce129c825
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>A Fejlesztőeszközök Azure Resource Manager-alapú HDInsight-fürtök áttelepítése

A HDInsight-ból kivezettük való Azure Service Manager ASM-alapú eszközök hdinsight. Ha használta az Azure PowerShell, az Azure parancssori felület vagy a HDInsight .NET SDK a HDInsight-fürtök együttműködni, hosszúan PowerShell, a parancssori felület és a .NET SDK továbbítja az Azure Resource Manager-verziót használnak. Ez a cikk mutatók áttelepítése az új Resource Manager-alapú módszert biztosít. Megfelelő esetben ez a dokumentum a HDInsight a címterület-kezelési és erőforrás-kezelő módszerek közötti különbségeket mutatja be.

> [!IMPORTANT]
> A címterület-kezelési támogatás PowerShell parancssori felület, és a .NET SDK nem küld a **2017. január 1**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Az Azure CLI áttelepítése az Azure Resource Manager számára

> [!IMPORTANT]
> Az Azure CLI 2.0 nem támogatják a HDInsight-fürtök használata. Továbbra is használhatja Azure CLI 1.0 a hdinsight eszközzel, azonban az Azure CLI 1.0 elavult.

A HDInsight az Azure CLI 1.0 keresztül használatához alapvető parancsok a következők:

* `azure hdinsight cluster create` -hoz létre egy új HDInsight-fürt
* `azure hdinsight cluster delete` – egy meglévő HDInsight-fürt törlése
* `azure hdinsight cluster show` – egy meglévő fürthöz kapcsolatos információk megjelenítése
* `azure hdinsight cluster list` – ismerteti az Azure-előfizetés a HDInsight-fürtök

Használja a `-h` kapcsolót, hogy a paraméterek és kapcsolók érhető el minden egyes parancsnál.

### <a name="new-commands"></a>Új parancsok
Elérhető az Azure Resource Manager új parancsok a következők:

* `azure hdinsight cluster resize` -dinamikusan változik adhatja meg a fürt feldolgozó csomópontjainak számát
* `azure hdinsight cluster enable-http-access` -HTTPs-hozzáférést biztosít a fürt (az alapértelmezés)
* `azure hdinsight cluster disable-http-access` -letiltja a HTTPs-hozzáférés a fürthöz
* `azure hdinsight script-action` -parancsokat biztosít létrehozása vagy kezelése Parancsfájlműveletek egy fürtön
* `azure hdinsight config` -parancsok biztosít a konfigurációs fájl létrehozása, amely használható a `hdinsight cluster create` parancs használatával adja meg a konfigurációs adatokat.

### <a name="deprecated-commands"></a>Elavult parancsok
Ha használja a `azure hdinsight job` elküldeni a HDInsight-fürtjét, feladatok parancsok ezek a parancsok nem érhetők el az erőforrás-kezelő parancsok. Ha programozottan feladatok elküldéséhez a HDInsight parancsfájlok van szüksége, helyette használja a HDInsight által biztosított REST API-k. A REST API-k használatával feladatok elküldésekor további információkért lásd a következő dokumentumokat.

* [Hadoop MapReduce-feladatok a HDInsight használata cURL használatával futtassa](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [A Hadoop Hive-lekérdezések futtatása a HDInsight használata cURL használatával](hadoop/apache-hadoop-use-hive-curl.md)
* [Pig-feladatokhoz Hadoop on HDInsight használata cURL használatával futtassa](hadoop/apache-hadoop-use-pig-curl.md)

Információk más módjairól MapReduce futtatásához, struktúra, és interaktív módon sertésfelmérés, lásd: [használata MapReduce a Hadoop on HDInsight](hadoop/hdinsight-use-mapreduce.md), [használata a hdinsight Hadoop Hive](hadoop/hdinsight-use-hive.md), és [a Pig használata a hadooppal HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

* Régi parancs (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Új parancsot: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**A fürtök törlésével**

* Régi parancs (ASM)- `azure hdinsight cluster delete myhdicluster`
* Új parancsot: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lista fürtök**

* Régi parancs (ASM)- `azure hdinsight cluster list`
* Új parancsot: `azure hdinsight cluster list`

> [!NOTE]
> A parancs megadásával az erőforrás csoport használatával `-g` csak a fürtök ad vissza a megadott erőforráscsoportban.
> 
> 

**Fürt információ megjelenítése**

* Régi parancs (ASM)- `azure hdinsight cluster show myhdicluster`
* Új parancsot: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Az Azure PowerShell áttelepítése az Azure Resource Manager számára
Az Azure PowerShell általános információk az Azure Resource Manager módban található [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md).

Az Azure PowerShell Resource Manager parancsmagok és a címterület-kezelési parancsmagok telepíthetők. A két mód a parancsmag elkülönítsék névvel.  A Resource Manager módra van *AzureRmHDInsight* való hasonlítás parancsmag nevében szereplő *AzureHDInsight* a címterület-kezelési módban.  Például *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Paraméterek és kapcsolók hírek nevük lehet, hogy legyen, és nincsenek sok új paraméter elérhető erőforrás-kezelő használata esetén.  Például több parancsmagok szükséges nevű új kapcsoló *- ResourceGroupName*. 

A HDInsight-parancsmagokat használhatja, csatlakozzon az Azure-fiókjával, és az új erőforráscsoport létrehozása:

* Login-AzureRmAccount vagy [válasszon-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Lásd: [hitelesítéséhez az Azure Resource Manager egyszerű szolgáltatás](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok a Windows PowerShell-konzolban listázásához:

    help *azurermhdinsight*

A következő táblázat a címterület-kezelési parancsmagok és a Resource Manager módra a nevek:

| Címterület-kezelési parancsmagok | Erőforrás-kezelő parancsmagok |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Új parancsmagok
Az alábbiakban az új parancsmagok, amelyek csak erőforrás-kezelő módban érhető el. 

**Parancsfájl művelet kapcsolatos parancsmagok:**

* **Get-AzureRmHDInsightPersistedScriptAction**: lekérdezi a megőrzött Parancsfájlműveletek fürt és időrendben sorolja fel azokat, vagy egy megadott megőrzött parancsfájl művelet lekérdezi a részletek. 
* **Get-AzureRmHDInsightScriptActionHistory**: a parancsfájlművelet előzményeinek lekérdezi a fürt, fordított időrendben sorolja fel, és lekérdezi a korábban végrehajtott parancsfájlművelet részleteit. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: egy megőrzött parancsfájl művelet eltávolítja a HDInsight-fürtöt.
* **Set-AzureRmHDInsightPersistedScriptAction**: egy megőrzött parancsfájl műveletet kell azelőtti parancsfájlművelet állítja be.
* **Küldje el AzureRmHDInsightScriptAction**: egy új parancsfájlművelet Azure HDInsight-fürtöt ad meg. 

További használati információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md).

**A fürt identitását kapcsolatos parancsmagok:**

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

Új parancs:

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

Új parancs:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lista fürt**

Régi parancs (ASM):

    Get-AzureHDInsightCluster

Új parancs:

    Get-AzureRmHDInsightCluster 

**Fürt megjelenítése**

Régi parancs (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Új parancs:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Más minták
* [A HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Küldje el a Hive-feladatok](hadoop/apache-hadoop-use-hive-powershell.md)
* [Küldje el a Pig-feladatokhoz](hadoop/apache-hadoop-use-pig-powershell.md)
* [Sqoop feladatok elküldéséhez](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Az új HDInsight .NET SDK áttelepítése
Az Azure Szolgáltatáskezelés-alapú [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) elavult. Hosszúan használja az Azure Resource Manager-alapú [Resource Manager-alapú HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx). A következő ASM-alapú HDInsight-csomagok elavulttá válnak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ez a témakör mutatókat biztosít a további információt a Resource Manager-alapú SDK használatával bizonyos feladatok elvégzéséhez.

| Hogyan... a Resource Manager-alapú HDInsight SDK használatával | Hivatkozások |
| --- | --- |
| .NET SDK használatával a HDInsight-fürtök létrehozása |Lásd: [HDInsight-fürtök létrehozása .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| A fürt parancsfájlművelet .NET SDK-val testreszabása |Lásd: [testreszabása HDInsight Linux clusters parancsfájlművelet használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Alkalmazások interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [.NET SDK használatával futtassa Hive lekérdezések](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Ebben a cikkben a kódrészletet használja az interaktív hitelesítési módszerrel. |
| Alkalmazások nem interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [a HDInsight nem interaktív alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK használatával Hive feladat elküldése |Lásd: [elküldeni a Hive-feladatok](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Elküldeni a Pig feladatot .NET SDK használatával |Lásd: [elküldeni a Pig-feladatokhoz](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| .NET SDK használatával Sqoop feladat elküldése |Lásd: [nyújt Sqoop feladatok](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Lista HDInsight-fürtök .NET SDK használatával |Lásd: [lista HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| A HDInsight-fürtök .NET SDK használatával méretezése |Lásd: [méretezési HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| A HDInsight-fürtök .NET SDK használatával engedélyezéshez/visszavonáshoz elérésére |Lásd: [Grant/revoke access HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP felhasználó hitelesítő adatait a HDInsight-fürtök .NET SDK használatával |Lásd: [frissítés HTTP felhasználói hitelesítő adatokat a HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Az alapértelmezett tárfiók keresése a HDInsight-fürtök .NET SDK használatával |Lásd: [található az alapértelmezett tárfiókot, a HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK használatával a HDInsight-fürtök törlése |Lásd: [törlése HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Példák
A következő példák a hogyan van egy művelet alapján történik a címterület-kezelési alapú SDK és az azzal egyenértékű kódrészletet a Resource Manager-alapú SDK-ban.

**A fürt CRUD-ügyfél létrehozása**

* Régi parancs (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Új parancs (egyszerű szolgáltatás engedélyezése)
  
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
* Új parancs (felhasználói engedélyezési)
  
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
* Új parancs
  
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
* Új parancs
  
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
* Új parancs
  
        client.Clusters.Delete(resourceGroup, dnsname);

