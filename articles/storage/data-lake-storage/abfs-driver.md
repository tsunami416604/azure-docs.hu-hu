---
title: Az Azure Blob fájlrendszer illesztőprogram az Azure Data Lake tárolási Gen2 előzetes
description: A Hadoop-fájlrendszer ABFS illesztőprogram
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114333"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Az Azure Blob fájlrendszer illesztőprogram (ABFS): a Hadoop egy dedikált Azure Storage-illesztőprogram

Az Azure Data Lake tárolási Gen2 előnézetben lévő adatok elsődleges hozzáférési metódusokat egyik keresztül a [Hadoop-fájlrendszer](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Az Azure Data Lake tárolási Gen2 szolgáltatások egy társított illesztőprogram, az Azure Blob fájlrendszer illesztőprogram vagy `ABFS`. ABFS Apache Hadoop része, és a Hadoop kereskedelmi disztribúciók számos tartalmazza. Az illesztőprogram használ, számos alkalmazás és keretrendszerek nélkül érhetik el a Data Lake tárolási Gen2 adatok bármely kód explicit módon a Data Lake tárolási Gen2 szolgáltatásra hivatkozik.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Előzetes funkció: A Windows Azure Storage-Blobba illesztőprogram

A Windows Azure Storage-Blobba illesztőprogram vagy [WASB illesztőprogram](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) megadott Azure Storage Blobs eredeti támogatása. Az illesztőprogram végre szemantikáját (szükség szerint a Hadoop-fájlrendszer felület), hogy az objektum, amely tárolja a stílus felületet Azure Blob Storage által elérhetővé tett leképezési fájlrendszer összetett feladatánál. Az illesztőprogram továbbra is támogatja az ebben a modellben, nagy teljesítményű hozzá lehet férni a blobokat, tárolt adatokat, de ez a leképezés, megnehezítve a karbantartása végrehajtása kód jelentős mennyiségű tartalmaz. Emellett bizonyos műveletek, például [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) és [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) címtárakhoz alkalmazásakor szükség (objektum tárolók hiánya miatt műveletek túlnyomó számos illesztőprogram a könyvtárak támogatása) amely gyakran vezet a teljesítmény csökkenését.

Ebből kifolyólag a WASB rejlő tervezési hiányosságait megoldásához, az új Azure Data Lake Storage szolgáltatás az új ABFS illesztőprogram-támogatást lett megvalósítva.

## <a name="the-azure-blob-file-system-driver"></a>Az Azure Blob fájlrendszer illesztőprogram

A [Azure Data Lake Storage REST-felület](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) fájlrendszer szemantikáját támogató Azure Blob Storage keresztül. Fényében, hogy a Hadoop-fájlrendszer is célja, hogy támogatják a azonos szemantikát esetében nem követelmény az illesztő egy összetett leképezés. Így az Azure Blob illesztőprogram (vagy ABFS) egy egyszerű ügyfél segédkód a REST API.

Van azonban néhány funkció, amely az illesztőprogram továbbra is el kell végeznie:

### <a name="uri-scheme-to-reference-data"></a>A referenciaadatok URI-séma

Konzisztens más fájlrendszer esetében Hadoop belül, a ABFS illesztőprogram határozza meg a saját URI-séma, hogy az erőforrások (fájlok és könyvtárak) jelszórészek egyértelmű kell megoldani. Az URI-séma részletes ismertetését lásd: [használja az Azure Data Lake tárolási Gen2 URI](./introduction-abfs-uri.md). Az URI a struktúra a következő: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

A fenti URI formátumot használja, szabványos Hadoop-eszközök és keretrendszerek használhatók ezek erőforrásokra kell hivatkoznia:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Belső a ABFS illesztőprogram fordítja le a fájlok és könyvtárak URI azonosítójában megadott (oka) t, és az Azure Data Lake Storage REST API ezeket a hivatkozásokat hívásakor.

### <a name="authentication"></a>Hitelesítés

A ABFS illesztőprogram jelenleg akkor támogatja a megosztott kulcsos hitelesítést, így előfordulhat, hogy a Hadoop alkalmazás biztonságosan hozzáférjenek a Data Lake tárolási Gen2 belül található erőforrásokhoz. A kulcs titkosított és Hadoop konfigurációs tárolja.

### <a name="configuration"></a>Konfiguráció

A ABFS illesztőprogram minden beállításának tárolja a <code>core-site.xml</code> konfigurációs fájlt. A Hadoop-disztribúció kiemeli [Ambari](http://ambari.apache.org/), a konfiguráció előfordulhat, hogy is kezelhetők a webes portál vagy az Ambari REST API használatával.

Az összes konfigurációs részletek vannak megadva a [hivatalos Hadoop-dokumentáció](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Hadoop-dokumentáció

A ABFS illesztőprogram teljesen részletes ismertetését lásd: a [hivatalos Hadoop-dokumentáció](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>További lépések

- [A telepítő a HDInsight-fürtök](./quickstart-create-connect-hdi-cluster.md)
- [Egy Azure Databricks fürt létrehozása](./quickstart-create-databricks-account.md)
- [Használja az Azure Data Lake tárolási Gen2 URI](./introduction-abfs-uri.md)
