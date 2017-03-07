---
title: "Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból | Microsoft Docs"
description: "Megtudhatja, hogyan kérdezhet le adatokat az Azure Blob Storage-ból és az Azure Data Lake Store-ból, és hogyan tárolhatja az elemzések eredményeit."
keywords: "blob storage,hdfs,strukturált adatok,strukturálatlan adatok, data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6d8133299b062bf3935df9c30dc8a6fcf88a525e
ms.openlocfilehash: d3af6358a5786510f4f150425d0eb8ed45e52a6c
ms.lasthandoff: 02/28/2017


---
# <a name="use-hdfs-compatible-storage-with-hadoop-in-hdinsight"></a>A HDFS-kompatibilis tároló és a Hadoop együttes használata a HDInsightban

A HDInsight-fürtben lévő adatok elemzéséhez az adatokat tárolhatja az Azure Blob Storage-ban, az Azure Data Lake Store-ban vagy mindkettőben. Mindkét tárolási lehetőség lehetővé teszi a számításhoz használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

A Hadoop támogatja az alapértelmezett fájlrendszert. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozása során Azure Blob Storage tárolók adhatók meg alapértelmezett fájlrendszerként, illetve a HDInsight 3.5 esetében választhat, hogy az Azure Blob Storage vagy az Azure Data Lake Store legyen az alapértelmezett fájlrendszer.

Ebben a cikkben megtudhatja, hogyan működik a két tárolási lehetőség a HDInsight-fürtökkel. További információ a HDInsight-fürtök létrehozásáról: [Ismerkedés a HDInsight szolgáltatással](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="using-azure-blob-storage-with-hdinsight-clusters"></a>Az Azure Blob Storage és a HDInsight-fürtök együttes használata

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!IMPORTANT]
> A HDInsight csak blokkblobokat támogat. A lapblobokat és hozzáfűző blobokat nem támogatja.
> 
> 

### <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra
A következő ábra a HDInsight tárló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage-ban.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight-tárarchitektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Blob Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Az alábbiakban néhány szempont olvasható Azure Storage-fiók és HDInsight-fürtök együttes használatával kapcsolatban.

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Fürthöz NEM csatlakozó nyilvános tárolók vagy nyilvános blobok a tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.
  
  > [!NOTE]
  > A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Restrict access to containers and blobs</a> (A tárolókhoz és blobokhoz való hozzáférés korlátozása).
  > 
  > 
* **Fürthöz NEM csatlakozó személyes tárolók a tárfiókokban:** Nem érheti el a tárolókban lévő blobokat, ha nem határozza meg a tárfiókot a WebHCat-feladatok elküldésekor. Ennek a magyarázatát a cikk későbbi részében találja.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Nem ajánlott a core-site.xml fájl módosítása, mert a fürt átjárócsomópontja (fő) rendszerképe bármikor alaphelyzetbe állítható vagy áttelepíthető, és ekkor ezen fájlok minden módosítása elveszik.

Több WebHCat-feladat (beleértve a Hive, MapReduce, Hadoop-stream és Pig-feladatokat) is tartalmazhatja a tárfiókok és a metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) Ezen cikk [Blobok elérése az Azure PowerShell eszközzel](#powershell) szakaszában találja ezen szolgáltatás egy mintáját. További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A Blob Storage tárolók a strukturált és strukturálatlan adatokhoz használhatók. A Blob Storage tárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

### <a name="a-idbenefitsabenefits-of-blob-storage"></a><a id="benefits"></a>A Blob Storage előnyei
A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményi költségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat nagyon hatékonnyá teszi, ahogyan a számítási csomópontok elérik az Azure Blob Storage tárlóban lévő adatokat.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Blob Storage-ban lévő adatok a HDFS API-kon vagy a [Blob Storage REST API-kon][blob-storage-restAPI] keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.
* **Adatarchiválás:** Az adatok Azure Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.
* **Adattárolási költség:** Az adatok DFS-ben végzett hosszú távú tárolása költségesebb, mint az adatok Azure Blob Storage tárolóban végzett tárolása, mert a számítási fürt költsége nagyobb, mint az Azure Blob Storage tárolók költsége. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.
* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.
* **Georeplikáció:** Az Azure Blob Storage tárolókról gereplikálás készíthető. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]
> A legtöbb HDFS parancs (például az <b>ls</b>, a <b>copyFromLocal</b> és a <b>mkdir</b>) továbbra is a várt módon működik. Csak a natív HDFS implementációra ( más néven DFS-re) jellemző parancsok, például a <b>fschk</b> és a <b>dfsadmin</b> viselkednek máshogy az Azure Blob Storage tárolóban.
> 
> 

### <a name="create-blob-containers"></a>Blob tárolók létrehozása
A blobok használatához először hozzon létre egy [Azure Storage-fiókot][azure-storage-create]. Ennek részeként meg kell adnia egy Azure-régiót, amelyben a tárfiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server adatbázisának és az Oozie-metaadattár SQL Server adatbázisának is ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.

Az alapértelmezett Blob tároló a fürtre jellemző információkat tárolja, például a feladatelőzményeket és a naplókat. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket, és a fürt hibásan működhet. Ajánlott különböző tárolót használni mindegyik fürthöz és a megosztott adatokat az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni az alapértelmezett tárfiók helyett. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása][hdinsight-creation]. De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. A HBase fürtök esetén megőrizheti a HBase táblasémát és adatokat, ha létrehoz egy új HBase fürtöt a törölt HBase fürt által használt alapértelmezett blobtárolóval.

#### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Amikor HDInsight-fürtöt hoz létre a portálról, megadhatja a tárfiók részleteit (az alábbiakban látható módon). Azt is megadhatja, hogy szeretne-e további tárfiókot társítani a fürttel, és ha igen, a Data Lake Store-t vagy más Azure Storage-blobot választhat további tárolóként.

![HDInsight hadoop létrehozási adatforrás](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

#### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Ha [telepítette és konfigurálta az Azure CLI parancssori felületet](../xplat-cli-install.md), a következő parancs használható a tárfiókokhoz és tárolóhoz.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> A(z) `--type` paraméter azt jelzi, hogyan lesz replikálva a tárfiók. További információ: [Azure Storage replication](../storage/storage-redundancy.md) (Az Azure Storage replikációja). Na használjon ZRS-t, mivel a ZRS nem támogatja a lapblobokat, a fájlokat, a táblákat vagy az üzenetsorokat.
> 
> 

A rendszer bekéri a tárfiók földrajzi régiójának megadását. Ugyanabban a régióban kell létrehoznia a tárfiókot, mint amelyben a HDInsight-fürt létrehozását tervezi.

A tárfiók létrehozása után a következő paranccsal kérheti le a tárfiók kulcsait:

    azure storage account keys list <storageaccountname>

Egy tároló létrehozásához használja az alábbi parancsot:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

#### <a name="using-azure-powershell"></a>Az Azure PowerShell használata
Ha [telepítette és konfigurálta az Azure PowerShellt][powershell-install], a következőt használhatja az Azure PowerShell-parancssorról egy tárfiók és tároló létrehozásához:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

### <a name="address-files-in-blob-storage"></a>A Blob Storage tárolóban található címfájlok
A Blob Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A &lt;BlobStorageContainerName&gt; azonosítja a tároló nevét az Azure Blob Storage tárolóban.
A &lt;StorageAccountName&gt; azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha a &lt;BlobStorageContainerName&gt; és a &lt;StorageAccountName&gt; sincs meghatározva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
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

### <a name="access-blobs-using-azure-cli"></a>Blobok elérése az Azure parancssori felület használatával
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

### <a name="access-blobs-using-azure-powershell"></a>Blobok elérése az Azure PowerShell eszköz használatával
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
    Login-AzureRmAccount 
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

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"


### <a name="using-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="using-azure-data-lake-store-with-hdinsight-clusters"></a>Az Azure Data Lake Store és a HDInsight-fürtök együttes használata

A HDInsight-fürtök kétféleképpen használhatják az Azure Data Lake Store-t:

* Azure Data Lake Store az alapértelmezett tárolóként
* Azure Data Lake Store kiegészítő tárolóként, ahol az Azure Storage-blob az alapértelmezett tároló.

> [!NOTE]
> Az Azure Data Lake Store elérése mindig biztonságos csatornán keresztül történik, így nincs `adls` fájlrendszer-sémanév. Mindig `adl` használandó.
> 
> 

### <a name="using-azure-data-lake-store-as-default-storage"></a>Az Azure Data Lake Store használata az alapértelmezett tárolóként

Ha a HDInsight alapértelmezett tárolójaként az Azure Data Lake Store van beállítva, a fürttel kapcsolatos fájlok az Azure Data Lake Store-ban találhatók a következő helyen:

    adl://mydatalakestore/<cluster_root_path>/

ahol `<cluster_root_path>` az Azure Data Lake Store-ban létrehozott mappa neve. Ha mindegyik fürthöz megadja a gyökér elérési útját, ugyanazt az Azure Data Lake Store-fiókot több fürthöz is használhatja. Így olyan beállítással rendelkezhet, ahol:

* Az&1;. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster1storage`
* A&2;. fürt a következő elérési utat használhatja: `adl://mydatalakestore/cluster2storage`

Vegye figyelembe, hogy mindét fürt ugyanazt a **mydatalakestore** nevű Data Lake Store-fiókot használja. Mindegyik fürt hozzáféréssel rendelkezik a saját gyökér fájlrendszeréhez a Data Lake Store-ban. Az Azure Portalon végzett üzembe helyezésekor a **/fürtök/\<fürtnév>** mappanevet kell használnia a gyökér elérési úthoz.

#### <a name="accessing-files-from-the-cluster"></a>Fájlok elérése a fürtből

Több módon érheti el az Azure Data Lake Store-ban lévő fájlokat HDInsight-fürtről.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökér elérési útját az adl:/// karakterláncra cseréli le. Így a fenti példában lecserélheti az `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` helyett az `adl:///` értéket használhatja.

        adl:///<file path>

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg. Ha például a fájl teljes elérési útja a következő:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Ehelyett a következő relatív elérési úttal is elérheti ugyanazon sample.log fájlt.

        /example/data/sample.log

### <a name="using-azure-data-lake-store-as-additional-storage"></a>Az Azure Data Lake Store használata kiegészítő tárolóként

A Data Lake Store a fürt kiegészítő tárolójaként is használható. Ilyen esetekben a fürt alapértelmezett tárolója egy Azure Storage Blob vagy egy Azure Data Lake Store-fiók lehet. Ha HDInsight-feladatokat futtat a kiegészítő tárolóként használt Azure Data Lake Store tárolóban tárolt adatokon, a fájlok teljes elérési útját kell használnia. Példa:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Vegye figyelembe, hogy most nincs **cluster_root_path** az URL-címben. Ez azért van, mert a Data Lake Store ebben az esetben nem alapértelmezett tároló, így csak meg kell adnia a fájok elérési útját.


### <a name="creating-hdinsight-clusters-with-access-to-data-lake-store"></a>Data Lake Store-hozzáféréssel rendelkező HDInsight-fürtök létrehozása

Kövesse az alábbi hivatkozásokat a Data Lake Store-hozzáféréssel rendelkező HDInsight-fürtök létrehozásának részletes utasításaiért.

* [A Portal használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [A PowerShell használata (alapértelmezett tárolóként beállított Data Lake Store-ral)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [A PowerShell használata (kiegészítő tárolóként beállított Data Lake Store-ral)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Azure-sablonok használata](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használhat HDFS-kompatibilis Azure Blob Storage és Azure Data Lake Store tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

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
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  

