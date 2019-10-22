---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Ismerje meg, hogyan kérdezheti le az Azure Storage és a Azure Data Lake Storage adatait az elemzés eredményeinek tárolásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71936854"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

A HDInsight-fürtben lévő adatelemzéshez az [Azure Storage](../storage/common/storage-introduction.md)-ban, [Azure Data Lake Storage 1](../data-lake-store/data-lake-store-overview.md) . generációs /[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)vagy egy kombinációban tárolhatók az adathalmazok. Ezek a tárolási beállítások lehetővé teszik a számításhoz használt HDInsight-fürtök biztonságos törlését felhasználói adatvesztés nélkül.

Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során megadhat egy BLOB-tárolót az Azure Storage-ban alapértelmezett fájlrendszerként, illetve a HDInsight 3,6-as verzióban, kiválaszthatja az Azure Storage-t vagy a Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen 2 értéket alapértelmezett fájlként a rendszer néhány kivétellel. Az 1. generációs Data Lake Storage az alapértelmezett és a csatolt tárolóként való használatának támogatásához tekintse meg a [HDInsight-fürt rendelkezésre állását](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)ismertető témakört.

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. Ha szeretné megtudni, hogyan működik Data Lake Storage Gen 1 HDInsight-fürtökkel, tekintse meg a [Azure Data Lake Storage használata az Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-store.md)című témakört. További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](hdinsight-hadoop-provision-linux-clusters.md).

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!IMPORTANT]  
> A Storage-fiók típusa **BlobStorage** csak másodlagos tárolóként használhatók a HDInsight-fürtökhöz.

| Storage-fiók típusa | Támogatott szolgáltatások | Támogatott teljesítményszint | Támogatott hozzáférési szintek |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (általános célú v2)  | Blob     | Standard                    | Gyakori, ritka elérésű, archív \*   |
| Storage (általános célú v1)   | Blob     | Standard                    | –                    |
| BlobStorage                    | Blob     | Standard                    | Gyakori, ritka elérésű, archív \*   |

Nem javasoljuk, hogy az üzleti adattároláshoz használja az alapértelmezett BLOB-tárolót. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló alkalmazás-és rendszernaplókat tartalmaz. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Egy blob-tároló megosztása, mivel a több fürt alapértelmezett fájlrendszere nem támogatott.

> [!NOTE]  
> Az archív hozzáférési szint egy olyan offline szint, amely több órás lekérési késéssel rendelkezik, és nem ajánlott a HDInsight-mel való használatra. További információ: az [archív hozzáférési szint](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Ha úgy dönt, hogy védi a Storage-fiókot a **tűzfalakkal és a virtuális hálózatokkal** kapcsolatos korlátozásokkal a **kiválasztott hálózatokon**, ügyeljen arra, hogy a kivételt engedélyezze a **megbízható Microsoft-szolgáltatások számára** ... így a HDInsight hozzáférhet a tárolóhoz fiók.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

A következő ábra az Azure Storage-ot használó HDInsight tároló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val férnek hozzá a blob Storage-ban tárolt és tárolt adattároláshoz](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight-tár architektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Az alábbiakban néhány szempont olvasható Azure Storage-fiók és HDInsight-fürtök együttes használatával kapcsolatban.

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Fürthöz NEM csatlakozó nyilvános tárolók vagy nyilvános blobok a tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.
  
> [!NOTE]  
> A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információ: [a tárolók és Blobok hozzáférésének kezelése](../storage/blobs/storage-manage-access-to-resources.md).

* Olyan **tárolók, amelyek nem csatlakoznak fürthöz:** A tárolóban lévő Blobok csak akkor érhetők el, ha a Storage-fiókot a Webhcaten-feladatok elküldésekor adja meg. Ennek a magyarázatát a cikk későbbi részében találja.

A létrehozási folyamatban és kulcsaikban definiált Storage-fiókok tárolása a fürtcsomópontok `%HADOOP_HOME%/conf/core-site.xml` történik. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Ezt a beállítást az [Apache Ambari](./hdinsight-hadoop-manage-ambari.md)használatával lehet módosítani.

Több Webhcaten-feladat, többek között a Apache Hive, a MapReduce, a Apache Hadoop streaming és az Apache Pig, a Storage-fiókok és-metaadatok leírását is elvégezheti. (Ez jelenleg a a Storage-fiókokkal, a metaadatok esetében azonban nem használható.) További információ: [HDInsight-fürt használata másodlagos Storage-fiókokkal és-metaadattárak](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blob-tárolók kulcs/érték párokként tárolják az értékeket, és nincs címtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Egy blob kulcsa lehet például az *input/log1.txt*. Nem létezik tényleges *input* könyvtár, de mivel jelen van a perjel karakter a kulcsnévben, úgy néz ki, mint egy fájlútvonal.

## <a id="benefits"></a>Az Azure Storage előnyei

A számítási fürtök és a tárolási erőforrások nem helyezhetők el egymással, a számítási fürtöket pedig az Azure-régióban található Storage-fiók erőforrásaihoz közeledve csökkentik, ahol a nagy sebességű hálózat teszi hatékonyabbá a számítási csomópontok az Azure Storage-ban tárolt adatok eléréséhez.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Storage-ban tárolt adatok a HDFS API-kon vagy a [blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)-kon keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.

* **Adatarchiválás:** Az adatok Azure Blob Storage tárolóban végzett tárolása lehetővé teszi, hogy biztonságosan törölje a számításhoz használt HDInsight fürtöket a felhasználói adatok elvesztése nélkül.

* **Adattárolási költség:** Az adatok DFS-ben végzett hosszú távú tárolása költségesebb, mint az adatok Azure Storage tárolóban végzett tárolása, mert a számítási fürt költsége nagyobb, mint az Azure Storage költsége. Továbbá, mivel az adatoknak nem kell újratölteniük minden számítási fürt létrehozásakor, az adatok betöltésével kapcsolatos költségeket is menteni fogjuk.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.

* **Georeplikáció:** Az Azure Storage tárolókról georeplikálás készíthető. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]  
> A legtöbb HDFS-parancs (például `ls`, `copyFromLocal` és `mkdir`) továbbra is a várt módon működik. Az Azure Storage-ban csak a natív HDFS-implementációra (azaz a DFS-re) jellemző parancsokra, például a `fschk`ra és a `dfsadmin`re van mód.

## <a name="address-files-in-azure-storage"></a>Az Azure Storage tárolóban található címfájlok

Az Azure Storage tárolóban a HDInsight eszközről végzett fájlelérés URI sémája a következő:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Az URI séma titkosítatlan hozzáférést (a *wasb:* előtaggal) és SSL titkosított hozzáférést (a *wasbs* előtaggal) biztosít. Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

A `<BlobStorageContainerName>` a blob-tároló nevét azonosítja az Azure Storage-ban.
A `<StorageAccountName>` azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

Ha sem `<BlobStorageContainerName>`, sem `<StorageAccountName>` meg van adva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. A HDInsight-fürtökben lévő *hadoop-mapreduce-examples.jar* fájlra például a következők egyikével lehet hivatkozni:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> A fájlnév a 2,1-es és 1,6-es HDInsight-verziókban `hadoop-examples.jar`.

Az elérési út a fájl vagy a könyvtár HDFS elérési útjának neve. Mivel az Azure Storage tárolói a kulcs-érték tárolók, nincs valós hierarchikus fájlrendszer. A blob kulcson belüli perjel karaktert ( / ) a rendszer könyvtárelválasztóként értelmezi. A *hadoop-mapreduce-examples.jar* blobneve például a következő:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>BLOB-tárolók

A Blobok használatához először létre kell hoznia egy [Azure Storage-fiókot](../storage/common/storage-create-storage-account.md). Ennek részeként meg kell adnia egy Azure-régiót, amelyben a tárfiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. Az Hive-metaadattár SQL Server adatbázisnak és az Apache Oozie metaadattár SQL Server adatbázisnak ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló egy már létező, a HDInsight eszközön kívül létrejövő blob vagy egy HDInsight-fürthöz létrehozott tároló lehet.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez károsíthatja a feladatelőzményeket. Javasoljuk, hogy használjon egy másik tárolót az egyes fürtökhöz, és helyezzen el megosztott adatokat egy társított Storage-fiókban, amely az alapértelmezett Storage-fiók helyett az összes releváns fürt központi telepítésében van megadva. A társított Storage-fiókok konfigurálásával kapcsolatos további információkért lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. A HBase-fürtök esetében ténylegesen megtarthatja a HBase tábla sémáját és az adattárolást úgy, hogy létrehoz egy új HBase-fürtöt a törölt HBase-fürt által használt alapértelmezett blob-tároló használatával.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interakció az Azure Storage szolgáltatással

A Microsoft az alábbi eszközöket biztosítja az Azure Storage-hoz való együttműködéshez:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portalra](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Ezen a tárfiókon kívül további tárfiókokat vehet fel ugyanabból az Azure-előfizetésből vagy más Azure-előfizetésekből a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez lehetővé teszi a skálázható, hosszú távú adatarchiváló beszerzési megoldások kiépítését, valamint hogy a HDInsighttal kinyerje a strukturált és strukturálatlan tárolt adatokban lévő információkat.

További információ eléréséhez lásd:

* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Ismerkedés a Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Adatok feltöltése a HDInsight](hdinsight-upload-data.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](hadoop/hdinsight-use-pig.md)
* [Az Azure Storage közös hozzáférésű aláírásait használva korlátozza az adathozzáférést a HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-storage-gen2.md)
