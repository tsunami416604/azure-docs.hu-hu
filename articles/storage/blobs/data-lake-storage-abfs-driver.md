---
title: Az Azure Data Lake Storage Gen2 Azure Blob fájlrendszer-illesztőprogramja
description: Az ABFS Hadoop fájlrendszer illesztőprogramja
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970279"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Az Azure Blob Fájlrendszer illesztőprogramja (ABFS): Dedikált Azure Storage-illesztőprogram a Hadoop számára

Az Azure Data Lake Storage Gen2-ben az adatok elsődleges hozzáférési módszereinek egyike a [Hadoop Fájlrendszeren](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html)keresztül történik. A Data Lake Storage Gen2 lehetővé teszi az Azure Blob Storage `ABFS`felhasználói számára az új illesztőprogramhoz, az Azure Blob fájlrendszer-illesztőprogramhoz vagy a . Az ABFS az Apache Hadoop része, és a Hadoop számos kereskedelmi disztribúciójában szerepel. Ezzel az illesztőprogrammal számos alkalmazás és keretrendszer hozzáférhet az Azure Blob Storage-ban lévő adatokhoz anélkül, hogy a Data Lake Storage Gen2-re kifejezetten hivatkozna.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Korábbi képesség: A Windows Azure Storage Blob illesztőprogramja

A Windows Azure Storage Blob illesztőprogramja vagy [WASB-illesztőprogram](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) jattt az Azure Blob Storage eredeti támogatása. Ez az illesztőprogram a fájlrendszer szemantikájának leképezésével végzett összetett feladatot (a Hadoop FileSystem felület előírásainak szerint) az Azure Blob Storage által elérhetővé tett objektumtároló-stílusú felülethez. Ez az illesztőprogram továbbra is támogatja ezt a modellt, amely nagy teljesítményű hozzáférést biztosít a blobokban tárolt adatokhoz, de jelentős mennyiségű kódot tartalmaz, amely ezt a leképezést hajtja végre, ami megnehezíti a karbantartást. Ezenkívül egyes műveletek, például [a FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) és a [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) könyvtárakra alkalmazva az illesztőprogramnak nagyszámú műveletet kell végrehajtania (a könyvtárak támogatásának hiánya miatt), ami gyakran a teljesítmény lebontásához vezet. Az ABFS vezetőt úgy tervezték, hogy leküzdje a WASB eredendő hiányosságait.

## <a name="the-azure-blob-file-system-driver"></a>Az Azure Blob fájlrendszer-illesztőprogramja

Az [Azure Data Lake Storage REST felülete](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) támogatja a fájlrendszer szemantikáját az Azure Blob Storage-on keresztül. Tekintettel arra, hogy a Hadoop fájlrendszer is célja, hogy támogassa ugyanazt a szemantikát nincs szükség egy összetett leképezés az illesztőprogramban. Így az Azure Blob File System illesztőprogram (vagy ABFS) egy egyszerű ügyfél alásibben a REST API-t.

Vannak azonban olyan funkciók, amelyeket az illesztőprogramnak továbbra is el kell végeznie:

### <a name="uri-scheme-to-reference-data"></a>URI-séma hivatkozási adatokhoz

A Hadoop-on belüli egyéb FileSystem implementációkkal összhangban az ABFS-illesztőprogram saját URI-sémát határoz meg, így az erőforrások (könyvtárak és fájlok) egyértelműen kezelhetők. Az URI-séma dokumentálva van [az Azure Data Lake Storage Gen2 URI használata című](./data-lake-storage-introduction-abfs-uri.md)dokumentumban. Az URI szerkezete:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

A fenti URI-formátum használatával szabványos Hadoop-eszközök és keretrendszerek használhatók az alábbi erőforrásokra való hivatkozáshoz:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Belsőleg az ABFS-illesztőprogram az URI-ban megadott erőforrás(oka)t fájlokra és könyvtárakra fordítja le, és ezekkel a hivatkozásokkal hívásokat kezdeményez az Azure Data Lake Storage REST API-ba.

### <a name="authentication"></a>Hitelesítés

Az ABFS-illesztőprogram két hitelesítési formát támogat, így a Hadoop-alkalmazás biztonságosan hozzáférhet a Data Lake Storage Gen2 képes fiókban lévő erőforrásokhoz. A rendelkezésre álló hitelesítési sémák részletes adatait az [Azure Storage biztonsági útmutatója tartalmazza.](security-recommendations.md) Ezek a következők:

- **Megosztott kulcs:** Ez lehetővé teszi a felhasználók számára a fiók összes erőforrásához való hozzáférést. A kulcs titkosítva van, és a Hadoop konfigurációban van tárolva.

- **Azure Active Directory OAuth-tulajdonosi token:** Az Azure AD-tulajdonosi jogkivonatok beszerzése és frissítése az illesztőprogram segítségével vagy a végfelhasználó identitását, vagy egy konfigurált egyszerű szolgáltatás. Ezzel a hitelesítési modell, minden hozzáférés engedélyezett egy hívás alapon a mellékelt jogkivonathoz társított identitás használatával, és kiértékelni a hozzárendelt POSIX hozzáférés-vezérlési lista (ACL).

   > [!NOTE]
   > Az Azure Data Lake Storage Gen2 csak az Azure AD 1.0-s végpontjait támogatja.

### <a name="configuration"></a>Konfiguráció

Az ABFS-illesztőprogram összes konfigurációja <code>core-site.xml</code> a konfigurációs fájlban tárolódik. Az [Ambari-val](https://ambari.apache.org/)rendelkező Hadoop-disztribúciókon a konfiguráció a webportál vagy az Ambari REST API használatával is kezelhető.

Az összes támogatott konfigurációs bejegyzés részleteit a [Hivatalos Hadoop dokumentáció](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)tartalmazza .

### <a name="hadoop-documentation"></a>Hadoop dokumentáció

Az ABFS illesztőprogram teljes mértékben dokumentálva van a [Hivatalos Hadoop dokumentációban](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>További lépések

- [Azure Databricks-fürt létrehozása](./data-lake-storage-quickstart-create-databricks-account.md)
- [Az Azure Data Lake Storage Gen2 URI használata](./data-lake-storage-introduction-abfs-uri.md)
