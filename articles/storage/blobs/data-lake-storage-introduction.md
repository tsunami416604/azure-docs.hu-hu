---
title: Azure Data Lake Storage Gen2 Bemutatkozás
description: Áttekintést nyújt Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942917"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Bevezetés a Azure Data Lake Storage Gen2ba

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](storage-blobs-introduction.md)-ra épülő, Big Data Analytics szolgáltatáshoz dedikált képességek összessége. Data Lake Storage Gen2 beépül a két meglévő storage-szolgáltatások, az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeit eredménye. A [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), például a fájlrendszer szemantikai, a könyvtári és a fájl szintű biztonság és a skálázás funkciói kombinálva vannak az [Azure Blob Storage](storage-blobs-introduction.md)alacsony díjas, többszintű tárolási, magas rendelkezésre állású/vész-helyreállítási képességeivel.

## <a name="designed-for-enterprise-big-data-analytics"></a>Vállalati big data-elemzés tervezve

Data Lake Storage Gen2 lehetővé teszi az Azure Storage az alapítvány enterprise data Lake tárolók létrehozásához az Azure-ban. Célja a kezdetektől szolgáltatás közben az átviteli sebesség Gigabit több száz fenntartási információkat több petabájtnyi, a Data Lake Storage Gen2 lehetővé teszi nagy mennyiségű adatot egyszerűen felügyelheti.

Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](data-lake-storage-namespace.md) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumok vagy fájlokat rendszerezi be egy hatékony adatelérés könyvtár-hierarchia. Egy közös tároló elnevezési konvenciót objektum neve perjeleket hierarchikus könyvtárstruktúrát utánzására használja. Ez a struktúra válik a Data Lake Storage Gen2 valós. Műveletek, például a átnevezése vagy törlése egy könyvtár egyetlen elemi metaadat-művelet a címtár helyett enumerálása és annak a könyvtárnak a nevét előtagot használó összes objektum feldolgozása a válnak.

A Data Lake Storage Gen2 a blob Storage-ra épül, és a következő módokon javítja a teljesítményt, a felügyeletet és a biztonságot:

-   A **teljesítmény** optimalizálva van, mert nem szükséges az adatok másolása és átalakítása előfeltételként az elemzéshez. A blob Storage-beli sík névtérhez képest a hierarchikus névtér nagy mértékben javítja a címtár-felügyeleti műveletek teljesítményét, ami javítja a feladatok általános teljesítményét.

-   A **felügyelet** egyszerűbb, mivel a fájlokat címtárakban és Alkönyvtárakban is rendszerezheti és kezelheti.

-   A **Biztonság** kényszeríthető, mert POSIX-engedélyeket adhat meg a címtárakhoz vagy az egyes fájlokhoz.

Emellett a Data Lake Storage Gen2 nagyon költséghatékony, mert az [Azure Blob Storage](storage-blobs-introduction.md)-ra épül. A további szolgáltatások tovább csökkentheti a teljes tulajdonosi költség, a big data-elemzés futtatása az Azure-ban.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 főbb funkciói

-   **Hadoop-kompatibilis hozzáférés**: a Data Lake Storage Gen2 lehetővé teszi az adatkezelést és az adathozzáférést ugyanúgy, mint a [HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS-illesztőprogram](data-lake-storage-abfs-driver.md) minden Apache Hadoop környezetben elérhető, beleértve az [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* a [Azure Databrickst](https://docs.microsoft.com/azure/azure-databricks/index)és a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) a Data Lake Storage Gen2 tárolt adateléréshez.

-   **A POSIX-engedélyek felülbírálása**: a Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. Beállítások keretrendszereket, mint például a Hive és a Spark vagy a Tártallózó keresztül konfigurálhatók.

-   **Költséghatékony**: Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Mivel az adatváltások teljes életciklusán keresztül történik, a számlázási díjszabás a minimálisan megjelenő beépített funkciókkal (például az [Azure Blob Storage életciklusával](storage-lifecycle-management-concepts.md)) megtartja a költségeket.

-   **Optimalizált illesztőprogram**: a ABFS-illesztőprogram kifejezetten Big Data elemzésekhez van [optimalizálva](data-lake-storage-abfs-driver.md) . A megfelelő REST API-k a végponton keresztül `dfs.core.windows.net`.

### <a name="scalability"></a>Méretezhetőség

Az Azure Storage szolgáltatás elvárt méretezhető, Data Lake Storage Gen2- vagy Blob storage felületéről férhet hozzá. *Számos több exabájt*képes tárolni és kiszolgálni. Szükséges tárhelyet a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) magas szintű (GB/s) mért átviteli sebesség érhető el. Csak adatmegőrzés túli feldolgozási kérésenkénti közel állandó késések, amelyek mérése történik, a szolgáltatás, a fiók és a fáj szinteken lévő kerülnek végrehajtásra.

### <a name="cost-effectiveness"></a>Költséghatékonyság

Létrehozásához, Data Lake Storage Gen2 épülő Azure Blob storage számos előnyének egyike az alacsony költségű tárolási kapacitás és a tranzakciók. Ellentétben más felhőalapú tárolási szolgáltatásokról Data Lake Storage Gen2-ban tárolt adatokkal, nem szükséges áthelyezését vagy elemzése előtt alakította át. A díjszabással kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage).

Emellett a [hierarchikus névtérrel](data-lake-storage-namespace.md) rendelkező funkciók jelentős mértékben javítják a számos elemzési feladat általános teljesítményét. Ez a teljesítmény fokozása azt jelenti, hogy megkövetelése kevesebb számítási teljesítmény az azonos mennyiségű adatot fel, ami a teljes körű analytics-feladat egy alacsonyabb teljes birtoklási költség.

### <a name="one-service-multiple-concepts"></a>Egy szolgáltatás, több fogalmak

Data Lake Storage Gen2 a big data-analitika, Azure Blob storage-ra épülő egy további lehetőség. Noha számos előnyét kihasználva meglévő platformösszetevőkre a létrehozhatják és működtethetik Analytics data Lake-tárolók, Blobok, az azonos, megosztott műveleteket leíró több fogalmak vezethet.

Az alábbiakban az egyenértékű entitások leírtak szerint különböző fogalmakat. Hacsak nincs másképpen megadva, ezeket az entitásokat is közvetlenül értelmű:

| Fogalom                                | Felső szintű szervezeti | Alacsonyabb szintű szervezeti                                            | Adattároló |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobok – általános célú tárolás | Tároló              | Virtuális könyvtár (SDK-t csak – ad atomi adatkezelési) | Blob           |
| Azure Data Lake Storage Gen2 – elemzési tár          | Tároló            | Címtár                                                           | Fájl           |

## <a name="supported-blob-storage-features"></a>Támogatott blob Storage-funkciók

A blob Storage olyan funkciói, mint a [diagnosztikai naplózás](../common/storage-analytics-logging.md), a [hozzáférési szintek](storage-blob-storage-tiers.md)és a [blob Storage életciklus-kezelési szabályzatok](storage-lifecycle-management-concepts.md) mostantól hierarchikus névtérrel rendelkező fiókokkal működnek. Ezért a blob Storage-fiókokban is engedélyezheti a hierarchikus névtereket anélkül, hogy ezekhez a funkciókhoz hozzáférjenek. 

A támogatott blob Storage-funkciók listáját a [Azure Data Lake Storage Gen2 elérhető blob Storage szolgáltatások](data-lake-storage-supported-blob-storage-features.md)című részben tekintheti meg.

## <a name="supported-azure-service-integrations"></a>Támogatott Azure-szolgáltatások integrációi

Data Lake Storage a Gen2 számos olyan Azure-szolgáltatást támogat, amelyet az adatok betöltésére, elemzések elvégzésére és vizuális ábrázolások létrehozására használhat. A támogatott Azure-szolgáltatások listáját a [Azure Data Lake Storage Gen2t támogató Azure-szolgáltatások](data-lake-storage-supported-azure-services.md)című témakörben tekintheti meg.

## <a name="supported-open-source-platforms"></a>Támogatja a nyílt forráskódú platformok

Számos nyílt forráskódú platformra támogatja a Data Lake Storage Gen2. A teljes listát lásd: a [Azure Data Lake Storage Gen2t támogató nyílt forráskódú platformok](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Lásd még

- [Ismert problémák a Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


