---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Ismerje meg, hogyan kérdezhet le adatokat az Azure storage és az Azure Data Lake Storage tárolja, az elemzések eredményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 3356d3eee00a640efe10e2d9f3aa4fa7be775995
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360783"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

HDInsight-fürtben lévő adatok elemzéséhez, az adatokat tárolhatja akár a [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage általános 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage általános 2](../storage/blobs/data-lake-storage-introduction.md), vagy a kettő kombinációjához. A tárolási lehetőségek engedélyezése felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight-fürtök biztonságos törlését.

Az Apache Hadoop támogatja az alapértelmezett fájlrendszer. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight fürt létrehozása során megadhatja egy blob-tárolóba az Azure Storage, az alapértelmezett fájlrendszerként, vagy a HDInsight 3.6-os, kiválaszthatja az Azure Storage vagy az Azure Data Lake Storage általános 1 / Azure Data Lake Storage, az alapértelmezett fájlok 2. generációs gyűjtések a rendszer néhány kivétellel. Az alapértelmezett és a társított storage használatával a Data Lake Storage általános 1 támogathatóság, lásd: [HDInsight-fürt rendelkezésre állási](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. A HDInsight-fürtökkel Data Lake Storage általános 1 működésével kapcsolatban lásd: [használata Azure Data Lake Storage az Azure HDInsight-fürtök](hdinsight-hadoop-use-data-lake-store.md). Egy HDInsight-fürt létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!WARNING]  
> Az Azure Storage-fiók létrehozása során számos beállítás áll rendelkezésre. A következő táblázat információkat nyújt arról, hogy milyen beállítások támogatottak a HDInsight használatakor:

| Tárfiók típusa | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott elérési szint |
|----------------------|--------------------|-----------------------------|------------------------|
| Általános célú V2   | Blob               | Standard                    | Gyakori, ritka elérésű, archív tárolási szint\*    |
| Általános célú V1   | Blob               | Standard                    | –                    |
| Blob Storage         | Blob               | Standard                    | Gyakori, ritka elérésű, archív tárolási szint\*    |

Az alapértelmezett blobtárolóban nem ajánlott üzleti adatokat tárolni. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló tartalmazza, az alkalmazás- és naplókat. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Egy blobtároló alapértelmezett fájlrendszerként való, több fürt közötti megosztása nem támogatott.
 
 > [!NOTE]  
 > Az archivált adatok hozzáférési szintje egy kapcsolat nélküli réteg egy több órás késése és a HDInsight segítségével nem ajánlott. További információkért lásd: <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier">archivált adatok hozzáférési szintje</a>.

Ha úgy dönt, hogy a tárfiók biztonságos a **tűzfalak és virtuális hálózatok** korlátozásai **kiválasztott hálózatok**, ügyeljen arra, hogy a kivétel engedélyezése **engedélyezése a Microsoft megbízható szolgáltatások...**  úgy, hogy a HDInsight képes hozzáférni a tárfiókhoz.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra
A következő ábra az Azure Storage-ot használó HDInsight tároló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage-ban.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight-tárarchitektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Az alábbiakban néhány szempont olvasható Azure Storage-fiók és HDInsight-fürtök együttes használatával kapcsolatban.

* **A storage-fiókokban fürthöz csatlakozó tárolók:** Mivel a fiók neve és kulcsa társítva a fürt létrehozása során, akkor ezeket a tárolókban lévő blobokhoz teljes hozzáféréssel rendelkezik.

* **Nyilvános tárolók vagy nyilvános blobok a storage-fiókok, amelyek nem csatlakoznak a fürthöz:** Rendelkezik olvasási engedéllyel a tárolókban lévő blobokat.
  
  > [!NOTE]  
  > A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: <a href="https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources">tárolókhoz és blobokhoz való hozzáférés kezelése</a>.

* **A storage-fiókok fürthöz nem csatlakozó személyes tárolók:** Nem férhet hozzá a tárolókban lévő blobokat, kivéve, ha a WebHCat-feladatok elküldésekor határozza meg a tárfiókot. Ennek a magyarázatát a cikk későbbi részében találja.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Ez módosítható használatának beállítása [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Több WebHCat-feladatok, többek között az Apache Hive, MapReduce, az Apache Hadoop-Stream és az Apache Pig, képes továbbítani a storage-fiókok és a velük metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blobtárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

## <a id="benefits"></a>Az Azure Storage előnyei
A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményköltségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat hatékonnyá teszi a számítási csomópontok számára az Azure Storage-ban lévő adatok elérését.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** Az adatok a HDFS-ben a számítási fürtön belül találhatók. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Blob Storage-ban lévő adatok a HDFS API-kon vagy a [Blob Storage REST API-kon][blob-storage-restAPI] keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.
* **Adatarchiválás:** Adatok az Azure storage tárolóban végzett tárolása lehetővé teszi, hogy a, hogy biztonságosan felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight fürtöket.
* **Adattárolási költség:** Az elosztott Fájlrendszerbeli adatok a hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure storage-ban, mert a számítási fürt költsége nagyobb, mint az Azure storage költsége. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.
* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a méretezési csoport, amely a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.
* **Georeplikáció:** Az Azure storage georeplikált is lehet. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]  
> A legtöbb HDFS parancs (például az <b>ls</b>, a <b>copyFromLocal</b> és a <b>mkdir</b>) továbbra is a várt módon működik. Csak a natív HDFS implementációra (más néven DFS-re) jellemző parancsok, például az <b>fschk</b> és a <b>dfsadmin</b> viselkednek máshogy az Azure Storage tárolóban.


## <a name="create-blob-containers"></a>Blob tárolók létrehozása
A blobok használatához először hozzon létre egy [Azure Storage-fiókot][azure-storage-create]. Ennek részeként meg kell adnia egy Azure-régiót, amelyben a tárfiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server-adatbázis és az Apache Oozie-metaadattár SQL Server-adatbázis található ugyanabban a régióban is kell.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket. Ajánlott különböző tárolót használni mindegyik fürthöz és a megosztott adatokat az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni az alapértelmezett tárfiók helyett. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása][hdinsight-creation]. De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. Az HBase fürtök esetén megőrizheti az HBase táblasémát és adatokat, ha létrehoz egy új HBase-fürtöt a törölt HBase-fürt által használt alapértelmezett blobtárolóval.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Amikor HDInsight-fürtöt hoz létre a portálról, megadhatja a tárfiók részleteit (az alábbiakban látható módon). Megadhatja azt is kívánja-e egy tárfiókot a fürthöz társított, és ha igen, válassza ki a Data Lake Storage vagy egy másik Azure Storage-blobot a kiegészítő tárolóként.

![HDInsight hadoop létrehozási adatforrás](./media/hdinsight-hadoop-use-blob-storage/storage.png)

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.


### <a name="use-azure-powershell"></a>Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha [telepítette és konfigurálta az Azure PowerShellt][powershell-install], a következőt használhatja az Azure PowerShell-parancssorról egy tárfiók és tároló létrehozásához:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzStorageContainer -Name $containerName -Context $destContext

### <a name="use-azure-classic-cli"></a>Az Azure klasszikus parancssori felület használata

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Ha rendelkezik [telepítése és konfigurálása a klasszikus Azure-CLI](../cli-install-nodejs.md), a következő parancs használható egy tárfiókot és tárolót.

```cli
azure storage account create <storageaccountname> --type LRS
```

> [!NOTE]  
> A `--type` paraméter jelzi, hogyan lesz replikálva a tárfiók. További információ: [Azure Storage replication](../storage/storage-redundancy.md) (Az Azure Storage replikációja). Na használjon ZRS-t, mivel a ZRS nem támogatja a lapblobokat, a fájlokat, a táblákat és az üzenetsorokat.

A rendszer kéri, hogy adja meg a földrajzi régiót, amelyben a tárfiók létre lett hozva. Ugyanabban a régióban kell létrehoznia a tárfiókot, mint amelyben a HDInsight-fürt létrehozását tervezi.

A tárfiók létrehozása után a következő paranccsal kérheti le a tárfiók kulcsait:

```cli
azure storage account keys list <storageaccountname>
```

Egy tároló létrehozásához használja az alábbi parancsot:

```cli
azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="address-files-in-azure-storage"></a>Az Azure Storage tárolóban található címfájlok
Az Azure Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A &lt;BlobStorageContainerName&gt; azonosítja a blobtároló nevét az Azure Storage tárolóban.
A &lt;StorageAccountName&gt; azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha a &lt;BlobStorageContainerName&gt; és a &lt;StorageAccountName&gt; sincs meghatározva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> A fájlnév a <i>hadoop-examples.jar</i> a HDInsight 2.1-es és 1.6-s verziójú fürtökben.

Az &lt;elérési út&gt; a fájl vagy könyvtár HDFS elérési útja neve. Mivel az Azure BLOB storage tárolók kulcs-érték tárolók, nincs valós hierarchikus fájlrendszer. A blob kulcson belüli perjel karaktert ( / ) a rendszer könyvtárelválasztóként értelmezi. A *hadoop-mapreduce-examples.jar* blobneve például a következő:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="access-blobs"></a>Blobok elérése

### <a name="access-blobs-using-azure-powershell"></a> Az Azure PowerShell használata

> [!NOTE]
> 
> Ezen szakasz parancsai a blobokban tárolt adatok PowerShell eszközön keresztüli elérésének egyszeri példáit nyújtják. A HDInsight használatához testreszabott további teljes példákért lásd: [HDInsight eszközök](https://github.com/Blackmist/hdinsight-tools).

Használja az alábbi parancsot a blobbal kapcsolatos parancsmagok listázásához:

```powershell 
Get-Command *blob*
```

![A blobbal kapcsolatos PowerShell parancsmagok listája.][img-hdi-powershell-blobcommands]

#### <a name="upload-files"></a>Fájlok feltöltése

Lásd: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

#### <a name="download-files"></a>Fájlok letöltése

A következő szkript egy blokkblobot tölt le az aktuális mappába. A parancsfájl futtatása előtt módosítsa a könyvtárt olyan mappára, ahol írási engedélyei vannak.

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

# Use Add-AzureAccount if you haven't connected to your Azure subscription
Connect-AzAccount 
Select-AzSubscription -SubscriptionID "<Your Azure Subscription ID>"

Write-Host "Create a context object ... " -ForegroundColor Green
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

Write-Host "List the downloaded file ..." -ForegroundColor Green
cat "./$blob"
```

Az erőforráscsoport nevét és a fürt nevét megadva a következő kódot használhatja:

```powershell
$resourceGroupName = "<AzureResourceGroupName>"
$clusterName = "<HDInsightClusterName>"
$blob = "example/data/sample.log" # The name of the blob to be downloaded.

$cluster = Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
$defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
$defaultStorageContainer = $cluster.DefaultStorageContainer
$storageContext = New-AzStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

Write-Host "Download the blob ..." -ForegroundColor Green
Get-AzStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force
```

#### <a name="delete-files"></a>Fájlok törlése

```powershell
Remove-AzStorageBlob -Container $containerName -Context $storageContext -blob $blob
```

#### <a name="list-files"></a>Fájlok listázása

```powershell
Get-AzStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"
```

#### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Hive-lekérdezések futtatása nem meghatározott tárfiókkal

Ez a példa bemutatja, hogyan listázhat mappákat a létrehozási folyamat során nem meghatározott tárfiókból.

```powershell
$clusterName = "<HDInsightClusterName>"

$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

$undefinedStorageKey = Get-AzStorageKey $undefinedStorageAccount | %{ $_.Primary }

Use-AzHDInsightCluster $clusterName

$defines = @{}
$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

Invoke-AzHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
```

### <a name="use-azure-classic-cli"></a>Az Azure klasszikus parancssori felület használata

Használja az alábbi parancsot a blobbal kapcsolatos parancsok listázásához:

```cli
azure storage blob
```

**Az Azure klasszikus parancssori felület használatának példája fájlok feltöltéséhez**

```cli
azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Az Azure klasszikus parancssori felület használatának példája fájlok letöltéséhez**

```cli
azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Az Azure klasszikus parancssori felület használatának példája fájlok törléséhez**

```cli
azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>
```

**Az Azure klasszikus parancssori felület használatának példája fájlok listázásához**

```cli
azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>
```

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Azure HDInsight – első lépések][hdinsight-get-started]
* [Ismerkedés az Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Adatok feltöltése a HDInsightba][hdinsight-upload-data]
* [Az Apache Hive használata a HDInsight][hdinsight-use-hive]
* [Az Apache Pig használata a HDInsight][hdinsight-use-pig]
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban][hdinsight-use-sas]
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](hdinsight-hadoop-use-data-lake-storage-gen2.md)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
