---
title: Az Azure Resource Manager tools for HDInsight áttelepítése
description: Az Azure Resource Manager fejlesztői eszközökre a HDInsight-fürtök áttelepítése
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 33bb3186493b2ea2a0d676f250282574b27f7988
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718512"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Áttelepítés az Azure Resource Manager-alapú fejlesztői eszközöket a HDInsight-fürtök

HDInsight-alapú Azure Service Manager ASM-eszközök van kivezetése a HDInsight. Ha már használja az Azure PowerShell-lel, az Azure klasszikus parancssori felület vagy a HDInsight .NET SDK-val a HDInsight-fürtökkel működik, akkor arra biztatjuk a PowerShell, CLI és a jövőben .NET SDK-t az Azure Resource Manager-verziókat használhat. Ez a cikk mutatók történő áttelepítés az új Resource Manager-alapú megközelítést. Bárhol is alkalmazható, ez a dokumentum kiemeli a HDInsight az ASM- és erőforrás-kezelő módszerek közötti különbségeket.

> [!IMPORTANT]  
> A címterület-kezelési támogatás PowerShell parancssori felület, és a .NET SDK nem küld a **2017. január 1**.

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Áttelepítés klasszikus Azure CLI Azure Resource Manager

> [!IMPORTANT]  
> Az Azure CLI nem biztosít támogatást a HDInsight-fürt használata esetén. Továbbra is használhatja az Azure klasszikus parancssori felület a HDInsight, azonban a klasszikus Azure-CLI elavult.

A klasszikus Azure-CLI-n keresztül a HDInsight használata az alapvető parancsok a következők:

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
* [Apache Hive-lekérdezések futtatásához az Apache Hadoop a HDInsight a cURL használatával](hadoop/apache-hadoop-use-hive-curl.md)
* [Az Apache Hadoop Apache Pig-feladatok futtatása HDInsight a cURL használatával](hadoop/apache-hadoop-use-pig-curl.md)

Apache Hadoop MapReduce, az Apache Hive- és Apache Pig futása egyéb módjaival kapcsolatos tudnivalókat lásd: [MapReduce használata a hadooppal a HDInsight](hadoop/hdinsight-use-mapreduce.md), [Apache Hive használata a HDInsight az Apache Hadoop](hadoop/hdinsight-use-hive.md), és [Apache Pig használata a HDInsight az Apache Hadoop](hadoop/hdinsight-use-pig.md).

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

**Fürt információ megjelenítése**

* Régi parancs (ASM) – `azure hdinsight cluster show myhdicluster`
* Új parancsot: `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Áttelepítés az Azure PowerShell az Azure Resource Manager
Azure PowerShell-lel kapcsolatos általános információk az Azure Resource Manager módban található [az Azure PowerShell az Azure Resource Manager](../powershell-azure-resource-manager.md).

Az Azure PowerShell Resource Manager parancsmagjainak párhuzamosan lesz az ASM-parancsmagok is telepíthető. A parancsmagok a két mód a nevük alapján különböztethetők meg.  A Resource Manager módra van *AzureRmHDInsight* való hasonlítás parancsmag nevében szereplő *AzureHDInsight* az ASM-módban.  Ha például *New-AzureRmHDInsightCluster* vs. *New-AzureHDInsightCluster*. Paraméterek és kapcsolók rendelkezhetnek hírek neveket, és elérhetőek sok új paraméterek Resource Manager használata esetén.  Például számos olyan parancsmagot igényelnek-e egy új kapcsoló nevű *- ResourceGroupName*. 

A HDInsight-parancsmagok használata előtt Azure-fiókjához csatlakozhat, és az egy új erőforráscsoport létrehozásához:

* Connect-AzureRmAccount vagy [Select-azurermprofile új](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/select-azurermprofile?view=azuresmps-4.0.0). Lásd: [hitelesítése egy egyszerű szolgáltatást az Azure Resource Managerrel](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok a Windows PowerShell-konzol megjelenítése:

    help *azurermhdinsight*

Az alábbi táblázat az ASM-parancsmagok és a Resource Manager módban nevét:

| Az ASM-parancsmagok | Resource Manager-parancsmagok |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightconfigvalues?view=azurermps-6.6.0) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightmetastore?view=azurermps-6.6.0) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction?view=azurermps-6.6.0) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightstorage?view=azurermps-6.6.0) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjob?view=azurermps-6.6.0) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjoboutput?view=azurermps-6.6.0) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightproperties?view=azurermps-6.6.0) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/invoke-azurermhdinsighthivejob?view=azurermps-6.6.0) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightclusterconfig?view=azurermps-6.6.0) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsighthivejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightmapreducejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightpigjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightsqoopjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightstreamingmapreducejobdefinition?view=azurermps-6.6.0) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/remove-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize?view=azurermps-6.6.0) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightdefaultstorage?view=azurermps-6.6.0) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/start-azurermhdinsightjob?view=azurermps-6.6.0) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/stop-azurermhdinsightjob?view=azurermps-6.6.0) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/use-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/wait-azurermhdinsightjob?view=azurermps-6.6.0) |

### <a name="new-cmdlets"></a>Új parancsmagok
Az alábbiakban az új parancsmagok, amelyek csak Resource Manager módban érhető el. 

**Parancsprogram-művelet kapcsolatos parancsmagok:**

* **Get-AzureRmHDInsightPersistedScriptAction**: Lekérdezi a fürthöz a megőrzött Parancsfájlműveletek és időrendben sorolja fel őket, vagy egy megadott megőrzött parancsfájlművelet részleteinek beolvasása. 
* **Get-AzureRmHDInsightScriptActionHistory**: Lekérdezi a parancsfájlművelet-előzmény-fürthöz tartozó fordított időrendben sorolja fel, és korábban végrehajtott szkriptműveletet részleteinek beolvasása. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: Megőrzött parancsfájlművelet távolít el egy HDInsight-fürtön.
* **Set-AzureRmHDInsightPersistedScriptAction**: Beállítja a korábban végrehajtott szkriptműveletet kell egy megőrzött parancsfájlművelet.
* **Küldje el AzureRmHDInsightScriptAction**: Az Azure HDInsight-fürthöz egy új szkriptművelet küldi el. 

További használati információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

**Fürt identitáshoz kapcsolódó parancsmagok:**

* **Adjon hozzá AzureRmHDInsightClusterIdentity**: Egy fürt identitás hozzáadása egy fürt konfigurációs objektumot, hogy a HDInsight-fürt tudjon férni az Azure Data Lake Storage. Lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Azure PowerShell-lel](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

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
* [Az Apache Hive-feladatok elküldése](hadoop/apache-hadoop-use-hive-powershell.md)
* [Az Apache Pig-feladatok elküldése](hadoop/apache-hadoop-use-pig-powershell.md)
* [Az Apache Sqoop-feladatok elküldése](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Az új HDInsight .NET SDK-ba való migrálás
Az Azure Service Management-alapú [(ASM) HDInsight .NET SDK-val](https://msdn.microsoft.com/library/azure/mt416619.aspx) most már elavult. Hosszúan használata az Azure Resource Management-alapú [Resource Manager-alapú HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight). A következő ASM-alapú HDInsight-csomagok elavulttá válnak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ebben a szakaszban mutató hivatkozások segítségével további információt Ez a Resource Manager-alapú SDK-val bizonyos feladatokat ismerteti.

| Hogyan... a Resource Manager-alapú HDInsight SDK használatával | Hivatkozások |
| --- | --- |
| .NET SDK használatával HDInsight-fürtök létrehozása |Lásd: [létre HDInsight-fürtök .NET SDK használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| A Szkriptműveletek használatával a .NET SDK-val fürt testreszabása |Lásd: [-fürtök HDInsight Linux testreszabása Szkriptműveletek használatával](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Interaktív módon az Azure Active Directory .NET SDK-val alkalmazások hitelesítése |Lásd: [.NET SDK használatával futtassa az Apache Hive lekérdezések](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Ebben a cikkben a kódrészlet az interaktív hitelesítési módszert használ. |
| Az alkalmazás nem interaktív .NET SDK-val az Azure Active Directory használatával hitelesíti |Lásd: [for HDInsight nem interaktív alkalmazások létrehozása](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK használatával egy Apache Hive-feladat elküldése |Lásd: [küldje el az Apache Hive-feladatok](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| .NET SDK használatával egy Apache Pig-feladat elküldése |Lásd: [küldje el az Apache Pig-feladatokhoz](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Az Apache Sqoop feladat .NET SDK használatával |Lásd: [feladatok beküldése az Apache sqoop használatával](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
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

