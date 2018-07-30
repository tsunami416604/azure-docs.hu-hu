---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Megtudhatja, hogyan kérdezhet le adatokat az Azure Blob Storage-ból és az Azure Data Lake Store-ból, és hogyan tárolhatja az elemzések eredményeit.
services: hdinsight,storage
tags: azure-portal
author: mumian
ms.author: jgao
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.topic: get-started-article
ms.date: 05/14/2018
ms.openlocfilehash: 13787620ca889beea74c96b8fa922287b88442f4
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237723"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

A HDInsight-fürtben lévő adatok elemzéséhez az adatokat tárolhatja az Azure Storage-ban, az Azure Data Lake Store-ban vagy mindkettőben. Mindkét tárolási lehetőség lehetővé teszi a számításhoz használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

A Hadoop támogatja az alapértelmezett fájlrendszert. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozása során blobtárolók adhatók meg alapértelmezett fájlrendszerként, illetve a HDInsight 3.5 esetében választhat, hogy az Azure Storage vagy az Azure Data Lake Store legyen az alapértelmezett fájlrendszer, néhány kivétellel. A Data Lake Storage alapértelmezett és kapcsolt tárként való egyidejű használatának támogatási lehetőségeiről a [HDInsight-fürtök esetén elérhető rendelkezésre állást](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters) ismertető cikk ad tájékoztatást.

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. A Data Lake Store HDInsight-fürtökkel való használatáról az [Azure Data Lake Store használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-store.md) című cikk szól. További információ a HDInsight-fürtök létrehozásáról: [Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-provision-linux-clusters.md).

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!WARNING]
> Az Azure Storage-fiók létrehozása során számos beállítás áll rendelkezésre. A következő táblázat információkat nyújt arról, hogy milyen beállítások támogatottak a HDInsight használatakor:
> 
> | Tárfiók típusa | Tárolási réteg | Támogatott a HDInsightban |
> | ------- | ------- | ------- |
> | Általános célú tárfiókok | Standard | __Igen__ |
> | &nbsp; | Prémium | Nem |
> | Blob Storage-fiók | Gyakori | Nem |
> | &nbsp; | Ritka | Nem |

Az alapértelmezett blobtárolóban nem ajánlott üzleti adatokat tárolni. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Fontos, hogy az alapértelmezett tároló alkalmazás- és rendszernaplókat is tartalmaz. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Egy blobtároló alapértelmezett fájlrendszerként való, több fürt közötti megosztása nem támogatott.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra
A következő ábra az Azure Storage-ot használó HDInsight tároló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage-ban.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight-tárarchitektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Az alábbiakban néhány szempont olvasható Azure Storage-fiók és HDInsight-fürtök együttes használatával kapcsolatban.

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Fürthöz NEM csatlakozó nyilvános tárolók vagy nyilvános blobok a tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.
  
  > [!NOTE]
  > A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Restrict access to containers and blobs</a> (A tárolókhoz és blobokhoz való hozzáférés korlátozása).
  > 
  > 
* **Fürthöz NEM csatlakozó személyes tárolók a tárfiókokban:** Nem érheti el a tárolókban lévő blobokat, ha nem határozza meg a tárfiókot a WebHCat-feladatok elküldésekor. Ennek a magyarázatát a cikk későbbi részében találja.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. A beállítást az [Ambari](./hdinsight-hadoop-manage-ambari.md) használatával módosíthatja

Több WebHCat-feladat (beleértve a Hive, MapReduce, Hadoop-stream és Pig-feladatokat) is tartalmazhatja a tárfiókok és a metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blobtárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

## <a id="benefits"></a>Az Azure Storage előnyei
A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményköltségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat hatékonnyá teszi a számítási csomópontok számára az Azure Storage-ban lévő adatok elérését.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Blob Storage-ban lévő adatok a HDFS API-kon vagy a [Blob Storage REST API-kon][blob-storage-restAPI] keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.
* **Adatarchiválás:** Az adatok Azure Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.
* **Adattárolási költség:** Az adatok DFS-ben végzett hosszú távú tárolása költségesebb, mint az adatok Azure Storage tárolóban végzett tárolása, mert a számítási fürt költsége nagyobb, mint az Azure Storage költsége. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.
* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.
* **Georeplikáció:** Az Azure Storage tárolókról georeplikálás készíthető. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]
> A legtöbb HDFS parancs (például az <b>ls</b>, a <b>copyFromLocal</b> és a <b>mkdir</b>) továbbra is a várt módon működik. Csak a natív HDFS implementációra (más néven DFS-re) jellemző parancsok, például az <b>fschk</b> és a <b>dfsadmin</b> viselkednek máshogy az Azure Storage tárolóban.
> 
> 

## <a name="create-blob-containers"></a>Blob tárolók létrehozása
A blobok használatához először hozzon létre egy [Azure Storage-fiókot][azure-storage-create]. Ennek részeként meg kell adnia egy Azure-régiót, amelyben a tárfiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server adatbázisának és az Oozie-metaadattár SQL Server adatbázisának is ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket. Ajánlott különböző tárolót használni mindegyik fürthöz és a megosztott adatokat az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni az alapértelmezett tárfiók helyett. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása][hdinsight-creation]. De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. Az HBase fürtök esetén megőrizheti az HBase táblasémát és adatokat, ha létrehoz egy új HBase-fürtöt a törölt HBase-fürt által használt alapértelmezett blobtárolóval.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Amikor HDInsight-fürtöt hoz létre a portálról, megadhatja a tárfiók részleteit (az alábbiakban látható módon). Azt is megadhatja, hogy szeretne-e további tárfiókot társítani a fürttel, és ha igen, a Data Lake Store-t vagy más Azure Storage-blobot választhat további tárolóként.

![HDInsight hadoop létrehozási adatforrás](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

> [!WARNING]
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.


### <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ha [telepítette és konfigurálta az Azure PowerShellt][powershell-install], a következőt használhatja az Azure PowerShell-parancssorról egy tárfiók és tároló létrehozásához:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Ha [telepítette és konfigurálta az Azure CLI parancssori felületet](../cli-install-nodejs.md), a következő parancs használható a tárfiókokhoz és tárolóhoz.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> A `--type` paraméter jelzi, hogyan lesz replikálva a tárfiók. További információ: [Azure Storage replication](../storage/storage-redundancy.md) (Az Azure Storage replikációja). Na használjon ZRS-t, mivel a ZRS nem támogatja a lapblobokat, a fájlokat, a táblákat és az üzenetsorokat.
> 
> 

A rendszer kéri, hogy adja meg a földrajzi régiót, amelyben a tárfiók létre lett hozva. Ugyanabban a régióban kell létrehoznia a tárfiókot, mint amelyben a HDInsight-fürt létrehozását tervezi.

A tárfiók létrehozása után a következő paranccsal kérheti le a tárfiók kulcsait:

    azure storage account keys list <storageaccountname>

Egy tároló létrehozásához használja az alábbi parancsot:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="address-files-in-azure-storage"></a>Az Azure Storage tárolóban található címfájlok
Az Azure Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A &lt;BlobStorageContainerName&gt; azonosítja a blobtároló nevét az Azure Storage tárolóban.
A &lt;StorageAccountName&gt; azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha a &lt;BlobStorageContainerName&gt; és a &lt;StorageAccountName&gt; sincs meghatározva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> A fájlnév a <i>hadoop-examples.jar</i> a HDInsight 2.1-es és 1.6-s verziójú fürtökben.
> 
> 

Az &lt;elérési út&gt; a fájl vagy könyvtár HDFS elérési útja neve. Mivel az Azure Blob Storage tárolóban lévő tárolók egyszerűen kulcs-érték tárolók, nincs valós hierarchikus fájlrendszer. A blob kulcson belüli perjel karaktert ( / ) a rendszer könyvtárelválasztóként értelmezi. A *hadoop-mapreduce-examples.jar* blobneve például a következő:

    example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.
> 
> 

## <a name="access-blobs"></a>Blobok elérése 


### <a name="access-blobs-using-azure-powershell"></a> Az Azure PowerShell használata
> [!NOTE]
> Ezen szakasz parancsai a blobokban tárolt adatok PowerShell eszközön keresztüli elérésének egyszeri példáit nyújtják. A HDInsight használatához testreszabott további teljes példákért lásd: [HDInsight eszközök](https://github.com/Blackmist/hdinsight-tools).
> 
> 

Használja az alábbi parancsot a blobbal kapcsolatos parancsmagok listázásához:

    Get-Command *blob*

![A blobbal kapcsolatos PowerShell parancsmagok listája.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Fájlok feltöltése
Lásd: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

#### <a name="download-files"></a>Fájlok letöltése
A következő szkript egy blokkblobot tölt le az aktuális mappába. A parancsfájl futtatása előtt módosítsa a könyvtárt olyan mappára, ahol írási engedélyei vannak.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Connect-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Az erőforráscsoport nevét és a fürt nevét megadva a következő kódot használhatja:

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force


#### <a name="delete-files"></a>Fájlok törlése
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

#### <a name="list-files"></a>Fájlok listázása
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Hive-lekérdezések futtatása nem meghatározott tárfiókkal
Ez a példa bemutatja, hogyan listázhat mappákat a létrehozási folyamat során nem meghatározott tárfiókból.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Használja az alábbi parancsot a blobbal kapcsolatos parancsok listázásához:

    azure storage blob

**Az Azure parancssori felület használatának példája fájlok feltöltéséhez**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Az Azure parancssori felület használatának példája fájlok letöltéséhez**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Az Azure parancssori felület használatának példája fájlok törléséhez**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Az Azure parancssori felület használatának példája fájlok listázásához**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>További lépések
Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Az Azure Data Lake Store használatának első lépései](../data-lake-store/data-lake-store-get-started-portal.md)
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [A Hive használata a HDInsightban][hdinsight-use-hive]
* [A Pig használata a HDInsightban][hdinsight-use-pig]
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
