---
title: Azure Data Lake Storage Gen2 Bemutatkozás
description: Áttekintést nyújt Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 99863f68c20a2f95dfc744e13a977bf3ccbbf639
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580429"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Bevezetés a Azure Data Lake Storage Gen2ba

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](storage-blobs-introduction.md)-ra épülő, Big Data Analytics szolgáltatáshoz dedikált képességek összessége. Data Lake Storage Gen2 a két meglévő tárolási szolgáltatás, az Azure Blob Storage és a Azure Data Lake Storage Gen1 képességeinek átszervezését eredményezi. A [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), például a fájlrendszer szemantikai, a könyvtári és a fájl szintű biztonság és a skálázás funkciói kombinálva vannak az [Azure Blob Storage](storage-blobs-introduction.md)alacsony díjas, többszintű tárolási, magas rendelkezésre állású/vész-helyreállítási képességeivel.

## <a name="designed-for-enterprise-big-data-analytics"></a>Nagyvállalati big data elemzésekhez tervezve

A Data Lake Storage Gen2 lehetővé teszi az Azure Storage számára, hogy a vállalati adattavakat az Azure-ban hozza létre. Több petabájt-szolgáltatás megkezdése és kiszolgálása mellett a Data Lake Storage Gen2 segítségével könnyedén kezelhet nagy mennyiségű adatot.

Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](data-lake-storage-namespace.md) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez. A közös objektum-tároló elnevezési konvenció a név perjelét használja a hierarchikus címtár-struktúra kiépítéséhez. Ez a struktúra Data Lake Storage Gen2. Az olyan műveletek, mint például a címtár átnevezése vagy törlése, a címtárhoz tartozó előtagot nem tartalmazó összes objektum enumerálása és feldolgozása helyett egyetlen atomi metaadat-művelet lesz.

A múltban a felhőalapú elemzések a teljesítmény, a felügyelet és a biztonság területén is veszélyeztethetik a biztonságot. Data Lake Storage Gen2 a következő módokon kezeli ezeket a szempontokat:

-   A **teljesítmény** optimalizálva van, mert nem szükséges az adatok másolása és átalakítása előfeltételként az elemzéshez. A hierarchikus névtér nagy mértékben javítja a címtár-felügyeleti műveletek teljesítményét, ami javítja a feladatok általános teljesítményét.

-   A **felügyelet** egyszerűbb, mivel a fájlokat címtárakban és Alkönyvtárakban is rendszerezheti és kezelheti.

-   A **Biztonság** kényszeríthető, mert POSIX-engedélyeket adhat meg a címtárakhoz vagy az egyes fájlokhoz.

-   A **költséghatékonyságot** az [Azure Blob Storage](storage-blobs-introduction.md)szolgáltatásra épülő, Data Lake Storage Gen2-ra épülő eszközön lehet elvégezni. A további funkciók tovább csökkentik az Azure-ban futó big data-elemzések teljes tulajdonlási költségeit.

## <a name="key-features-of-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 főbb funkciói

-   **Hadoop-kompatibilis hozzáférés**: a Data Lake Storage Gen2 lehetővé teszi az adatkezelést és az adathozzáférést ugyanúgy, mint a [HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS-illesztőprogram](data-lake-storage-abfs-driver.md) minden Apache Hadoop környezetben elérhető, beleértve az [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* a [Azure Databrickst](https://docs.microsoft.com/azure/azure-databricks/index)és a [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) a Data Lake Storage Gen2 tárolt adateléréshez.

-   **A POSIX-engedélyek felülbírálása**: a Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások a Storage Exploreron vagy olyan keretrendszereken keresztül konfigurálhatók, mint a kaptár és a Spark.

-   **Költséghatékony**: Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Mivel az adatváltások teljes életciklusán keresztül történik, a számlázási díjszabás a minimálisan megjelenő beépített funkciókkal (például az [Azure Blob Storage életciklusával](storage-lifecycle-management-concepts.md)) megtartja a költségeket.

-   **Optimalizált illesztőprogram**: a ABFS-illesztőprogram kifejezetten Big Data elemzésekhez van [optimalizálva](data-lake-storage-abfs-driver.md) . A megfelelő REST API-k a végponton keresztül `dfs.core.windows.net`.

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

## <a name="supported-open-source-platforms"></a>Támogatott nyílt forráskódú platformok

Számos nyílt forráskódú platform támogatja a Data Lake Storage Gen2. Ezek a platformok az alábbi táblázatban láthatók.

> [!NOTE]
> Csak a táblázatban megjelenő verziók támogatottak.

| Platform |  Támogatott verzió (k) | További információ |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Milyen Apache Hadoop-összetevők és-verziók érhetők el a HDInsight-ben?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 + | [Apache Hadoop kiadások archívuma](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Cloudera Enterprise 6. x kibocsátási megjegyzések](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Databricks Runtime verziók](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [A Felhőbeli adathozzáférés konfigurálása](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Támogatott Azure-szolgáltatások

Data Lake Storage a Gen2 számos olyan Azure-szolgáltatást támogat, amelyet az adatok betöltésére, elemzések elvégzésére és vizuális ábrázolások létrehozására használhat. A támogatott Azure-szolgáltatások listáját lásd: a [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](data-lake-storage-integrate-with-azure-services.md).

## <a name="next-steps"></a>További lépések

Az alábbi cikkek a Data Lake Storage Gen2 főbb fogalmait ismertetik, és részletesen ismertetik, hogyan tárolhatók, érhetők el, kezelhetők és betekintést nyerhetnek az adatokból:

- [Hierarchikus névtér](data-lake-storage-namespace.md)
- [Tárfiók létrehozása](data-lake-storage-quickstart-create-account.md)
- [Több protokollos hozzáférés Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](data-lake-storage-integrate-with-azure-services.md);
