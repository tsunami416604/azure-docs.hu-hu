---
title: Az Azure Blob fájlrendszer-illesztőprogramja Azure Data Lake Storage Gen2
description: A ABFS Hadoop fájlrendszer-illesztőprogramja
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ece85feff3c6eff9fc2348de70842204986952d5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855627"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Az Azure Blob fájlrendszer-illesztőprogramja (ABFS): Dedikált Azure Storage-illesztőprogram a Hadoop-hez

Azure Data Lake Storage Gen2ban lévő adatelérési módszerek egyike az [Hadoop fájlrendszeren](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html)keresztül történik. Data Lake Storage Gen2 lehetővé teszi, hogy az Azure Blob Storage hozzáférjen egy új illesztőprogramhoz, az Azure Blob fájlrendszer- `ABFS`illesztőprogramhoz vagy. A ABFS Apache Hadoop része, és a Hadoop számos kereskedelmi disztribúciójában szerepel. Az illesztőprogram használatával számos alkalmazás és keretrendszer fér hozzá az Azure Blob Storage adataihoz anélkül, hogy a kód kifejezetten hivatkozik a Data Lake Storage Gen2ra.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Korábbi képesség: A Windows Azure Storage Blob illesztőprogramja

A Windows Azure Storage Blob illesztőprogram vagy [WASB illesztőprogramja](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) az Azure Blob Storage eredeti támogatását biztosította. Ez az illesztőprogram olyan összetett feladatot hajtott végre, amely az Azure Blob Storage által elérhető objektum-tárolási stílus felületének hozzárendelését (a Hadoop fájlrendszer felülete megköveteli). Ez az illesztőprogram továbbra is támogatja ezt a modellt, nagy teljesítményű hozzáférést biztosít a Blobokban tárolt adatokhoz, de jelentős mennyiségű kódot tartalmaz a leképezés végrehajtásához, ami megnehezíti a karbantartást. Emellett bizonyos műveletek, mint például a [filesystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) és a [filesystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) a címtárakon való alkalmazásakor az illesztőprogramnak nagy mennyiségű műveletet kell végrehajtania (mivel az objektumok nem támogatják a címtárak támogatását), ami gyakran vezet csökkentheti a teljesítményt. A ABFS-illesztőprogram úgy lett kialakítva, hogy legyőzze a WASB rejlő hiányosságait.

## <a name="the-azure-blob-file-system-driver"></a>Az Azure Blob fájlrendszer-illesztőprogramja

A [Azure Data Lake Storage Rest-felületet](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) úgy tervezték, hogy támogassa a fájlrendszer szemantikai feladatait az Azure Blob Storageban. Mivel a Hadoop fájlrendszert úgy is tervezték, hogy támogassa ugyanazt a szemantikai feltételt, nincs szükség összetett leképezésre az illesztőprogramban. Így az Azure Blob fájlrendszer-illesztőprogram (vagy ABFS) a REST API egyszerű ügyfél-alátéte.

Vannak azonban olyan függvények, amelyeknek az illesztőprogramnak továbbra is végre kell hajtania a következőket:

### <a name="uri-scheme-to-reference-data"></a>Az adathivatkozás URI-sémája

A Hadoop-en belüli más fájlrendszer-implementációkkal összhangban a ABFS-illesztőprogram meghatározza a saját URI-sémáját, hogy az erőforrások (könyvtárak és fájlok) külön kezelhetők legyenek. Az URI-séma a [Azure Data Lake Storage GEN2 URI használatával](./data-lake-storage-introduction-abfs-uri.md)van dokumentálva. Az URI szerkezete:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

A fenti URI formátum használata esetén a standard Hadoop-eszközök és-keretrendszerek használhatók a következő erőforrásokra való hivatkozáshoz:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Belsőleg a ABFS-illesztőprogram az URI-ban megadott erőforrás (oka) t a fájlok és könyvtárak számára fordítja le, és hívásokat kezdeményez a Azure Data Lake Storage REST API ezekkel a hivatkozásokkal.

### <a name="authentication"></a>Authentication

A ABFS-illesztőprogram két hitelesítési módszert támogat, hogy a Hadoop alkalmazás biztonságosan hozzáférhessen egy Data Lake Storage Gen2-kompatibilis fiókban található erőforrásokhoz. Az elérhető hitelesítési sémák teljes részletességét az [Azure Storage biztonsági útmutatója](../common/storage-security-guide.md)tartalmazza. Ezek a következők:

- **Megosztott kulcs:** Ez lehetővé teszi a felhasználók számára a fiókban lévő összes erőforrás elérését. A kulcs titkosítva van, és a Hadoop-konfigurációban tárolódik.

- **Azure Active Directory OAuth tulajdonosi jogkivonata:** Az Azure AD tulajdonosi jogkivonatait az illesztőprogram szerzi be és frissíti a végfelhasználó vagy egy konfigurált szolgáltatásnév identitásával. Ennek a hitelesítési modellnek a használatával minden hozzáférés a megadott jogkivonathoz társított identitással és a hozzárendelt POSIX Access Control listával (ACL) kiértékelt hívási alapon minden hozzáférésre engedélyezve van.

### <a name="configuration"></a>Konfiguráció

A ABFS-illesztőprogram összes konfigurációja a <code>core-site.xml</code> konfigurációs fájlban van tárolva. A [Ambari](https://ambari.apache.org/)-t tartalmazó Hadoop-disztribúciók esetében a konfiguráció a webportál vagy a Ambari REST API használatával is kezelhető.

Az összes támogatott konfigurációs bejegyzés részletei a [hivatalos Hadoop dokumentációjában](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html)vannak megadva.

### <a name="hadoop-documentation"></a>A Hadoop dokumentációja

A ABFS-illesztőprogram teljes mértékben dokumentálva van a [hivatalos Hadoop dokumentációjában](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html)

## <a name="next-steps"></a>További lépések

- [Azure Databricks-fürt létrehozása](./data-lake-storage-quickstart-create-databricks-account.md)
- [Az Azure Data Lake Storage Gen2 URI használata](./data-lake-storage-introduction-abfs-uri.md)
