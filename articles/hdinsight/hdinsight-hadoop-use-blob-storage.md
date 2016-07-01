<properties
    pageTitle="Adatok lekérdezése HDFS-kompatibilis Blob Storage tárolóról | Microsoft Azure"
    description="A HDInsight az Azure Blob Storage tárolót használja a HDFS big data-táraként. Megtudhatja, hogyan kérdezhet le adatokat a Blob Storage tárolóból, és hogyan tárolhatja az eredményeket elemzéshez."
    keywords="blob storage,hdfs,structured data,unstructured data"
    services="hdinsight,storage"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="jgao"/>


# A HDFS-kompatibilis Azure Blob Storage és a Hadoop együttes használata a HDInsightban

Megtudhatja, hogyan használhat alacsony költségű Azure Blob Storage tárolót a HDInsight eszközzel, hogyan hozhat létre Azure-tárfiókot és Blob Storage tárolót, majd hogyan kezelheti a benne lévő adatokat.

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

Az adatok Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.

> [AZURE.NOTE]  Az *asv://* szintaxis nem támogatott a HDInsight 3.0-s verziójú fürtökben. Ez azt jelenti, hogy a HDInsight 3.0-s fürtbe elküldött, explicit módon az *asv://* szintaxist használó feladatok meghiúsulnak. Ehelyett a *wasb://* szintaxist kell használni. Ezenkívül az olyan meglévő metaadattárral létrehozott HDInsight 3.0-s verzióba küldött feladatok, amelyek az asv:// szintaxist használó erőforrásokra mutató explicit hivatkozásokat tartalmaznak, meghiúsulnak. Ezeket a metaadattárakat a wasb:// szintaxissal kell újra létrehozni erőforrások kezeléséhez.

> A HDInsight jelenleg csak blokk blobokat támogat.

> A legtöbb HDFS parancs (például az <b>ls</b>, a <b>copyFromLocal</b> és a <b>mkdir</b>) továbbra is a várt módon működik. Csak a natív HDFS implementációra ( más néven DFS-re) jellemző parancsok, például a <b>fschk</b> és a <b>dfsadmin</b> viselkednek máshogy az Azure Blob Storage tárolóban.

A HDInsight fürtök létrehozásával kapcsolatban további információért lásd: [Get Started with HDInsight][hdinsight-get-started] (HDInsight – első lépések) vagy [Create HDInsight clusters][hdinsight-creation] (HDInsight-fürtök létrehozása).


## HDInsight tároló-architektúra
A következő ábra a HDInsight tárló-architektúra absztrakt nézetét nyújtja:

![A Hadoop fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage tárolóban.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Blob Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


A Hadoop támogatja az alapértelmezett fájlrendszert. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight létrehozási folyamata alatt egy Azure Storage-fiók és a fiók adott Azure Blob Storage tárolója lesz kijelölve az alapértelmezett fájlrendszerként.

Ezen tárfiók mellett további tárfiókokat adhat hozzá ugyanazon Azure-előfizetésből vagy másik Azure-előfizetésekből a létrehozási folyamat során. A további tárfiókok hozzáadásáról útmutatásért lásd: [Create HDInsight clusters][hdinsight-creation] (HDInsight-fürtök létrehozása).

- **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

- **Fürthöz NEM csatlakozó nyilvános tárolók vagy nyilvános blobok a tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.

    > [AZURE.NOTE]
        > A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Restrict access to containers and blobs</a> (A tárolókhoz és blobokhoz való hozzáférés korlátozása).

- **Fürthöz NEM csatlakozó személyes tárolók a tárfiókokban:** Nem érheti el a tárolókban lévő blobokat, ha nem határozza meg a tárfiókot a WebHCat-feladatok elküldésekor. Ennek a magyarázatát a cikk későbbi részében találja.


A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Nem ajánlott a core-site.xml fájl módosítása, mert a fürt átjárócsomópontja (fő) rendszerképe bármikor alaphelyzetbe állítható vagy áttelepíthető, és ekkor ezen fájlok minden módosítása elveszik.

Több WebHCat-feladat (beleértve a Hive, MapReduce, Hadoop-stream és Pig-feladatokat) is tartalmazhatja a tárfiókok és a metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) Ezen cikk [Blobok elérése az Azure PowerShell eszközzel](#powershell) szakaszában találja ezen szolgáltatás egy mintáját. További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A Blob Storage tárolók a strukturált és strukturálatlan adatokhoz használhatók. A Blob Storage tárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

###<a id="benefits"></a>A Blob Storage előnyei
A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményi költségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat nagyon hatékonnyá teszi, ahogyan a számítási csomópontok elérik az Azure Blob Storage tárlóban lévő adatokat.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Blob Storage tárolóban lévő adatok a HDFS API-kon vagy a [Blob Storage REST API-kon][blob-storage-restAPI] keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.
* **Adatarchiválás:** Az adatok Azure Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.
* **Adattárolási költség:** Az adatok DFS-ben végzett hosszú távú tárolása költségesebb, mint az adatok Azure Blob Storage tárolóban végzett tárolása, mert a számítási fürt költsége nagyobb, mint az Azure Blob Storage tárolók költsége. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.
* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.
* **Georeplikáció:** Az Azure Blob Storage tárolókról gereplikálás készíthető. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.



## Blob tárolók létrehozása

A blobok használatához először hozzon létre egy [Azure Storage-fiókot][azure-storage-create]. Ennek részeként olyan Azure-régiót határozhat meg, amely ezzel a fiókkal fogja tárolni a létrehozott objektumokat. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server adatbázisának és az Oozie-metaadattár SQL Server adatbázisának is ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.


Az alapértelmezett Blob tároló a fürtre jellemző információkat tárolja, például a feladatelőzményeket és a naplókat. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket, és a fürt hibásan működhet. Ajánlott különböző tárolót használni mindegyik fürthöz és a megosztott adatokat az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni az alapértelmezett tárfiók helyett. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [Create HDInsight clusters][hdinsight-creation] (HDInsight-fürtök létrehozása). De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. A HBase fürtök esetén megőrizheti a HBase táblasémát és adatokat, ha létrehoz egy új HBase fürtöt a törölt HBase fürt által használt alapértelmezett blobtárolóval.


### Az Azure portál használata

Amikor HDInsight fürtöt hoz létre a portálról, használhat meglévő tárfiókokat, vagy létrehozhat új tárfiókot:

![hdinsight hadoop létrehozási adatforrás](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

###Az Azure parancssori felület használata

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Ha [telepítette és konfigurálta az Azure CLI parancssori felületet](../xplat-cli-install.md), a következő parancs használható a tárfiókokhoz és tárolóhoz.

    azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE] A(z) `--type` paraméter azt jelzi, hogyan lesz replikálva a tárfiók. További információ: [Azure Storage replication](../storage/storage-redundancy.md) (Az Azure Storage replikációja). Na használjon ZRS-t, mivel a ZRS nem támogatja a lapblobokat, a fájlokat, a táblákat vagy az üzenetsorokat.

A rendszer bekéri a tárfiók földrajzi régiójának megadását. Ugyanabban a régióban kell létrehoznia a tárfiókot, mint amelyben a HDInsight-fürt létrehozását tervezi.

A tárfiók létrehozása után a következő paranccsal kérheti le a tárfiók kulcsait:

    azure storage account keys list <storageaccountname>

Egy tároló létrehozásához használja az alábbi parancsot:

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### Az Azure PowerShell használata

Ha [telepítette és konfigurálta az Azure PowerShell eszközt][powershell-install], a következőt használhatja az Azure PowerShell parancssorról egy tárfiók és tároló létrehozásához:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

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

## A Blob Storage tárolóban található címfájlok

A Blob Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] A (HDInsight emulátoron futó) Storage Emulator eszközön a fájlok kezelésének szintaxisa a következő: <i>wasb://&lt;Tárolónév&gt;@tárolóemulátor</i>.



Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A &lt;BlobStorageContainerName&gt; azonosítja a tároló nevét az Azure Blob Storage tárolóban.
A &lt;StorageAccountName&gt; azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha a &lt;BlobStorageContainerName&gt; és a &lt;StorageAccountName&gt; sincs meghatározva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] A fájlnév a <i>hadoop-examples.jar</i> a HDInsight 2.1-es és 1.6-s verziójú fürtökben.


Az &lt;elérési út&gt; a fájl vagy könyvtár HDFS elérési útja neve. Mivel az Azure Blob Storage tárolóban lévő tárolók egyszerűen kulcs-érték tárolók, nincs valós hierarchikus fájlrendszer. A blob kulcson belüli perjel karaktert ( / ) a rendszer könyvtárelválasztóként értelmezi. A *hadoop-mapreduce-examples.jar* blobneve például a következő:

    example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE] Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

## Blobok elérése az Azure parancssori felület használatával

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

## Blobok elérése az Azure PowerShell eszköz használatával

> [AZURE.NOTE] Ezen szakasz parancsai a blobokban tárolt adatok PowerShell eszközön keresztüli elérésének egyszeri példáit nyújtják. A HDInsight használatához testreszabott további teljes példákért lásd: [HDInsight eszközök](https://github.com/Blackmist/hdinsight-tools).

Használja az alábbi parancsot a blobbal kapcsolatos parancsmagok listázásához:

    Get-Command *blob*

![A blobbal kapcsolatos PowerShell parancsmagok listája.][img-hdi-powershell-blobcommands]

###Fájlok feltöltése

Lásd: [Upload data to HDInsight][hdinsight-upload-data] (Adatok feltöltése a HDInsightba).

###Fájlok letöltése

A következő parancsfájl egy blokkblobot tölt le az aktuális mappába. A parancsfájl futtatása előtt módosítsa a könyvtárt olyan mappára, ahol írási engedélyei vannak.

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

###Fájlok törlése


    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

###Fájlok listázása

    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

###Hive-lekérdezések futtatása nem meghatározott tárfiókkal

Ez a példa bemutatja, hogyan listázhat mappákat a létrehozási folyamat során nem meghatározott tárfiókból.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## Következő lépések

Ebben a cikkben megtanulta, hogyan használhat HDFS-kompatibilis Azure Blob Storage tárolót a HDInsight eszközzel, és hogy az Azure Blob Storage a HDInsight alapvető alkotóeleme. Ez lehetővé teszi a méretezhető, hosszú távú adatarchiváló beszerzési megoldások kiépítését az Azure Blob Storage tárolóval, valamint hogy a HDInsight eszközzel feloldja a tárolt strukturált és strukturálatlan adatokban lévő információkat.

További információkért lásd:

* [Get Started with Azure HDInsight][hdinsight-get-started] (Azure HDInsight – első lépések)
* [Upload data to HDInsight][hdinsight-upload-data] (Adatok feltöltése a HDInsightba)
* [Use Hive with HDInsight][hdinsight-use-hive] (A Hive használata a HDInsightban)
* [Use Pig with HDInsight][hdinsight-use-pig] (A Pig használata a HDInsightban).
* [Use Azure Storage Shared Access Signatures to restrict access to data with HDInsight][hdinsight-use-sas] (Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban)

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  



<!--HONumber=Jun16_HO2-->


