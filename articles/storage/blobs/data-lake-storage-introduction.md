---
title: Az Azure Data Lake Storage Gen2 bemutatása
description: Áttekintést nyújt az Azure Data Lake Storage Gen2-ről
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942917"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba

Az Azure Data Lake Storage Gen2 az [Azure Blob storage-ra](storage-blobs-introduction.md)épülő big data-elemzési képességek összeskészlete. A Data Lake Storage Gen2 két meglévő tárolási szolgáltatásunk, az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeinek összefolyásának eredménye. Az [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)szolgáltatásai, például a fájlrendszer szemantikája, a könyvtár és a fájlszintű biztonság és méretezés alacsony költségű, többszintű tárolással, magas rendelkezésre állású/vész-helyreállítási képességekkel kombinálhatók az [Azure Blob](storage-blobs-introduction.md)storage-ból.

## <a name="designed-for-enterprise-big-data-analytics"></a>Nagyvállalati big data-elemzéshez tervezve

A Data Lake Storage Gen2 az Azure Storage-ot az Azure-beli nagyvállalati adattavak létrehozásának alapjának teszi lehetővé. A Data Lake Storage Gen2 a kezdetektől fogva több petabájtnyi információt szolgál ki, miközben több száz gigabitátviteli kapacitást tart fenn, így könnyedén kezelheti a hatalmas mennyiségű adatot.

A Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](data-lake-storage-namespace.md) hozzáadása a Blob storage-hoz. A hierarchikus névtér az objektumokat/fájlokat könyvtárak hierarchiájába rendezi a hatékony adathozzáférés érdekében. Egy közös objektumtároló-elnevezési konvenció a névben perjeleket használ a hierarchikus könyvtárstruktúra utánzásához. Ez a struktúra a Data Lake Storage Gen2 segítségével válik valósággá. Az olyan műveletek, mint például a címtár átnevezése vagy törlése, egyetlen atomi metaadat-műveletté válnak a címtárban, ahelyett, hogy enumerálnák és feldolgoznák a címtár névelőtagát használó összes objektumot.

A Data Lake Storage Gen2 a Blob storage-ra épül, és az alábbi módokon növeli a teljesítményt, a felügyeletet és a biztonságot:

-   **A teljesítmény** azért van optimalizálva, mert az elemzés előfeltételeként nem kell adatokat másolni vagy átalakítani. A Blob storage egysíkú névteréhez képest a hierarchikus névtér nagymértékben javítja a címtárkezelési műveletek teljesítményét, ami javítja a feladat általános teljesítményét.

-   **A kezelés** egyszerűbb, mert könyvtárakés alkönyvtárak on keresztül rendszerezheti és kezelheti a fájlokat.

-   **A biztonság** azért érvényesíthető, mert POSIX engedélyeket definiálhat könyvtárakhoz vagy egyéni fájlokhoz.

Emellett a Data Lake Storage Gen2 nagyon költséghatékony, mivel az alacsony költségű [Azure Blob-tárolóra](storage-blobs-introduction.md)épül. A további funkciók tovább csökkentik a big data-elemzések Azure-on való futtatásához a teljes tulajdonlási költséget.

## <a name="key-features-of-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 legfontosabb jellemzői

-   **Hadoop-kompatibilis hozzáférés**: A Data Lake Storage Gen2 lehetővé teszi az adatok kezelését és elérését, ugyanúgy, mint a [Hadoop Distributed File System (HDFS) segítségével.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Az új [ABFS-illesztőprogram](data-lake-storage-abfs-driver.md) az összes Apache Hadoop-környezetben elérhető, beleértve az [Azure HDInsightot,](https://docs.microsoft.com/azure/hdinsight/index)*,* az [Azure Databricks-t](https://docs.microsoft.com/azure/azure-databricks/index)és az [SQL Data Warehouse-t](https://docs.microsoft.com/azure/sql-data-warehouse/) a Data Lake Storage Gen2-ben tárolt adatok eléréséhez.

-   **PoSIX-engedélyek egy szuperhalmaza:** A Data Lake Gen2 biztonsági modellje támogatja az ACL és A POSIX engedélyeket, valamint néhány, a Data Lake Storage Gen2-re jellemző extra részletességet. A beállítások konfigurálhatók a Storage Explorer vagy a keretrendszerek, például a Hive és a Spark.

-   **Költséghatékony**: A Data Lake Storage Gen2 alacsony költségű tárolókapacitást és tranzakciókat kínál. Ahogy az adatok áttérnek a teljes életcikluson, a számlázási díjak a beépített funkciók, például az [Azure Blob tárolási életciklusa](storage-lifecycle-management-concepts.md)révén minimálisra változtatják a költségeket.

-   **Optimalizált illesztőprogram**: Az ABFS illesztőprogram kifejezetten big data-elemzésre [van optimalizálva.](data-lake-storage-abfs-driver.md) A megfelelő REST API-k a `dfs.core.windows.net`végponton keresztül kerülnek felszínre.

### <a name="scalability"></a>Méretezhetőség

Az Azure Storage forintban méretezhető, függetlenül attól, hogy a Data Lake Storage Gen2 vagy a Blob storage-felületeken keresztül érhető el. Képes tárolni és kiszolgálni *sok exabájt adatot*. Ez az összeg a tárolási áll rendelkezésre az átviteli sebesség mért gigabit / másodperc (Gbps) magas bemeneti/kimeneti műveletek másodpercenként (IOPS). A folyamatos megőrzésen túl a feldolgozás a szolgáltatás, a fiók és a fájl szintjén mért, kérésenkénti késések mellett történik.

### <a name="cost-effectiveness"></a>Költséghatékonyság

A Data Lake Storage Gen2 azure Blob-tárhelyen való létrehozása a tárolókapacitás és a tranzakciók alacsony költsége. Más felhőalapú tárolási szolgáltatásokkal ellentétben a Data Lake Storage Gen2-ben tárolt adatokat nem szükséges áthelyezni vagy átalakítani az elemzés végrehajtása előtt. A díjszabásról az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage)című témakörben talál további információt.

Emellett az olyan funkciók, mint például a [hierarchikus névtér](data-lake-storage-namespace.md) jelentősen javítják számos elemzési feladat általános teljesítményét. Ez a teljesítményjavulás azt jelenti, hogy kevesebb számítási teljesítményre van szükség az azonos mennyiségű adat feldolgozásához, ami alacsonyabb teljes tulajdonlási költséget (TCO) eredményez a teljes körű elemzési feladathoz.

### <a name="one-service-multiple-concepts"></a>Egy szolgáltatás, több fogalom

A Data Lake Storage Gen2 egy további képesség a big data-elemzéshez, amely az Azure Blob storage-ra épül. Bár számos előnye van a Blobok meglévő platformösszetevőinek elemzéshez való létrehozásában és üzemeltetésében, számos fogalom hoz létre és működik elemzésre, ez több fogalomhoz vezet, amelyek ugyanazokat a megosztott dolgokat írják le.

A következő egyenértékű entitások, a különböző fogalmak által leírt. Eltérő rendelkezés hiányában ezek az entitások közvetlenül szinonimák:

| Fogalom                                | Legfelső szintű szervezet | Alacsonyabb szintű szervezet                                            | Adattároló |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobok – Általános célú objektumtárolás | Tároló              | Virtuális könyvtár (csak SDK - nem biztosít atomi manipulációt) | Blob           |
| Azure Data Lake Storage Gen2 – Analytics-tár          | Tároló            | Címtár                                                           | Fájl           |

## <a name="supported-blob-storage-features"></a>A Blob-tároló támogatott szolgáltatásai

A Blob storage-szolgáltatások, például [a diagnosztikai naplózás,](../common/storage-analytics-logging.md) [a hozzáférési szintek](storage-blob-storage-tiers.md)és a Blob Storage [életciklus-kezelési házirendjei](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a Blob storage-fiókok hierarchikus névtereket engedélyezhet anélkül, hogy elveszítené a hozzáférést ezekhez a szolgáltatásokhoz. 

A támogatott Blob storage-funkciók listáját az [Azure Data Lake Storage Gen2 szolgáltatásában elérhető Blob Storage-funkciók című témakörben láthatja.](data-lake-storage-supported-blob-storage-features.md)

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatásintegrációk

A Data Lake Storage gen2 számos Azure-szolgáltatást támogat, amelyek segítségével adatokat adhat be, elemzéseket végezhet, és vizuális ábrázolásokat hozhat létre. A támogatott Azure-szolgáltatások listáját az [Azure Data Lake Storage Gen2 szolgáltatást támogató Azure-szolgáltatások című](data-lake-storage-supported-azure-services.md)témakörben található.

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2-t. A teljes listát az [Azure Data Lake Storage Gen2-t támogató nyílt forráskódú platformok ról.](data-lake-storage-supported-open-source-platforms.md)

## <a name="see-also"></a>Lásd még

- [Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban](data-lake-storage-known-issues.md)
- [Többprotokollos hozzáférés az Azure Data Lake Storage szolgáltatásban](data-lake-storage-multi-protocol-access.md)


