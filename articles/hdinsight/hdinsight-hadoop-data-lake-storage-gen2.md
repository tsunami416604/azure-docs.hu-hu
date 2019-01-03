---
title: Az Azure Data Lake Storage (ADLS) Gen2 használata az Azure HDInsight az Apache Hadoop-keretrendszerrel
description: Az Azure Data Lake Storage Gen2 és működését az Azure HDInsight áttekintése.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: b7cde3a8990a51e95a8ce9ad85bca524d5669e0c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721121"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Az Azure Data Lake Storage Gen2 használata az Azure HDInsight az Apache Hadoop-keretrendszerrel

Az Azure Data Lake Storage (ADLS) Gen2 idő szükséges alapvető Hadoop kompatibilis a fájlrendszer, Azure Active Directory, az Azure Data Lake Storage Gen1 funkciókat és a POSIX-alapú hozzáférés-vezérlési listákat, és integrálja őket az Azure Blob Storage-bA. Ez a kombináció lehetővé teszi kihasználhatja az Azure Data Lake Storage Gen1 teljesítményét is használata a Blob Storage-rétegezést és az adatok életciklus-kezelése során.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 fő funkciói

- Hadoop-kompatibilis hozzáférés: Az Azure Data Lake Storage Gen2 lehetővé teszi, hogy kezelését, és ugyanúgy, mint az a Hadoop elosztott fájlrendszer (HDFS) adatok eléréséhez. A ABFS illesztőprogram összes Apache Hadoop-környezetekben, beleértve az Azure HDInsight és az Azure Databricks a Data Lake Storage Gen2 adataihoz hozzáférést érhető el.

- Felülbírálja a POSIX-engedélyek: A Data Lake Gen2 biztonsági modell támogatja az ACL-JEI és POSIX engedélyekkel együtt néhány további pontossággal megadott Data Lake Storage Gen2-re. Beállítások felügyeleti eszközöket és keretrendszereket, mint például az Apache Hive és az Apache Spark keresztül konfigurálhatók.

- Költséghatékony: Data Lake Storage Gen2 költséghatékony tárolási kapacitás és a tranzakciók érhetők el. Szolgáltatások, például az Azure Blob storage életciklus alacsonyabb költségek segítségével módosításával díjszabása nem módosul, az adatok életciklusa továbbítanak.

- A Blob storage-eszközökkel, keretrendszerek és alkalmazások működését: Data Lake Storage Gen2 továbbra is az eszközöket, keretrendszerek és alkalmazások a Blob Storage jelenleg létező széles választékának működik.

- Optimalizált illesztőprogram: A ABFS illesztőprogram kifejezetten a big data-elemzés optimalizáltuk. A megfelelő REST API-k az elosztott fájlrendszerbeli végpont végzetesnek dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage általános 2 kapcsolatos újdonságok

### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások számára biztonságos hozzáférést

Az Azure HDInsight fürt védelméhez az Azure Data Lake Storage Gen2 fájlok felügyelt identitásokat használ. Felügyelt identitások érhetők el az Azure Active Directory által biztosított Azure-szolgáltatások automatikusan kezelt hitelesítő adatok vannak beállítva. Ezekkel a hitelesítő adatokkal történő hitelesítéshez minden olyan szolgáltatás, amely támogatja az AD-hitelesítés használható. Felügyelt identitások használatával nincs szükség, hogy kódírásra vagy konfigurálásra fájlban tárolhatja a hitelesítő adatokat.

További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Az Azure Blob fájlrendszer (ABFS) illesztőprogram

Az Apache Hadoop-alkalmazások natív módon várhatóan adatokat olvasnak és írnak a helyi lemezes tárterületet. Egy Hadoop-fájlrendszer illesztőprogram ABFS például lehetővé teszi a Hadoop-alkalmazások által az alkalmazás rendszeres Hadoop fájlrendszerműveletek működni emulálni a felhőalapú tárolással működjön. Az illesztőprogram majd alakítja át ezeket a műveleteket, amely a tényleges felhőalapú tárolási platform együttműködik a műveleteket.

Korábban a Hadoop-fájlrendszer illesztőprogram lenne minden fájlrendszerműveletek átalakítása az Azure Storage REST API-hívásokat az ügyféloldalon, és ezután meghívja a REST API-t. Az ügyféloldali átalakítás, azonban több REST API eredményezett egy egyetlen fájlrendszer-műveletet, például egy fájl átnevezése szükségessé teszi. ABFS át lett helyezve a Hadoop-fájlrendszer logika némelyike az ügyfél oldaláról a kiszolgálóoldali és ADLS Gen2 API-val most Blob API-val párhuzamosan fut. Az áttelepítés javítja a teljesítményt, mert most Hadoop gyakori fájlrendszerműveletre hajtható végre egy REST API-hívással.

További információkért lásd: [az Azure Blob fájlrendszer illesztőprogram (ABFS): Egy dedikált Azure Storage-illesztő az Apache Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>ADLS általános 2 URI-séma

ADLS Gen2 elérnek egy fájlt az Azure BLOB storage a HDInsight egy új URI-séma használ:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL titkosított hozzáférést biztosít (`abfss://` előtag) és a nem titkosított hozzáférést (`abfs://` előtag). Azt javasoljuk, `abfss` amikor csak lehetséges, akkor is, ha ugyanabban a régióban, az Azure-ban lévő adatokat éri el.

`<FILE_SYSTEM_NAME>` az elérési útját a fájlrendszerben, Data Lake Storage Gen2 azonosítja.

`<ACCOUNT_NAME>` az Azure Storage-fiók neve azonosítja. Szükség van a teljes tartománynévre (FQDN-re).

A `<PATH>` a fájl vagy könyvtár HDFS elérési út.

Ha az értékek `<FILE_SYSTEM_NAME>` és `<ACCOUNT_NAME>` nincs megadva, az alapértelmezett fájlrendszer szolgál. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. Ha például a `hadoop-mapreduce-examples.jar` fájlt, amely együttműködik a HDInsight-fürtök a következő útvonalak egyikét használva lehet hivatkozni:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> A Fájlnév `hadoop-examples.jar` HDInsight 2.1-es és 1.6-os verziójának fürtökben. Ha HDInsight-on kívül fájljain végzett munka, legtöbb segédprogram nem ismeri fel a ABFS formázhatja, és ehelyett például az egy alapvető elérési út formátumot várhatóan `example/jars/hadoop-mapreduce-examples.jar`.

További információkért lásd: [használata az Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>További lépések
- [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/data-lake-storage/introduction.md)
- [Az Azure Data Lake Storage Gen2 előzetes verzió használata Azure HDInsight-fürtök](../storage/data-lake-storage/use-hdi-cluster.md)