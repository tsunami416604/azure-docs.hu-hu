---
title: Azure Data Lake Storage Gen2 Bemutatkozás
description: Olvassa el a Azure Data Lake Storage Gen2 bevezetését. Ismerje meg a legfontosabb funkciókat. Tekintse át a támogatott blob Storage-szolgáltatásokat, az Azure-szolgáltatások integrációját és a platformokat.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d85b0cd2f9fa7eb81f5c39bd5d163188e3cd7106
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835765"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 bemutatása

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](storage-blobs-introduction.md)-ra épülő, Big Data Analytics szolgáltatáshoz dedikált képességek összessége. Data Lake Storage Gen2 a két meglévő tárolási szolgáltatás, az Azure Blob Storage és a Azure Data Lake Storage Gen1 képességeinek átszervezését eredményezi. A [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), például a fájlrendszer szemantikai, a könyvtári és a fájl szintű biztonság és a skálázás funkciói kombinálva vannak az [Azure Blob Storage](storage-blobs-introduction.md)alacsony díjas, többszintű tárolási, magas rendelkezésre állású/vész-helyreállítási képességeivel.

## <a name="designed-for-enterprise-big-data-analytics"></a>Nagyvállalati big data elemzésekhez tervezve

A Data Lake Storage Gen2 lehetővé teszi az Azure Storage számára, hogy a vállalati adattavakat az Azure-ban hozza létre. Több petabájt-szolgáltatás megkezdése és kiszolgálása mellett a Data Lake Storage Gen2 segítségével könnyedén kezelhet nagy mennyiségű adatot.

Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](data-lake-storage-namespace.md) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez. A közös objektum-tároló elnevezési konvenció a név perjelét használja a hierarchikus címtár-struktúra kiépítéséhez. Ez a struktúra Data Lake Storage Gen2. Az olyan műveletek, mint például a címtár átnevezése vagy törlése, a címtárhoz tartozó előtagot nem tartalmazó összes objektum enumerálása és feldolgozása helyett egyetlen atomi metaadat-művelet lesz.

A Data Lake Storage Gen2 a blob Storage-ra épül, és a következő módokon javítja a teljesítményt, a felügyeletet és a biztonságot:

-   A **teljesítmény** optimalizálva van, mert nem szükséges az adatok másolása és átalakítása előfeltételként az elemzéshez. A blob Storage-beli sík névtérhez képest a hierarchikus névtér nagy mértékben javítja a címtár-felügyeleti műveletek teljesítményét, ami javítja a feladatok általános teljesítményét.

-   A **felügyelet** egyszerűbb, mivel a fájlokat címtárakban és Alkönyvtárakban is rendszerezheti és kezelheti.

-   A **Biztonság** kényszeríthető, mert POSIX-engedélyeket adhat meg a címtárakhoz vagy az egyes fájlokhoz.

Emellett a Data Lake Storage Gen2 nagyon költséghatékony, mert az [Azure Blob Storage](storage-blobs-introduction.md)-ra épül. A további funkciók tovább csökkentik az Azure-ban futó big data-elemzések teljes tulajdonlási költségeit.

## <a name="key-features-of-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 főbb funkciói

-   **Hadoop-kompatibilis hozzáférés**: a Data Lake Storage Gen2 lehetővé teszi az adatkezelést és az adathozzáférést ugyanúgy, mint a [HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS-illesztőprogram](data-lake-storage-abfs-driver.md) minden Apache Hadoop környezetben elérhető, beleértve az [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* a [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)és az [Azure szinapszis Analytics szolgáltatást](https://docs.microsoft.com/azure/synapse-analytics) a Data Lake Storage Gen2 tárolt adateléréshez.

-   **A POSIX-engedélyek felülbírálása**: a Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások a Storage Exploreron vagy olyan keretrendszereken keresztül konfigurálhatók, mint a kaptár és a Spark.

-   **Költséghatékony**: Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Mivel az adatváltások teljes életciklusán keresztül történik, a számlázási díjszabás a minimálisan megjelenő beépített funkciókkal (például az [Azure Blob Storage életciklusával](storage-lifecycle-management-concepts.md)) megtartja a költségeket.

-   **Optimalizált illesztőprogram**: a ABFS-illesztőprogram kifejezetten Big Data elemzésekhez van [optimalizálva](data-lake-storage-abfs-driver.md) . A megfelelő REST API-k a végponton keresztül vannak felszínen `dfs.core.windows.net` .

### <a name="scalability"></a>Méretezhetőség

Az Azure Storage a Data Lake Storage Gen2-vagy blob Storage-felületeken keresztüli hozzáférésének megtervezésével méretezhető. *Számos több exabájt*képes tárolni és kiszolgálni. Ez a tárterület a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) magas szintjein mért Gigabit/másodperc (GB/s) értékkel érhető el. A csak az adatmegőrzési időszakon túl a feldolgozás a szolgáltatás, a fiók és a fájl szintjén mérhető, a kérelmekre vonatkozó késések mellett történik.

### <a name="cost-effectiveness"></a>Költséghatékonyság

Az Azure Blob Storage-hoz való Data Lake Storage Gen2ának számos előnye, hogy a tárolókapacitás és a tranzakciók alacsony díja. A többi felhőalapú tárolási szolgáltatástól eltérően a Data Lake Storage Gen2ban tárolt adatok nem szükségesek az elemzés végrehajtása előtt áthelyezni vagy átalakítani. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage).

Emellett a [hierarchikus névtérrel](data-lake-storage-namespace.md) rendelkező funkciók jelentős mértékben javítják a számos elemzési feladat általános teljesítményét. A teljesítmény fokozása azt jelenti, hogy kevesebb számítási teljesítményre van szükség az azonos mennyiségű adat feldolgozásához, így a végpontok közötti elemzési feladathoz tartozó teljes tulajdonlási költség (TCO) alacsonyabb.

### <a name="one-service-multiple-concepts"></a>Egy szolgáltatás, több fogalom

A Data Lake Storage Gen2 az Azure Blob Storage-ra épülő big data Analytics további funkciója. Noha számos előnnyel jár a Blobok meglévő platform-összetevőinek kihasználása az adattavak elemzési célra való létrehozásához és üzemeltetéséhez, több fogalmat is tartalmaz, amelyek ugyanazokat a megosztott dolgokat ismertetik.

A következők az egyenértékű entitások, amelyeket a különböző fogalmak ismertetnek. Ha másként nincs megadva, akkor ezek az entitások közvetlenül szinonimák:

| Fogalom                                | Legfelső szintű szervezet | Alacsonyabb szintű szervezet                                            | Adattároló |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobok – általános célú objektumok tárolója | Tároló              | Virtuális könyvtár (csak SDK – nem biztosít atomi manipulációt) | Blob           |
| Azure Data Lake Storage Gen2 – elemzési tár          | Tároló            | Címtár                                                           | Fájl           |

## <a name="supported-blob-storage-features"></a>Támogatott Blob Storage-funkciók

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatok](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a funkciókhoz hozzáférjenek. 

A támogatott blob Storage-funkciók listáját a [Azure Data Lake Storage Gen2 elérhető blob Storage szolgáltatások](data-lake-storage-supported-blob-storage-features.md)című részben tekintheti meg.

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatások integrációi

Data Lake Storage a Gen2 számos olyan Azure-szolgáltatást támogat, amelyet az adatok betöltésére, elemzések elvégzésére és vizuális ábrázolások létrehozására használhat. A támogatott Azure-szolgáltatások listáját a [Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)című témakörben tekintheti meg.

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2. A teljes listát lásd: a [Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


