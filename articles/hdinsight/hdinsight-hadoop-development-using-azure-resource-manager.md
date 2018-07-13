---
title: A HDInsight az Azure Resource Manager-eszközök migrálása |} A Microsoft Docs
description: Az Azure Resource Manager fejlesztői eszközökre a HDInsight-fürtök áttelepítése
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
ms.openlocfilehash: 7234341fd63f4e841ac8d15a51293148d2c60975
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001871"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Áttelepítés az Azure Resource Manager-alapú fejlesztői eszközöket a HDInsight-fürtök

HDInsight-alapú Azure Service Manager ASM-eszközök van kivezetése a HDInsight. Ha már használja az Azure PowerShell-lel, az Azure CLI vagy a HDInsight .NET SDK-val a HDInsight-fürtökkel működik, akkor arra biztatjuk a PowerShell, CLI és a jövőben .NET SDK-t az Azure Resource Manager-verziókat használhat. Ez a cikk mutatók történő áttelepítés az új Resource Manager-alapú megközelítést. Bárhol is alkalmazható, ez a dokumentum kiemeli a HDInsight az ASM- és erőforrás-kezelő módszerek közötti különbségeket.

> [!IMPORTANT]
> A címterület-kezelési támogatás PowerShell parancssori felület, és a .NET SDK nem küld a **2017. január 1**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Áttelepítés az Azure CLI Azure Resource Manager

> [!IMPORTANT]
> Az Azure CLI 2.0-val a HDInsight-fürt használata esetén nem biztosít támogatást. Továbbra is használhatja az Azure CLI 1.0-t a HDInsight, azonban az Azure CLI 1.0 elavult.

Az alábbiakban az alapszintű parancsainak Azure CLI 1.0 használatával HDInsight használata:

* `azure hdinsight cluster create` – egy új HDInsight-fürtöt hoz létre
* `azure hdinsight cluster delete` – egy meglévő HDInsight-fürt törlése
* `azure hdinsight cluster show` – egy meglévő fürthöz kapcsolatos információk megjelenítése
* `azure hdinsight cluster list` -sorolja fel a HDInsight-fürtök az Azure-előfizetés

Használja a `-h` kapcsoló vizsgálhatja meg a paramétereket és a kapcsolók érhető el minden egyes parancsnál.

### <a name="new-commands"></a>Új parancsok
Az Azure Resource Managerrel használható új parancsok a következők:

* `azure hdinsight cluster resize` – dinamikusan változtatja a fürtben található munkavégző csomópontok száma
* `azure hdinsight cluster enable-http-access` -lehetővé teszi, hogy a fürt HTTPs-hozzáférést (az alapértelmezés szerint)
* `azure hdinsight cluster disable-http-access` – a fürt HTTPs-hozzáférés letiltása
* `azure hdinsight script-action` -parancsokat kínálja a létrehozásához és kezeléséhez Szkriptműveletek egy fürtön
* `azure hdinsight config` -parancsokat biztosít a konfigurációs fájl létrehozása, amely használható a `hdinsight cluster create` parancs használatával adja meg a konfigurációs adatokat.

### <a name="deprecated-commands"></a>Elavult parancsok
Ha használja a `azure hdinsight job` küldhetők be feladatok a HDInsight-fürthöz, a parancsok ezek a parancsok nem érhetők el a Resource Manager-parancsok használatával. Ha programozott módon küldhetők be feladatok HDInsight parancsfájlok alapján van szüksége, Ehelyett használjon a HDInsight által biztosított REST API-kat. További információ a REST API-k használatával feladatok elküldése a következő dokumentumokban talál.

* [És a Hadoop MapReduce feladatok futtatása HDInsight a cURL használatával](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Hive-lekérdezések futtatásához a cURL használatával HDInsight Hadoop-keretrendszerrel](hadoop/apache-hadoop-use-hive-curl.md)
* [A Pig-feladatok futtatása és a Hadoop HDInsight a cURL használatával](hadoop/apache-hadoop-use-pig-curl.md)

Információ más különböző módon futtathatja a MapReduce-Hive, és Pig-alapú interaktív módon, lásd: [MapReduce használata a hadooppal a HDInsight](hadoop/hdinsight-use-mapreduce.md), [Hive használata a Hadooppal a HDInsight](hadoop/hdinsight-use-hive.md), és [a Pig használata a hadooppal HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

* Régi parancs (ASM) – `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Új parancsot: `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Fürt törlése**

* Régi parancs (ASM) – `azure hdinsight cluster delete myhdicluster`
* Új parancsot: `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Fürtök listázása**

* Régi parancs (ASM) – `azure hdinsight cluster list`
* Új parancsot: `azure hdinsight cluster list`

> [!NOTE]
> A list parancs megadásával az erőforrás csoport használatával `-g` adja vissza a fürtök csak a megadott erőforráscsoportban.
> 
> 

**Fürt információ megjelenítése**

* Régi parancs (ASM) – `azure hdinsight cluster show myhdicluster`
* Új parancsot: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Áttelepítés az Azure PowerShell az Azure Resource Manager
Azure PowerShell-lel kapcsolatos általános információk az Azure Resource Manager módban található [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

Az Azure PowerShell Resource Manager parancsmagjainak párhuzamosan lesz az ASM-parancsmagok is telepíthető. A parancsmagok a két mód a nevük alapján különböztethetők meg.  A Resource Manager módra van *AzureRmHDInsight* való hasonlítás parancsmag nevében szereplő *AzureHDInsight* az ASM-módban.  Ha például *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Paraméterek és kapcsolók rendelkezhetnek hírek neveket, és elérhetőek sok új paraméterek Resource Manager használata esetén.  Például számos olyan parancsmagot igényelnek-e egy új kapcsoló nevű *- ResourceGroupName*. 

A HDInsight-parancsmagok használata előtt Azure-fiókjához csatlakozhat, és az egy új erőforráscsoport létrehozásához:

* Connect-AzureRmAccount vagy [Select-azurermprofile új](https://msdn.microsoft.com/library/mt619310.aspx). Lásd: [hitelesítése egy egyszerű szolgáltatást az Azure Resource Managerrel](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok a Windows PowerShell-konzol megjelenítése:

    help *azurermhdinsight*

Az alábbi táblázat az ASM-parancsmagok és a Resource Manager módban nevét:

| Az ASM-parancsmagok | Resource Manager-parancsmagok |
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
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
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
Az alábbiakban az új parancsmagok, amelyek csak Resource Manager módban érhető el. 

**Parancsprogram-művelet kapcsolatos parancsmagok:**

* **Get-AzureRmHDInsightPersistedScriptAction**: lekérdezi a fürthöz a megőrzött Parancsfájlműveletek és időrendben sorolja fel őket, vagy egy megadott megőrzött parancsfájlművelet részleteinek beolvasása. 
* **Get-AzureRmHDInsightScriptActionHistory**: lekérdezi a parancsfájlművelet-előzmény-fürthöz tartozó, fordított időrendben sorolja fel, és lekérdezi a korábban végrehajtott szkriptműveletet részleteit. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: egy megőrzött parancsfájlművelet távolít el egy HDInsight-fürtön.
* **Set-AzureRmHDInsightPersistedScriptAction**: állítja be a korábban végrehajtott szkriptműveletet kell egy megőrzött parancsfájlművelet.
* **Küldje el AzureRmHDInsightScriptAction**: elküld egy új szkriptművelet egy Azure HDInsight-fürtön. 

További használati információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

**Fürt identitáshoz kapcsolódó parancsmagok:**

* **Adjon hozzá AzureRmHDInsightClusterIdentity**: ad hozzá egy fürt identitását egy fürt konfigurációs objektumot, hogy a HDInsight-fürt tudjon férni az Azure Data Lake Store. Lásd: [egy HDInsight-fürt létrehozása a Data Lake Store az Azure PowerShell-lel](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Hive-feladatok](hadoop/apache-hadoop-use-hive-powershell.md)
* [A Pig-feladatok elküldése](hadoop/apache-hadoop-use-pig-powershell.md)
* [Sqoop-feladatok elküldése](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Az új HDInsight .NET SDK-ba való migrálás
Az Azure Service Management-alapú [(ASM) HDInsight .NET SDK-val](https://msdn.microsoft.com/library/azure/mt416619.aspx) most már elavult. Hosszúan használata az Azure Resource Management-alapú [Resource Manager-alapú HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). A következő ASM-alapú HDInsight-csomagok elavulttá válnak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ebben a szakaszban mutató hivatkozások segítségével további információt Ez a Resource Manager-alapú SDK-val bizonyos feladatokat ismerteti.

| Hogyan... a Resource Manager-alapú HDInsight SDK használatával | Hivatkozások |
| --- | --- |
| .NET SDK használatával HDInsight-fürtök létrehozása |Lásd: [létre HDInsight-fürtök .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| A Szkriptműveletek használatával a .NET SDK-val fürt testreszabása |Lásd: [-fürtök HDInsight Linux testreszabása Szkriptműveletek használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Interaktív módon az Azure Active Directory .NET SDK-val alkalmazások hitelesítése |Lásd: [futtatása Hive-lekérdezések a .NET SDK használatával](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Ebben a cikkben a kódrészlet az interaktív hitelesítési módszert használ. |
| Az alkalmazás nem interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [for HDInsight nem interaktív alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Elküldött egy Hive-feladatot a .NET SDK használata |Lásd: [küldje el a Hive-feladatok](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| .NET SDK használatával a Pig feladat elküldése |Lásd: [elküldeni a Pig-feladatok](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| .NET SDK használatával Sqoop feladat elküldése |Lásd: [feladatok elküldéséhez a sqoop használatával](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| .NET SDK-t használó HDInsight-fürtök listázása |Lásd: [lista HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| .NET SDK-t használó HDInsight-fürtök méretezése |Lásd: [méretezési HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| GRANT/revoke access HDInsight-fürtök .NET SDK használatával |Lásd: [Grant/revoke access HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| A HTTP felhasználói hitelesítő adatokat, a .NET SDK használatával HDInsight-fürtök frissítése |Lásd: [frissítés HTTP-felhasználónál használt HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Keresse meg az alapértelmezett tárfiók HDInsight-fürtök .NET SDK használatával |Lásd: [keresse meg az alapértelmezett tárfiókot, HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK használatával HDInsight-fürtök törlése |Lásd: [törlése HDInsight-fürtök](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Példák
Az alábbiakban néhány példa, hogy milyen művelet az ASM-alapú SDK és az azzal egyenértékű kódrészlet használatával a Resource Manager-alapú SDK végrehajtva.

**Egy fürt CRUD-ügyfelének létrehozása**

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
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Új parancs (felhasználói hitelesítés)
  
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

**Fürt törlése**

* Régi parancs (ASM)
  
        client.DeleteCluster(dnsName);
* Új parancs
  
        client.Clusters.Delete(resourceGroup, dnsname);

