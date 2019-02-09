---
title: Az Azure Data Lake Storage Gen2 bemutatása
description: Az Azure Data Lake Storage Gen2 nyújt áttekintést
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 871b6314bff7c984bc6cc779c43800883b8ff767
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963811"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Bevezetés az Azure Data Lake Storage Gen2-re

Az Azure Data Lake Storage Gen2 dedikált alapuló, big data-elemzési képességeket [Azure Blob storage](storage-blobs-introduction.md). Data Lake Storage Gen2 beépül a két meglévő storage-szolgáltatások, az Azure Blob storage és az Azure Data Lake Storage Gen1 képességeit eredménye. A szolgáltatások [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), mint például a fájlrendszer szemantikáját, a könyvtárban, és a fájl biztonság és a méretezési csoport alacsony költségű, a többrétegű tárolást, magas rendelkezésre állás/vész-helyreállítási lehetőségei a kombinálva[Azure Blob storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Vállalati big data-elemzés tervezve

Data Lake Storage Gen2 lehetővé teszi az Azure Storage az alapítvány enterprise data Lake tárolók létrehozásához az Azure-ban. Célja a kezdetektől szolgáltatás közben az átviteli sebesség Gigabit több száz fenntartási információkat több petabájtnyi, a Data Lake Storage Gen2 lehetővé teszi nagy mennyiségű adatot egyszerűen felügyelheti.

Data Lake Storage Gen2 alapvető részét is egy [hierarchikus névtér](data-lake-storage-namespace.md) a Blob storage. A hierarchikus névtér objektumok vagy fájlokat rendszerezi be egy hatékony adatelérés könyvtár-hierarchia. Egy közös tároló elnevezési konvenciót objektum neve perjeleket hierarchikus könyvtárstruktúrát utánzására használja. Ez a struktúra válik a Data Lake Storage Gen2 valós. Műveletek, például a átnevezése vagy törlése egy könyvtár egyetlen elemi metaadat-művelet a címtár helyett enumerálása és annak a könyvtárnak a nevét előtagot használó összes objektum feldolgozása a válnak.

Múltbeli időpont felhőalapú elemzési kellett veszélyeztetheti a teljesítmény, a felügyelet és biztonság területéhez. Data Lake Storage Gen2-címek ezeket a szempontokat mindegyike a következő módon:

-   **Teljesítmény** van optimalizálva, mert nem kell másolnia, vagy az adatok átalakítása elemzés előfeltételeként. A hierarchikus névtér nagy mértékben javítja a könyvtár-felügyeleti műveleteket, ami javítja az általános feladat teljesítményt.

-   **Felügyeleti** azért egyszerűbben rendszerezheti és a fájlkezelő könyvtárak és alkönyvtárak keresztül.

-   **Biztonsági** végrehajtható, mert meghatározhatja a POSIX-engedélyeket a könyvtárak vagy csak egyes fájlokat.

-   **Költséghatékonysága** lehetséges legyen, a Data Lake Storage Gen2 épül az alacsony költségű [Azure Blob storage](storage-blobs-introduction.md). A további szolgáltatások tovább csökkentheti a teljes tulajdonosi költség, a big data-elemzés futtatása az Azure-ban.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 főbb funkciói

-   **Hadoop-kompatibilis hozzáférés**: Data Lake Storage Gen2 lehetővé teszi, hogy a kezelése és az adatok eléréséhez, ugyanúgy, mint az egy [Hadoop elosztott fájlrendszer (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS illesztőprogram](data-lake-storage-abfs-driver.md) elérhető összes Apache Hadoop-környezetekben, beleértve a [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), és [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) a Data Lake Storage Gen2 tárolt adatok elérését.

-   **Felülbírálja a POSIX-engedélyek**: A Data Lake Gen2 biztonsági modell támogatja az ACL-JEI és POSIX engedélyekkel együtt néhány további pontossággal megadott Data Lake Storage Gen2-re. Beállítások keretrendszereket, mint például a Hive és a Spark vagy a Tártallózó keresztül konfigurálhatók.

-   **Költséghatékony**: Data Lake Storage Gen2 költséghatékony tárolási kapacitás és a tranzakciók érhetők el. A teljes körű életciklus keresztül adatok transitions lehetőségnél szerint díjszabása nem módosul módosítja a megtartja költségeket a beépített funkciók használatával minimális például [Azure Blob storage életciklus](storage-lifecycle-management-concepts.md).

-   **Optimalizált illesztőprogram**: A abfs illesztőprogram [kifejezetten optimalizált](data-lake-storage-abfs-driver.md) big data-elemzőeszközöket. A megfelelő REST API-k az elosztott fájlrendszerbeli végpont végzetesnek dfs.core.windows.net.

### <a name="scalability"></a>Méretezhetőség

Az Azure Storage szolgáltatás elvárt méretezhető, Data Lake Storage Gen2- vagy Blob storage felületéről férhet hozzá. Képes tárolni és *több exabájt adat*. Szükséges tárhelyet a bemeneti/kimeneti műveletek száma másodpercenként (IOPS) magas szintű (GB/s) mért átviteli sebesség érhető el. Csak adatmegőrzés túli feldolgozási kérésenkénti közel állandó késések, amelyek mérése történik, a szolgáltatás, a fiók és a fáj szinteken lévő kerülnek végrehajtásra.

### <a name="cost-effectiveness"></a>Költséghatékonyság

Létrehozásához, Data Lake Storage Gen2 épülő Azure Blob storage számos előnyének egyike az alacsony költségű tárolási kapacitás és a tranzakciók. Ellentétben más felhőalapú tárolási szolgáltatásokról Data Lake Storage Gen2-ban tárolt adatokkal, nem szükséges áthelyezését vagy elemzése előtt alakította át. Díjszabással kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage).

Továbbá szolgáltatások, például a a [hierarchikus névtér](data-lake-storage-namespace.md) jelentősen növelhető az analytics-feladatok általános teljesítményét. Ez a teljesítmény fokozása azt jelenti, hogy megkövetelése kevesebb számítási teljesítmény az azonos mennyiségű adatot fel, ami a teljes körű analytics-feladat egy alacsonyabb teljes birtoklási költség.

### <a name="one-service-multiple-concepts"></a>Egy szolgáltatás, több fogalmak

Data Lake Storage Gen2 a big data-analitika, Azure Blob storage-ra épülő egy további lehetőség. Noha számos előnyét kihasználva meglévő platformösszetevőkre a létrehozhatják és működtethetik Analytics data Lake-tárolók, Blobok, az azonos, megosztott műveleteket leíró több fogalmak vezethet.

Az alábbiakban az egyenértékű entitások leírtak szerint különböző fogalmakat. Hacsak nincs másképpen megadva, ezeket az entitásokat is közvetlenül értelmű:

| Fogalom                                | Felső szintű szervezeti | Alacsonyabb szintű szervezeti                                            | Adattároló |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blobok – általános célú tárolás | Tároló              | Virtuális könyvtár (SDK-t csak – ad atomi adatkezelési) | Blob           |
| ADLS Gen2 – Analytics Storage          | Fájlrendszer             | Címtár                                                           | Fájl           |

## <a name="supported-open-source-platforms"></a>Támogatja a nyílt forráskódú platformok

Számos nyílt forráskódú platformra támogatja a Data Lake Storage Gen2. Ezek a rendszerek az alábbi táblázatban jelennek meg.

> [!NOTE]
> Csak ebben a táblázatban látható a verziók támogatottak.

| Platform |  Támogatott verziót | További információ |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6. + | [Mik azok az Apache Hadoop-összetevők és verziók a HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 2.7 + | [Az Apache Hadoop-kiadások archívum](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Cloudera Enterprise 6.x kibocsátási megjegyzései](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 4.2 + | [Databricks futtatókörnyezet-verzió](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 2.6 | [Hortonworks-dokumentáció](https://docs.hortonworks.com/) |

## <a name="next-steps"></a>További lépések

Az alábbi cikkek ismertetik azokat a Data Lake Storage Gen2 és részletes főbb fogalmakat tárolása, elérése, kezelése és a data-betekintések:

-   [Hierarchikus névtér](data-lake-storage-namespace.md)
-   [Tárfiók létrehozása](data-lake-storage-quickstart-create-account.md)
-   [Data Lake Storage Gen2 fiók használata az Azure Databricksben](data-lake-storage-quickstart-create-databricks-account.md)
