---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 December |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 0fa7004b9b0dd1ecf087566e7ce8a7d6ebf9be93
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842107"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Mi az új Azure SQL Data warehouse? 2018. december
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek 2018. December.

## <a name="virtual-network-service-endpoints-generally-available"></a>Virtuális hálózati Szolgáltatásvégpontok általánosan elérhető
Ebben a kiadásban az Azure SQL Data Warehouse az összes Azure-régióban virtuális hálózat (VNet) Szolgáltatásvégpontok általános elérhetőségét tartalmazza. Virtuális hálózati Szolgáltatásvégpontok engedélyezése, hogy elkülönítse a kapcsolat a logikai kiszolgáló, egy adott alhálózaton vagy alhálózatokat a virtuális hálózaton belül. A forgalmat a virtuális hálózatról az Azure SQL Data Warehouse mindig az Azure gerinchálózatán belül marad. A közvetlen útvonalon keresztül bármilyen egyedi útvonalakat, amelyek internetes forgalmat a virtuális készülékek vagy a helyszíni előnyben részesített lesz. Nincsenek további számlázási díjszabásának Szolgáltatásvégpontok a virtuális hálózati hozzáférés. A jelenlegi díjszabási modellje [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) irányadó.

Ebben a kiadásban is lehetővé tettük a PolyBase kapcsolatának [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) keresztül [Azure Blob fájlrendszerének](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) illesztőprogram (ABFS). Az Azure Data Lake Storage Gen2 számos lehetőséget kínál az Azure Storage analytics-adatok teljes életciklusa szükséges összes minőség. A két meglévő Azure storage-szolgáltatásokhoz az Azure Blob Storage és az Azure Data Lake Storage Gen1 funkcióit egyetlen. A szolgáltatások [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), például a fájlrendszer szemantikáját, fájlszintű biztonsági és méretezési csoport alacsony költségű, többrétegű tárolást, és a magas rendelkezésre állás/vész-helyreállítási lehetőségei vannak kombinálva [ Az Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

A Polybase használatával is importálhat adatokat az Azure SQL Data Warehouse védett virtuális hálózathoz az Azure Storage-ból. Ehhez hasonlóan adatok exportálása az Azure SQL Data Warehouse-ból az Azure Storage-virtuális hálózathoz biztonságos is támogatott a Polybase segítségével. 

A virtuális hálózati Szolgáltatásvégpontok az Azure SQL Data Warehouse további információkért tekintse meg a [blogbejegyzés](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) vagy a [dokumentáció](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Automatikus teljesítményfigyelés (előzetes verzió)
[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) már elérhető az Azure SQL Data Warehouse előzetes verzióban érhető el. Query Store célja a lekérdezési teljesítmény lekérdezéseket, lekérdezési tervek, futásidejű statisztikája és lekérdezési előzmények a tevékenység és az adatraktár teljesítményét monitorozhatja nyomon követése révén hibaelhárítási segítséget. Query Store egy olyan belső tárolók és a dinamikus felügyeleti nézetekkel (DMV-kkel), amelyekkel:

- Azonosíthatja és felső erőforrásigényes lekérdezések hangolása
- Azonosíthatja és javíthatja az ad-hoc számítási feladatokhoz
- Lekérdezési teljesítmény és a hatása a terv kiértékelése a változások a statisztikákat, indexek vagy méretet (DWU-beállítás)
- Tekintse meg a teljes lekérdezést végrehajtott összes lekérdezés szövege

A Query Store három tényleges tárolók tartalmazza:  
- Egy csomag üzlet a végrehajtási terv adatainak megőrzése 
- A futásidejű statisztikái áruház a végrehajtás statisztikai adatok megőrzése
- Egy várakozási statisztikái tároló megőrzése várakozási statisztikái információt. 

Az SQL Data Warehouse automatikusan kezeli a ezekkel az áruházakkal, és adja meg az elmúlt hét napban díjmentesen storied lekérdezések korlátlan számú. Query Store engedélyezése rendkívül egyszerű, mintha az ALTER DATABASE T-SQL-utasítást:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
A Query Store az Azure SQL Data Warehouse további információkért lásd: a cikk [teljesítmény figyelése a Query Store használatával](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017), és a Query Store DMV-k használatával, mint például [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Íme a [blogbejegyzés](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) bejelentése a kiadásban.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Az Azure SQL Data Warehouse Gen2 alacsonyabb számítási szintek
Az Azure SQL Data Warehouse Gen2 mostantól támogatja az alacsonyabb számítási szinten. Ügyfelek tapasztalhatnak az Azure SQL Data Warehouse kiváló teljesítményt, rugalmasságot és biztonsági funkciókat 100 cDWU kezdve ([Adattárházegységek](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) és 30 000 cDWU percek alatt. 2018 decemberének közepétől kezdve ügyfeleink kihasználhatják a Gen2 teljesítményt és rugalmasságot biztosít az alsó számítási rétegben [régiók](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), a többi 2019 során rendelkezésre álló régiók.

A belépési pont a következő generációs adatraktározási elvetésével Microsoft kitárja a kapukat a érték adatvezérelt ügyfelek számára anélkül, hogy melyik próbaverziós környezet a legjobb őket a találgatás egy biztonságos, nagy teljesítményű data warehouse minden előnyét ki kell számítani. Ügyfelek előfordulhat, hogy kezdhet akár 100 cDWU is le a jelenlegi 500 cDWU belépési pont. Az SQL Data Warehouse Gen2 szüneteltetése és folytatása műveletek és goes túl csak a rugalmasságot, a számítási továbbra is. Gen2 is támogatja az oszloptár korlátlan tárolási kapacitás 2,5-szer több memóriát lekérdezéseként, valamint akár 128 lekérdezést és [adaptív gyorsítótárazást](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) funkciókat. Ezeket a funkciókat, átlagosan öt nagyobb teljesítményt képest a Gen1 ugyanazon Adattárházegység ugyanazon az áron használata. Georedundáns biztonsági mentés olyan Gen2 az beépített garantált adatvédelmi szabvány. Az Azure SQL Data Warehouse Gen2 készen áll a méretezhető, amikor áll.

## <a name="columnstore-background-merge"></a>Oszlopcentrikus háttér egyesítése
Alapértelmezés szerint az Azure SQL Data Warehouse (Azure SQL DW) adatokat tárol Oszlopalapú formátum nevű micro partícióval rendelkező [naplóbájtot](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Egyes esetekben memória miatt indexnél korlátozza a build vagy az adatok betöltési idő, előfordulhat, hogy tömöríti a naplóbájtot kevesebb mint 1 millió sor optimális mérete. Törlések miatt is válnak Naplóbájtot töredezett. Kis- és töredezett naplóbájtot a nagyobb memóriát, valamint a nem elég hatékony lekérdezés-végrehajtás eredményez. Ezzel a kiadással az Azure SQL DW a oszlopcentrikus háttér-karbantartási feladat egyesíti a kis tömörített naplóbájtot jobban memória, és a lekérdezés végrehajtása gyorsabb, nagyobb naplóbájtot létrehozásához.

## <a name="next-steps"></a>További lépések
Most, hogy jobban megismerte az SQL Data warehouse, megtudhatja, hogyan lehet gyorsan [hozzon létre egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha most ismerkedik az Azure-ba, hasznosnak találhatja a [Azure szószedet] [ Azure glossary] hasznos megtudhatja az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
