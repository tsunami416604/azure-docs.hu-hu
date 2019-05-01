---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Ismerje meg, hogyan kérdezhet le adatokat az Azure storage és az Azure Data Lake Storage tárolja, az elemzések eredményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 24e0b61dfd9950a5c5990f8341e32d048453c5d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689568"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

HDInsight-fürtben lévő adatok elemzéséhez, az adatokat tárolhatja akár a [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage általános 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage általános 2](../storage/blobs/data-lake-storage-introduction.md), vagy a kettő kombinációjához. A tárolási lehetőségek engedélyezése felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight-fürtök biztonságos törlését.

Az Apache Hadoop támogatja az alapértelmezett fájlrendszer. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight fürt létrehozása során megadhatja egy blob-tárolóba az Azure Storage, az alapértelmezett fájlrendszerként, vagy a HDInsight 3.6-os, kiválaszthatja az Azure Storage vagy az Azure Data Lake Storage általános 1 / Azure Data Lake Storage, az alapértelmezett fájlok 2. generációs gyűjtések a rendszer néhány kivétellel. Az alapértelmezett és a társított storage használatával a Data Lake Storage általános 1 támogathatóság, lásd: [HDInsight-fürt rendelkezésre állási](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. A HDInsight-fürtökkel Data Lake Storage általános 1 működésével kapcsolatban lásd: [használata Azure Data Lake Storage az Azure HDInsight-fürtök](hdinsight-hadoop-use-data-lake-store.md). Egy HDInsight-fürt létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!WARNING]  
> A tárfiók típusának **BlobStorage** csak akkor használható másodlagos tárolóként HDInsight-fürtök esetén.

| A tárfiók típusának | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott elérési szint |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (általános célú v2)  | Blob     | Standard                    | Gyakori, ritka elérésű, archív tárolási szint\*   |
| Storage (általános célú v1)   | Blob     | Standard                    | –                    |
| BlobStorage                    | Blob     | Standard                    | Gyakori, ritka elérésű, archív tárolási szint\*   |

Az alapértelmezett blobtárolóban nem ajánlott üzleti adatokat tárolni. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló tartalmazza, az alkalmazás- és naplókat. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Egy blobtároló alapértelmezett fájlrendszerként való, több fürt közötti megosztása nem támogatott.

> [!NOTE]  
> Az archivált adatok hozzáférési szintje egy kapcsolat nélküli réteg egy több órás késése és a HDInsight segítségével nem ajánlott. További információkért lásd: [archivált adatok hozzáférési szintje](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

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
> A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: [tárolókhoz és blobokhoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

* **A storage-fiókok fürthöz nem csatlakozó személyes tárolók:** Nem férhet hozzá a tárolókban lévő blobokat, kivéve, ha a WebHCat-feladatok elküldésekor határozza meg a tárfiókot. Ennek a magyarázatát a cikk későbbi részében találja.

A storage-fiókok a létrehozási folyamat és a kulcsaik meghatározott tárolt `%HADOOP_HOME%/conf/core-site.xml` a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Ez módosítható használatának beállítása [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Több WebHCat-feladatok, többek között az Apache Hive, MapReduce, az Apache Hadoop-Stream és az Apache Pig, képes továbbítani a storage-fiókok és a velük metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blobtárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

## <a id="benefits"></a>Az Azure Storage előnyei
Az, ahogyan a számítási fürtök belül az Azure-régió, ahol a nagysebességű hálózat hatékonnyá teszi, az a tárfiók erőforrásainak közelében való létrehozása teljesítményköltségeket csökkenti a számítási fürtök és tárolási erőforrások nem mentesülhetnek teljesítményi költségeket a a számítási csomópontok elérik az Azure storage lévő adatok.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** Az adatok a HDFS-ben a számítási fürtön belül találhatók. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Storage-adatok a HDFS API-kon keresztül vagy keresztül elérhető a [Blob Storage REST API-k](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.

* **Adatarchiválás:** Adatok az Azure storage tárolóban végzett tárolása lehetővé teszi, hogy a, hogy biztonságosan felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight fürtöket.

* **Adattárolási költség:** Az elosztott Fájlrendszerbeli adatok a hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure storage-ban, mert a számítási fürt költsége nagyobb, mint az Azure storage költsége. Ezenkívül mivel az adatokat nem kell újból betölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a méretezési csoport, amely a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.

* **Georeplikáció:** Az Azure storage georeplikált is lehet. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]  
> A legtöbb HDFS parancs (például `ls`, `copyFromLocal` és `mkdir`) továbbra is a várt módon működik. Csak azok a parancsok, mint például a natív HDFS implementációra (más nevezzük az elosztott Fájlrendszerbeli), jellemző `fschk` és `dfsadmin`, eltérő viselkedéssel megjelenítése az Azure storage-ban.

## <a name="address-files-in-azure-storage"></a>Az Azure Storage tárolóban található címfájlok
Az Azure Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A `<BlobStorageContainerName>` azonosítja az Azure BLOB storage a blob-tároló nevét.
A `<StorageAccountName>` azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha sem `<BlobStorageContainerName>` sem `<StorageAccountName>` lett megadva, az alapértelmezett fájlrendszer szolgál. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> A Fájlnév `hadoop-examples.jar` HDInsight 2.1-es és 1.6-os verziójának fürtökben.

Az elérési út a fájl vagy könyvtár HDFS elérési útja. Mivel az Azure BLOB storage tárolók kulcs-érték tárolók, nincs valós hierarchikus fájlrendszer. A blob kulcson belüli perjel karaktert ( / ) a rendszer könyvtárelválasztóként értelmezi. A *hadoop-mapreduce-examples.jar* blobneve például a következő:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>BLOB-tárolók
Blobok használatához először hozzon létre egy [Azure Storage-fiók](../storage/common/storage-create-storage-account.md). Ennek részeként meg kell adnia egy Azure-régiót, amelyben a tárfiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server-adatbázis és az Apache Oozie-metaadattár SQL Server-adatbázis található ugyanabban a régióban is kell.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket. Ajánlott különböző tárolót használni mindegyik fürthöz és a megosztott adatokat az összes kapcsolódó fürt üzemelő példányában meghatározott kapcsolt tárfiókra helyezni az alapértelmezett tárfiók helyett. A kapcsolt tárfiókok konfigurálásáról további információkért lásd: [létre HDInsight-fürtök](hdinsight-hadoop-provision-linux-clusters.md). De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. Az HBase fürtök esetén megőrizheti az HBase táblasémát és adatokat, ha létrehoz egy új HBase-fürtöt a törölt HBase-fürt által használt alapértelmezett blobtárolóval.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Az Azure storage használata

A következő eszközök az Azure Storage használatához a Microsoft biztosítja:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információkért lásd:

* [Az Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Ismerkedés az Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Adatok feltöltése a HDInsight](hdinsight-upload-data.md)
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [Az Azure Storage közös hozzáférésű Jogosultságkódokat használata a HDInsight-adatokhoz való hozzáférés korlátozása](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](hdinsight-hadoop-use-data-lake-storage-gen2.md)