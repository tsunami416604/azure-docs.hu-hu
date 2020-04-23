---
title: Az Azure Data Lake Storage Gen2 áttekintése a HDInsightban
description: A Data Lake Storage Gen2 áttekintése a HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 677d8348cc230e4f64915ac5fc1e86b9b50ab5c3
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873341"
---
# <a name="azure-data-lake-storage-gen2-overview-in-hdinsight"></a>Az Azure Data Lake Storage Gen2 áttekintése a HDInsightban

Az Azure Data Lake Storage Gen2 az Azure Data Lake Storage Gen1 alapvető funkcióit veszi igénybe, és integrálja azokat az Azure Blob storage-ba. Ezek a funkciók közé tartozik a fájlrendszer, amely kompatibilis a Hadoop, az Azure Active Directory (Azure AD) és a POSIX-alapú hozzáférés-vezérlési listák (ACLs). Ez a kombináció lehetővé teszi, hogy kihasználja az Azure Data Lake Storage Gen1 teljesítményét. A Blob storage rétegezési és adatéletciklus-kezelését is használja.

Az Azure Data Lake Storage Gen2 szolgáltatásról az [Azure Data Lake Storage Gen2 – Bevezetés](../storage/blobs/data-lake-storage-introduction.md)című témakörben talál további információt.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 alapvető funkciói

* **A Hadoop-al kompatibilis hozzáférés:** Az Azure Data Lake Storage Gen2-ben ugyanúgy kezelheti és érheti el az adatokat, mint a Hadoop elosztott fájlrendszer (HDFS) használatával. Az Azure Blob File System (ABFS) illesztőprogram jattson az összes Apache Hadoop környezetben, beleértve az Azure HDInsight és az Azure Databricks. Az ABFS használatával a Data Lake Storage Gen2-ben tárolt adatok eléréséhez.

* **PoSIX engedélyek egy része:** A Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint néhány további, a Data Lake Storage Gen2-re jellemző extra részletességet. A beállítások konfigurálhatók felügyeleti eszközökön vagy keretrendszereken, például az Apache Hive-n és az Apache Sparkon keresztül.

* **Költséghatékonyság:** A Data Lake Storage Gen2 alacsony költségű tárolókapacitást és tranzakciókat kínál. Az Azure Blob tárolási életciklusa a számlázási díjak módosításával csökkenti a költségeket, ahogy az adatok végighaladnak az életciklusuksorán.

* **Kompatibilitás a Blob-tárolóeszközökkel, keretrendszerekkel és alkalmazásokkal:** A Data Lake Storage Gen2 továbbra is számos eszközzel, keretrendszerrel és alkalmazással működik a Blob storage számára.

* **Optimalizált illesztőprogram:** Az ABFS-illesztőprogram kifejezetten a big data-elemzéshez van optimalizálva. A megfelelő REST API-k az elosztott fájlrendszer (DFS) végponton keresztül kerülnek felszínre, dfs.core.windows.net.

## <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage Gen 2 újdonságai

### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások a biztonságos fájlhozzáféréshez

Az Azure HDInsight felügyelt identitások használatával biztosítja a fürt hozzáférést az Azure Data Lake Storage Gen2 fájljaihoz. A felügyelt identitások az Azure Active Directory egyik szolgáltatása, amely automatikusan felügyelt hitelesítő adatokat biztosít az Azure-szolgáltatások számára. Ezek a hitelesítő adatok az Active Directory-hitelesítést támogató szolgáltatások hitelesítésére használhatók. Felügyelt identitások használata nem igényel hitelesítő adatokat kód- vagy konfigurációs fájlokban tárolni.

További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-blob-file-system-driver"></a>Azure Blob fájlrendszer-illesztőprogram

Az Apache Hadoop alkalmazások natív módon elvárják az adatok olvasását és írását a helyi lemeztárolóból. A Hadoop fájlrendszer-illesztőprogram, például az ABFS lehetővé teszi, hogy a Hadoop-alkalmazások működjenek a felhőalapú tárolással. A hadoop fájlrendszer rendszeres műveleteinek emulálásával működik. Az illesztőprogram az alkalmazástól kapott parancsokat olyan műveletekké alakítja, amelyeket a tényleges felhőtárolási platform megért.

Korábban a Hadoop fájlrendszer-illesztőprogram az összes fájlrendszer-műveletet az ügyféloldalon az Azure Storage REST API-hívásokra konvertálta. Majd meghívta a REST API-t. Ez az ügyféloldali átalakítás azonban több REST API-t eredményezett egyetlen fájlrendszer-művelethez, például egy fájl átnevezéséhez. Az ABFS a Hadoop fájlrendszer logikáját az ügyféloldalról a kiszolgálóoldalra helyezte át. Az Azure Data Lake Storage Gen2 API most párhuzamosan fut a Blob API-val. Ez az áttelepítés javítja a teljesítményt, mert mostantól a Hadoop fájlrendszer gyakori műveletei egyetlen REST API-hívással hajthatók végre.

További információ: [The Azure Blob Filesystem driver (ABFS): A dedikált Azure Storage-illesztőprogram hadoop.](../storage/blobs/data-lake-storage-abfs-driver.md)

### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-séma az Azure Data Lake Storage Gen 2-hez

Az Azure Data Lake Storage Gen2 új URI-sémát használ az Azure Storage-ban lévő fájlok HDInsightból való eléréséhez:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL-titkosítású hozzáférést biztosít.

`<FILE_SYSTEM_NAME>`azonosítja a Fájlrendszer Data Lake Storage Gen2 elérési útját.

`<ACCOUNT_NAME>`azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

`<PATH>`a HDFS fájl- vagy könyvtárelérési útja.

Ha a `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` megadott és a nincs érték, akkor a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszerben lévő fájlokhoz használjon relatív elérési utat vagy abszolút elérési utat. A HDInsight-fürtökhöz tartozó `hadoop-mapreduce-examples.jar` fájlra például az alábbi elérési utak egyikével lehet hivatkozni:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]
> A fájlnév `hadoop-examples.jar` a HDInsight 2.1-es és 1.6-os verzióiban található. Ha a HDInsight-on kívüli fájlokkal dolgozik, azt fogja találni, hogy a legtöbb segédprogram nem ismeri fel `example/jars/hadoop-mapreduce-examples.jar`az ABFS formátumot, hanem alapvető elérési útformátumot vár, például .

További információ: [Az Azure Data Lake Storage Gen2 URI használata.](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)