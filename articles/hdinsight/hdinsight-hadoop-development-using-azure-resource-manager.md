---
title: Áttelepítés az Azure Resource Manager eszközeire a HDInsighthoz
description: Az Azure Resource Manager HDInsight-fürtökhöz való áttelepítése
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934586"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Áttelepítés az Azure Resource Manager-alapú fejlesztői eszközökre HDInsight-fürtökhöz

A HDInsight elavulta hdinsight-alapú Azure Service Manager (ASM) alapú eszközöket. Ha az Azure PowerShell, az Azure Classic CLI vagy a HDInsight .NET SDK használatával hdinsight-fürtökkel dolgozik, javasoljuk, hogy a PowerShell, a CLI és a .NET SDK Azure Resource Manager-verzióit használja a jövőben. Ez a cikk az új Erőforrás-kezelő alapú megközelítésre való áttelepítést ismerteti. Adott esetben ez a dokumentum kiemeli a HDInsight ASM- és Erőforrás-kezelő-megközelítései közötti különbségeket.

> [!IMPORTANT]  
> **2017. január 1-jén**megszűnik az ASM-alapú PowerShell, CLI és .NET SDK támogatása.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Az Azure Classic CLI áttelepítése az Azure Resource Manager beszolgáltatására

> [!IMPORTANT]  
> Az Azure CLI nem támogatja a HDInsight-fürtökkel való munkát. Továbbra is használhatja az Azure Classic CLI-t a HDInsight-mal, azonban az Azure Classic CLI elavult.

Az alábbiakban a HDInsight Azure CLassic CLI-n keresztüli használatára vonatkozó alapvető parancsokat ismeri fel:

* `azure hdinsight cluster create`- létrehoz egy új HDInsight klasztert
* `azure hdinsight cluster delete`- töröl egy meglévő HDInsight-fürtöt
* `azure hdinsight cluster show`- információk megjelenítése egy meglévő fürtről
* `azure hdinsight cluster list`- felsorolja a HDInsight-fürtöket az Azure-előfizetéséhez

A `-h` kapcsolósegítségével vizsgálja meg az egyes parancsokhoz rendelkezésre álló paramétereket és kapcsolókat.

### <a name="new-commands"></a>Új parancsok
Az Azure Resource Manager a következő új parancsokat elérhető:

* `azure hdinsight cluster resize`- dinamikusan módosítja a dolgozó csomópontok számát a fürtben
* `azure hdinsight cluster enable-http-access`- lehetővé teszi a HTTPs hozzáférést a fürthöz (alapértelmezés szerint)
* `azure hdinsight cluster disable-http-access`- letiltja a HTTPs hozzáférést a fürthöz
* `azure hdinsight script-action`- parancsokat biztosít a parancsfájlműveletek létrehozásához/kezeléséhez egy fürtön
* `azure hdinsight config`- parancsokat biztosít egy konfigurációs fájl `hdinsight cluster create` létrehozásához, amely a paranccsal használható a konfigurációs információk megadására.

### <a name="deprecated-commands"></a>Elavult parancsok
Ha a `azure hdinsight job` parancsok segítségével küldi el a feladatokat a HDInsight-fürtbe, ezek a parancsok nem érhetők el az Erőforrás-kezelő parancsain keresztül. Ha programozott módon kell elküldenie a feladatokat a HDInsight-parancsfájlokból, ehelyett a HDInsight által biztosított REST API-kat kell használnia. A REST API-k használatával történő feladatok elküldésével kapcsolatos további információkért tekintse meg az alábbi dokumentumokat.

* [MapReduce feladatok futtatása a Hadoop segítségével a HDInsight on cURL használatával](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Apache Hive-lekérdezések futtatása az Apache Hadoop segítségével a HDInsight on cURL használatával](hadoop/apache-hadoop-use-hive-curl.md)


Az Apache Hadoop MapReduce, az Apache Hive és az Apache Pig interaktív futtatásának egyéb módjairól a [MapReduce használata hadoopsegítségével a HDInsight on HDInsight](hadoop/hdinsight-use-mapreduce.md)című témakörben, [az Apache Hive használata az Apache Hadoop használatával a HDInsight on,And](hadoop/hdinsight-use-hive.md) [Use Apache Pig with Apache Hadoop on HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Példák
**Fürt létrehozása**

* Régi parancs (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Új parancs -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Fürt törlése**

* Régi parancs (ASM) -`azure hdinsight cluster delete myhdicluster`
* Új parancs -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Fürtök listázása**

* Régi parancs (ASM) -`azure hdinsight cluster list`
* Új parancs -`azure hdinsight cluster list`

> [!NOTE]  
> A listaparancsban az erőforráscsoport `-g` használatával történő megadása csak a megadott erőforráscsoport fürtjeit adja vissza.

**Fürtadatok megjelenítése**

* Régi parancs (ASM) -`azure hdinsight cluster show myhdicluster`
* Új parancs -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Az Azure PowerShell áttelepítése az Azure Resource Managerbe
Az Azure Resource Manager módban az Azure PowerShellhasználatával kapcsolatos általános információk az [Azure PowerShell használata](../powershell-azure-resource-manager.md)az Azure Resource Manager rel című részben találhatók.

Az Azure PowerShell Resource Manager parancsmagjai az ASM-parancsmagokkal együtt telepíthetők. A két mód parancsmagjai a nevükkel különböztethetők meg.  Az Erőforrás-kezelő mód az *AzHDInsight* a parancsmag nevei összehasonlítása *az AzureHDInsight* asm módban.  Például *a New-AzHDInsightCluster* és az *Új-AzureHDInsightCluster*. A paramétereknek és kapcsolóknak lehethírneve, és az Erőforrás-kezelő használata kor számos új paraméter érhető el.  Például több parancsmaghoz új kapcsolószükséges, a *-ResourceGroupName*. 

A HDInsight-parancsmagok használata előtt csatlakoznia kell az Azure-fiókjához, és létre kell hoznia egy új erőforráscsoportot:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Átnevezett parancsmagok
A HDInsight ASM-parancsmagok listázása a Windows PowerShell konzolon:

    help *azurehdinsight*

Az alábbi táblázat az ASM-parancsmagokat és azok nevét sorolja fel Erőforrás-kezelő módban:

| ASM-parancsmagok | Erőforrás-kezelő parancsmagjai |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightcluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsight JobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty tulajdonság |[Get-AzHDInsighttulajdonság](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Új-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Új-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Új-AzHDInsightHiveFeladatdefiníció](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Új-AzHDInsightMapReducejobdefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Új-AzHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Új-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobdefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightclusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Új parancsmagok
Az alábbiakban az új parancsmagok, amelyek csak erőforrás-kezelő módban érhetők el. 

**Parancsfájlokkal kapcsolatos műveletparancsmagok:**

* **Get-AzHDInsightPersistedScriptAction:** Lekéri a fürt megőrzött parancsfájl-műveleteket, és időrendi sorrendben sorolja fel őket, vagy egy megadott, megőrzött parancsfájlművelet részleteit. 
* **Get-AzHDInsightScriptActionHistory**: Lekéri a fürt parancsfájl-műveletelőzményeit, és fordított időrendi sorrendben sorolja fel, vagy egy korábban végrehajtott parancsfájlművelet részleteit. 
* **Remove-AzHDInsightPersistedScriptAction**: Egy megőrzött parancsfájlművelet eltávolítása egy HDInsight-fürtből.
* **Set-AzHDInsightPersistedScriptAction**: Egy korábban végrehajtott parancsfájlműveletet egy megőrzött parancsfájlműveletnek állít be.
* **Submit-AzHDInsightScriptAction:** Új parancsfájlműveletküldése egy Azure HDInsight-fürtbe. 

További használati információkért olvassa el a [Linux-alapú HDInsight-fürtök testreszabása parancsfájlművelettel című témakört.](hdinsight-hadoop-customize-cluster-linux.md)

**Fürtidentitással kapcsolatos parancsmagok:**

* **Add-AzHDInsightClusterIdentity:** fürtidentitáshozzáadása egy fürtkonfigurációs objektumhoz, hogy a HDInsight-fürt hozzáférhessen az Azure Data Lake Storage-hoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage használatával az Azure PowerShell használatával című témakört.](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)

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

    New-AzHDInsightCluster `
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

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Fürt listázása**

Régi parancs (ASM):

    Get-AzureHDInsightCluster

Új parancs:

    Get-AzHDInsightCluster 

**Fürt megjelenítése**

Régi parancs (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Új parancs:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Egyéb minták
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Apache Hive-feladatok küldése](hadoop/apache-hadoop-use-hive-powershell.md)
* [Apache Sqoop-feladatok beküldése](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Áttelepítés az új HDInsight .NET SDK-ra
Az Azure Service Management-alapú [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) már elavult. Javasoljuk, hogy használja az Azure Resource Management-alapú [Erőforrás-kezelő-alapú HDInsight .NET SDK.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) A következő ASM-alapú HDInsight-csomagok elavultak.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Ez a szakasz további információt nyújt arra vonatkozóan, hogy miként hajthat végre bizonyos feladatokat az Erőforrás-kezelő alapú SDK használatával.

| kézikönyv... az Erőforrás-kezelő alapú HDInsight SDK használatával | Hivatkozások |
| --- | --- |
| .NET-hez készült Azure HDInsight SDK|Lásd: [Azure HDInsight SDK for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Alkalmazások interaktív hitelesítése az Azure Active Directory használatával a .NET SDK használatával |Lásd: [Apache Hive-lekérdezések futtatása a .NET SDK használatával.](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) A cikkben szereplő kódrészlet az interaktív hitelesítési megközelítést használja. |
| Nem interaktív módon hitelesítheti az alkalmazásokat az Azure Active Directory használatával a .NET SDK szolgáltatással |Lásd: [Nem interaktív alkalmazások létrehozása a HDInsighthoz](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Apache Hive-feladat küldése a .NET SDK használatával |Lásd: [Apache Hive-feladatok küldése](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Apache Sqoop-feladat beküldése .NET SDK használatával |Lásd: [Apache Sqoop-feladatok küldése](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| HDInsight-fürtök listázása .NET SDK használatával |Lásd: [HDInsight-fürtök listája](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| HDInsight-fürtök méretezése a .NET SDK használatával |Lásd: [HDInsight-fürtök méretezése](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| HDInsight-fürtökhöz való hozzáférés megadása/visszavonása a .NET SDK használatával |Lásd: [HdInsight-fürtökhöz való hozzáférés megadása/visszavonása](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| A HDInsight-fürtök HTTP-felhasználói hitelesítő adatainak frissítése a .NET SDK használatával |Lásd: [HTTP-felhasználói hitelesítő adatok frissítése HDInsight-fürtökhöz](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| A HDInsight-fürtök alapértelmezett tárolófiókjának megkeresése a .NET SDK használatával |Lásd: [A HDInsight-fürtök alapértelmezett tárfiókjának megkeresése](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| HDInsight-fürtök törlése a .NET SDK használatával |Lásd: [HDInsight-fürtök törlése](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Példák
Az alábbiakban néhány példa, hogyan történik egy művelet végrehajtása az ASM-alapú SDK és az egyenértékű kódrészlet az Erőforrás-kezelő-alapú SDK.

**Fürt CRUD-ügyfél létrehozása**

* Régi parancs (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Új parancs (egyszerű szolgáltatásengedélyezése)
  
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
* Új parancs (Felhasználói engedélyezés)
  
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

