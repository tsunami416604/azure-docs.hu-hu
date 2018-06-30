---
title: Azure Data Lake tárolási Gen2 előzetes használata Azure HDInsight-fürtök
description: 'Útmutató: Azure Data Lake tárolási Gen2 előzetes adatok lekérdezése és tárolhatja az eredményeket elemzéshez.'
keywords: hdfs, strukturált adatok, strukturálatlan adatok, data lake store, Hadoop bemeneti, Hadoop kimeneti, hadoop tárolási, hdfs bemeneti, hdfs kimeneti, hdfs tárolási, azure wasb
services: hdinsight,storage
documentationcenter: ''
tags: azure-portal
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: d5b67d971c2261084857d6b512c8d011173884af
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113255"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Azure Data Lake tárolási Gen2 előzetes használata Azure HDInsight-fürtök

A HDInsight-fürt adatok elemzésére, a adatait tárolhatja az Azure Storage, az Azure Data Lake tárolási Gen1 és az Azure Data Lake tárolási Gen2 előzetes bármely kombinációjával. Az összes tároló-beállítások engedélyezése, hogy biztonságosan törölje a felhasználói adatok elvesztése nélkül a számításhoz használt HDInsight fürtöket.

A Hadoop támogatja az alapértelmezett fájlrendszert. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight fürt létrehozása során megadhatja egy blob-tároló Azure Storage vagy az Azure Data Lake Storage az alapértelmezett fájlrendszert. Azt is megteheti a HDInsight 3.5 kiválaszthatja is vagy az Azure Storage, vagy az Azure Data Lake tárolási néhány kivételtől eltekintve alapértelmezett fájlrendszert.

Ebből a cikkből megismerheti, hogyan Azure Data Lake tárolási Gen2 működik együtt a HDInsight-fürtök. HDInsight-fürtök létrehozásával kapcsolatos további információkért lásd: [állítsa be a HDInsight-fürtök Azure Data Lake Storage használata a Hadoop, Spark, Kafka és több](quickstart-create-connect-hdi-cluster.md).

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. HDInsight a fürt használhatja az alapértelmezett fájlrendszert Azure Data Lake-tároló. Egy Hadoop elosztott fájlrendszer (HDFS) rendszer felületen a hdinsight összetevők teljes készlete működhet közvetlenül az Azure Data Lake Storage fájlokon.

Nem javasoljuk, hogy az alapértelmezett fájlrendszert használ üzleti adatok tárolására. Az alapértelmezett fájlrendszert után minden használat tárolási költségek csökkentése a bevált gyakorlat törlése Megjegyzés: alapértelmezett tároló tartalmazza az alkalmazás- és naplókat. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

A megosztás több fürthöz egy fájlrendszer nem támogatott.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

A következő ábra az Azure Storage-ot használó HDInsight tároló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage-ban.](./media/use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight-tárarchitektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Data Lake Storage tárolt adatok eléréséhez. A szintaxis a következő:

    abfs[s]://<file_system>@<accountname>.dfs.core.windows.net/<path>

Egy Azure Storage-fiók használata a HDInsight-fürtök esetén az alábbiakban néhány szempontot.

* **A fürt csatlakozó tárfiókokban lévő fájlok** a fiók neve, valamint a létrehozásakor a fürthöz tartozó kulcs. Ez a konfiguráció hozzáférést biztosít a fájlok a fájlrendszerben.

* **A storage-fiókok fürthöz nem csatlakozó nyilvános fájlok** teszi közzé a csak olvasási engedéllyel a fájlokat a fájlrendszerben.
  
  > [!NOTE]
  > Nyilvános fájlrendszerek engedélyezi, hogy a fájlrendszer elérhető fájlok listáját, és metaadatok hozzáférési. Nyilvános fájlrendszerek lehetővé teszi fájlok eléréséhez, csak akkor, ha ismeri a pontos URL-CÍMÉT. További információkért lásd: [tárolók és blobok való hozzáférés korlátozása](http://msdn.microsoft.com/library/windowsazure/dd179354.aspx) (a tárolók és blobok vonatkozó szabályokat az ugyanaz: előtér munkahelyi fájlokat és a fájlrendszerhez).
 
* **A storage-fiókok fürthöz nem csatlakozó személyes fájlrendszerek** nem teszik lehetővé hozzáférési fájlokat a fájlrendszerben kivéve, ha a WebHCat-feladatok elküldésekor határozza meg a tárfiókot. Ez a korlátozás okait magyarázatát a cikk későbbi részében.

A létrehozási folyamat és a kulcsaik meghatározott tárfiókok vannak tárolva *%HADOOP_HOME%/conf/core-site.xml* a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése, hogy meghatározott tárfiókok használata a *core-site.xml* fájlt. A beállítást az [Ambari](/hdinsight/hdinsight-hadoop-manage-ambari.md) használatával módosíthatja

Több WebHCat-feladat (beleértve a Hive, MapReduce, Hadoop-stream és Pig-feladatokat) is tartalmazhatja a tárfiókok és a metaadatok leírását. (Ez a megközelítés jelenleg működik a Pig-feladatokkal tárfiókokkal, de a metaadatok nem.) További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

## <a id="benefits"></a>Az Azure Storage előnyei

A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményköltségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat hatékonnyá teszi a számítási csomópontok számára az Azure Storage-ban lévő adatok elérését.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Blob Storage-ban lévő adatok a HDFS API-kon vagy a [Blob Storage REST API-kon][blob-storage-restAPI] keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.

* **Adatarchiválás:** Az adatok Azure Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.

* **Adattárolási költség:** adatok tárolása a natív HDFS-ben, a hosszú távú költségesebb, mint az adatok tárolása az Azure-tárfiókba, mert a számítási fürt költsége nagyobb, mint az Azure storage költségét. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.

* **Georeplikáció:** adatait az Azure storage georeplikált lehet. Bár ez a lehetőség lehetővé teszi az földrajzi helyreállítást és adatredundanciát, a georeplikált helyre a feladatátvételt súlyosan támogatása hatással van a teljesítményre, és további költségeket vonhat. Ezért, válassza ki a georeplikáció, gondosan és csak ha az érték az adatok megérik a további költségeket.

* **Életciklus-felügyeletének:** bármely fájlrendszerben lévő összes adatot a saját teljes életciklusukon keresztül kerül. Adatok ki nagyon értékes és gyakran használt gyakran kezdődik, kevesebb értékes alatt, és kevesebb hozzáférést igénylő állapotba, és végső soron szükséges archiválása vagy törlése. Az Azure Storage biztosít adatokat rétegezéséhez és életciklus adatok megfelelően réteg esetében a életciklusfázis-kezelési házirenddel.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatok használja a natív HDFS implementációra (amely nevezzük DFS).

> [!NOTE]
> A legtöbb HDFS parancs (például `ls`, `copyFromLocal` és `mkdir`) továbbra is a várt módon működik. Csak azok a parancsok, amelyek használhatók az elosztott fájlrendszer, például a `fschk` és `dfsadmin`, az Azure storage másképp viselkednek megjelenítése.

## <a name="create-an-data-lake-storage-file-system"></a>Hozzon létre egy Data Lake tárolási fájlrendszer

A fájlrendszer használatához először létre kell hoznia egy [Azure Storage-fiók][azure-storage-create]. Ez a folyamat részeként adjon meg egy Azure-régió, ahol a tárfiók létrehozása. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server adatbázisának és az Oozie-metaadattár SQL Server adatbázisának is ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Data Lake-tárfiókra rendszerben egy fájl tartozik. 

Az alapértelmezett Data Lake-tárolási fájl rendszer tárolja a fürtökkel kapcsolatos információkat, például a feladatelőzményeket és a naplókat. Windows operációs rendszer alapértelmezett Data Lake tárolási ne ossza meg több HDInsight-fürtökkel. Ez károsíthatja a feladatelőzményeket. Javasoljuk, hogy új fájlrendszer használni mindegyik fürthöz és a megosztott adatokat az alapértelmezett tárfiók helyett az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása][hdinsight-creation]. Windows operációs rendszer alapértelmezett tárolási azonban az eredeti HDInsight fürt törlése után is felhasználhatja. A HBase fürtök esetén is megőrzi a HBase táblasémát és adatokat hozzon létre egy új HBase fürtöt a törölt törölt HBase fürt által használt alapértelmezett blob tárolókat.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

HDInsight-fürtöt a portálról létrehozásakor közül az (ahogy az alábbi képernyőfelvételen) adja meg a tárolási fiók adatait. Megadhatja, hogy kívánja-e egy további tárfiók a fürthöz tartozó, és ha igen, választhat a rendelkezésre álló tár beállításokat a további tárhely.

![HDInsight hadoop létrehozási adatforrás](./media/use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

### <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ha Ön [telepítette és konfigurálta az Azure PowerShell][powershell-install], egy tárfiók és tároló létrehozásához az Azure PowerShell-parancssorból a következő kódot használhatja:

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> Tároló létrehozása az azonos a fájlrendszer létrehozása az Azure Data Lake Storage.

### <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

Ha [telepítette és konfigurálta az Azure CLI parancssori felületet](../../cli-install-nodejs.md), a következő parancs használható a tárfiókokhoz és tárolóhoz.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> A nyilvános előzetes a Data Lake tárolási Gen2 csak `--sku Standard_LRS` esetén támogatott.

A rendszer kéri, hogy adja meg a földrajzi régiót, amelyben a tárfiók létre lett hozva. A storage-fiók létrehozása, amely a HDInsight-fürt létrehozását tervezi ugyanabban a régióban.

A tárfiók létrehozása után a következő paranccsal kérheti le a tárfiók kulcsait:

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

Egy tároló létrehozásához használja az alábbi parancsot:

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> Tároló létrehozása az azonos a fájlrendszer létrehozása az Azure Data Lake Storage.

## <a name="address-files-in-azure-storage"></a>Az Azure Storage tárolóban található címfájlok

Az Azure Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>

Az URI séma titkosítatlan hozzáférést biztosít (az a *abfs:* előtag) és SSL titkosított hozzáférést (a *abfss*). Azt javasoljuk, *abfss* lehetőség, akkor is, ha az Azure-ban ugyanabban a régióban adatokat éri.

* &lt;FILE_SYSTEM_NAME&gt; elérési útját a fájlrendszerben Azure Data Lake tárolási azonosítja.
* &lt;Fióknév&gt; azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

    Ha értékei &lt;FILE_SYSTEM_NAME&gt; sem &lt;fióknév&gt; van megadva, az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. Például a *hadoop-mapreduce-examples.jar* a HDInsight-fürtök fájlt a következő elérési utak egyikének használatával lehet hivatkozni:
    
        abfs://myfilesystempath@myaccount.dfs.core.widows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> A fájlnév a *hadoop-examples.jar* a HDInsight 2.1-es és 1.6-s verziójú fürtökben.

* A &lt;ELÉRÉSI&gt; a fájl vagy könyvtár HDFS elérési út.

> [!NOTE]
> A HDInsight eszközön kívüli fájlok használata, amikor legtöbb segédprogram nem ismeri fel a ABFS formázása, és helyette, mint az egy alapvető elérési út formátumot várhatóan `example/jars/hadoop-mapreduce-examples.jar`.
 
## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](/hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ezzel a megközelítéssel, méretezhető, hosszú távú, archiválási adatok beszerzési megoldások, és használja a HDInsight az információk belül a tárolt strukturált és strukturálatlan adatok zárolásának feloldásához.

További információkért lásd:

* [Az Azure Data Lake tárolási Gen2 ABFS Hadoop-fájlrendszer illesztőprogramját](abfs-driver.md)
* [Az Azure Data Lake Storage bemutatása](introduction.md)
* [Hadoop, Spark, Kafka és további Azure Data Lake Storage használata a HDInsight-fürtök beállítása](quickstart-create-connect-hdi-cluster.md)
* [Az Azure Data Lake tárolóhely-ból a distcp betöltik az adatokat](use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: /hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: /storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/use-hdi-cluster/HDI.PowerShell.BlobCommands.png
