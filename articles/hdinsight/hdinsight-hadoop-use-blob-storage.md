---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Ismerje meg, hogyan kérdezheti le az adatokat az Azure Storage-ból és az Azure Data Lake Storage-ból az elemzés eredményeinek tárolásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: cd6ba50cf81b93da887134e89d75313acb6bd936
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869858"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

Az [Azure Storage,](../storage/common/storage-introduction.md)az [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)vagy az Azure Data Lake Storage Gen [2](../storage/blobs/data-lake-storage-introduction.md)szolgáltatásban tárolhatja az adatokat. Vagy ezek a lehetőségek kombinációja. Ezek a tárolási lehetőségek lehetővé teszik a számítási célokra használt HDInsight-fürtök biztonságos törlését a felhasználói adatok elvesztése nélkül.

Az Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során megadhatja a blob tároló az Azure Storage alapértelmezett fájlrendszerként. Vagy a HDInsight 3.6-os, kiválaszthatja az Azure Storage vagy az Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 alapértelmezett fájlrendszer, néhány kivételtől eltekintve. A Data Lake Storage Gen 1 alapértelmezett és csatolt tárolóként való használatának támogatása a [HDInsight-fürt elérhetősége című témakörben található.](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. Ha meg szeretné tudni, hogyan működik a Data Lake Storage Gen 1 a HDInsight-fürtökkel, olvassa el [az Azure Data Lake Storage használata Azure HDInsight-fürtökkel.](hdinsight-hadoop-use-data-lake-store.md) A HDInsight-fürtök létrehozásáról az [Apache Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál további információt.

> [!IMPORTANT]  
> **A BlobStorage-tároló** típusú tárfiók csak a HDInsight-fürtök másodlagos tárolójaként használható.

| Tárfiók-típusú | Támogatott szolgáltatások | Támogatott teljesítményszintek |Nem támogatott teljesítményszintek| Támogatott hozzáférési szintek |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (általános célú v2)  | Blob     | Standard                    |Prémium| Forró, Hűvös, Archívum\*   |
| Tároló (általános célú v1)   | Blob     | Standard                    |Prémium| N/A                    |
| BlobStorage tároló                    | Blob     | Standard                    |Prémium| Forró, Hűvös, Archívum\*   |

Nem javasoljuk, hogy az alapértelmezett blob tároló üzleti adatok tárolására használja. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló alkalmazás- és rendszernaplókat tartalmaz. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Nem támogatott egy blobtároló megosztása több fürt alapértelmezett fájlrendszereként.

> [!NOTE]  
> Az archív hozzáférési szint egy offline réteg, amely több órás lekérési késést, és nem ajánlott a HDInsight használata. További információ: [Archív hozzáférési szint.](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)

## <a name="access-files-from-within-cluster"></a>Fájlok elérése a fürtből

A Data Lake Storage-ban található fájlok hdinsight-fürtről többféleképpen érhetők el. Az URI-séma titkosítatlan hozzáférést biztosít (a *wasb:* előtaggal) és a TLS titkosított hozzáférést *(wasbs-szel).* Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel a fürtgyökérhez vezető elérési utat a következőre cserélheti:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Példák adatelérési példák

Példák alapulnak [ssh kapcsolatot](./hdinsight-hadoop-linux-use-ssh-unix.md) a fürt fő csomópontja. A példák mindhárom URI-sémát használják. Cserélje `CONTAINERNAME` `STORAGEACCOUNT` ki és a vonatkozó értékeket

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancsok

1. Hozzon létre egy fájlt a helyi tárolón.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürttárolón.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürttárolóba.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. A fürttároló könyvtártartalmának listázása.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Hive-tábla létrehozása

Három fájlhely jelenik meg szemléltető célokra. Tényleges végrehajtáshoz csak az `LOCATION` egyik tételt használja.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Fájlok elérése a fürtön kívülről

A Microsoft a következő eszközöket biztosítja az Azure Storage szolgáltatással való munkához:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Az Ambari tárolási útvonalának azonosítása

* A konfigurált alapértelmezett tároló teljes elérési útjának azonosításához keresse meg a következő t:

    **HDFS** > **Configs** `fs.defaultFS` és írja be a szűrő beviteli mezőbe.

* Annak ellenőrzéséhez, hogy a wasb tároló másodlagos tárolóként van-e konfigurálva, keresse meg a következő t:

    **HDFS** > **Configs** `blob.core.windows.net` és írja be a szűrő beviteli mezőbe.

Az elérési út Ambari REST API használatával történő beszerzéséhez [olvassa el Az alapértelmezett tároló beszerzése](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)című témakört.

## <a name="blob-containers"></a>Blob-tárolók

A blobok használatához először hozzon létre egy [Azure Storage-fiókot](../storage/common/storage-create-storage-account.md). Ennek a lépésnek a részeként adja meg az Azure-régióban, ahol a tárfiók jön létre. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive metastore SQL Server adatbázis és az Apache Oozie metastore SQL Server adatbázisnak ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló lehet egy meglévő blob a HDInsighton kívül létrehozott. Vagy lehet, hogy egy HDInsight-fürthöz létrehozott tároló.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez a művelet megronthatja a feladatelőzményeket. Javasoljuk, hogy minden fürthöz használjon egy másik tárolót. Helyezze a megosztott adatokat az alapértelmezett tárfiók helyett az összes megfelelő fürthöz megadott csatolt tárfiókra. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. HBase-fürtök esetén a HBase táblasémát és az adatokat megtarthatja egy új HBase-fürt létrehozásával a törölt HBase-fürt által használt alapértelmezett blobtároló használatával.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. A létrehozási folyamat során további tárfiókokat is hozzáadhat ugyanabból az Azure-előfizetésből vagy különböző Azure-előfizetésekből. Vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez a tárhely lehetővé teszi, hogy alkalmazkodó, hosszú távú, archiválási adatgyűjtési megoldásokat hozzon létre, és a HDInsight segítségével feloldja a tárolt strukturált és strukturálatlan adatokon belüli információkat.

További információkért lásd:

* [Ismerkedés az Azure HDInsight-mal](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Ismerkedés az Azure Data Lake Storage-szal](../data-lake-store/data-lake-store-get-started-portal.md)
* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezésével](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
