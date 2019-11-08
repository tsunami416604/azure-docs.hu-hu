---
title: Ggeneral célja és üzleti szempontból kritikus fontosságú
description: A cikk a virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási rétegeit tárgyalja.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: 85b610969f9e5e85d014f89b90cfd862731aaf0c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821084"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database szolgáltatási szintek

A Azure SQL Database SQL Server adatbázismotor-architektúrán alapul, amely a felhőalapú környezet számára lett kialakítva, hogy 99,99%-os rendelkezésre állást biztosítson, még akkor is, ha van infrastrukturális hiba. A Azure SQL Databaseban három szolgáltatási szintet használunk, amelyek mindegyike más építészeti modellel rendelkezik. A szolgáltatási szintek a következők:

- [Általános célú](sql-database-service-tier-general-purpose.md), amely költségvetés-alapú számítási feladatokhoz készült.
- A [nagy kapacitású](sql-database-service-tier-hyperscale.md), amely a legtöbb üzleti számítási feladathoz lett tervezve, nagy mértékben méretezhető tárolást, olvasási felskálázást és gyors adatbázis-visszaállítási képességeket biztosít.
- [Üzleti szempontból kritikus fontosságú](sql-database-service-tier-business-critical.md), amely kis késleltetésű számítási feladatokhoz készült, nagy rugalmassággal és gyors feladatátvételsel.

Ebből a cikkből megtudhatja, hogy a különbségek a virtuális mag-alapú vásárlási modell általános célú és üzleti szempontból kritikus szolgáltatási szintjeinek a szolgáltatási szintek, a tárolási és a biztonsági mentési szempontok.

## <a name="service-tier-comparison"></a>Szolgáltatási Rétegek összehasonlítása

A következő táblázat ismerteti a legújabb generációs szolgáltatási szintek közötti fő különbségeket (Gen5). Vegye figyelembe, hogy a szolgáltatási szintek jellemzői a önálló adatbázis és a felügyelt példányban eltérőek lehetnek.

| | Erőforrás típusa | Általános célú |  Rugalmas skálázás | Üzletileg kritikus |
|:---:|:---:|:---:|:---:|:---:|
| **Legjobb a következőhöz:** | |  A költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál. | A legtöbb üzleti számítási feladat. A tárterület méretének automatikus skálázása 100 TB-ig, folyékony vertikális és horizontális számítási skálázás, gyors adatbázis-visszaállítás. | OLTP alkalmazások nagy tranzakciós sebességgel és alacsony IO-késéssel. Maximális rugalmasságot biztosít a hibák és a gyors feladatátvételek esetében, több szinkronban frissített replika használatával.|
|  **Erőforrás-típusban elérhető:** ||Önálló adatbázis/rugalmas készlet/felügyelt példány | Önálló adatbázis | Önálló adatbázis/rugalmas készlet/felügyelt példány |
| **Számítási méret**|Önálló adatbázis/rugalmas készlet | 1 – 80 virtuális mag | 1 – 80 virtuális mag | 1 – 80 virtuális mag |
| | Felügyelt példány | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| | Felügyelt példányok készletei | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | N/A |
| **Tárolás típusa** | Összes | Prémium szintű távoli tárterület (/példány) | A leválasztott tárterület helyi SSD-gyorsítótárral (/példány) | Villámgyors helyi SSD-tároló (példány) |
| **Adatbázis mérete** | Önálló adatbázis/rugalmas készlet | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **Tárterület mérete** | Önálló adatbázis/rugalmas készlet | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB mérete** | Önálló adatbázis/rugalmas készlet | [32 GB/virtuális mag](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB/virtuális mag](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB/virtuális mag](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Felügyelt példány  | [24 GB/virtuális mag](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | Legfeljebb 4 TB – [a tárterület mérete korlátozva](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Írási sebesség naplózása** | Önálló adatbázis | [1,875 MB/s/virtuális mag (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s/s/virtuális mag (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Felügyelt példány | [3 MB/s/s virtuális mag (max. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [4 MB/s/s virtuális mag (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Rendelkezésre állás**|Összes| 99.99% |  [99,95% egyetlen másodlagos replikával, 99,99%-kal több replikával](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [99,995% a zóna redundáns önálló adatbázisával](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Mentések**|Összes|RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap)| RA-GRS, 7 nap, állandó időpontra történő helyreállítás (PITR) | RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap) |
|**Memóriában tárolt OLTP** | | N/A | N/A | Elérhető |
|**Írásvédett replikák**| | 0  | 0 - 4 | 1 (beépített, árba belefoglalva) |
|**Díjszabás/számlázás** | Önálló adatbázis | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/single/) díja. <br/>A IOPS nem számítunk fel díjat. | [az egyes replikák és a felhasznált tárolók virtuális mag](https://azure.microsoft.com/pricing/details/sql-database/single/) számítunk fel díjat. <br/>A IOPS még nincs felszámítva. | a [virtuális mag, a fenntartott tárterület és a biztonsági mentési tár](https://azure.microsoft.com/pricing/details/sql-database/single/) díja. <br/>A IOPS nem számítunk fel díjat. |
|| Felügyelt példány | a [virtuális mag és a fenntartott tároló](https://azure.microsoft.com/pricing/details/sql-database/managed/) díja. <br/>A IOPS nem számítunk fel díjat.<br/>A biztonsági mentési tár még nincs felszámítva. | N/A | a [virtuális mag és a fenntartott tároló](https://azure.microsoft.com/pricing/details/sql-database/managed/) díja. <br/>A IOPS nem számítunk fel díjat.<br/>A biztonsági mentési tár még nincs felszámítva. | 
|**Kedvezményes modellek**| | [Fenntartott példányok](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések| [Fenntartott példányok](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el a fejlesztési és tesztelési előfizetéseken)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [utólagos](https://azure.microsoft.com/offers/ms-azr-0023p/) elszámolású fejlesztési és tesztelési előfizetések|

További információkért tekintse meg az [önálló adatbázis (virtuális mag)](sql-database-vcore-resource-limits-single-databases.md), az [önálló adatbázis-készletek (virtuális mag)](sql-database-dtu-resource-limits-single-databases.md), az [önálló adatbázisok (DTU)](sql-database-dtu-resource-limits-single-databases.md), az [önálló adatbázis-készletek (DTU)](sql-database-dtu-resource-limits-single-databases.md)és a [felügyelt példányok](sql-database-managed-instance-resource-limits.md) közötti különbségeket. lapok.

> [!NOTE]
> További információ a nagy kapacitású szolgáltatási szintjéről a virtuális mag-alapú vásárlási modellben: [nagy kapacitású szolgáltatási réteg](sql-database-service-tier-hyperscale.md). A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Adatkezelési és naplózási tár

Az alábbi tényezők befolyásolják az adatokhoz és a naplófájlokhoz használt tárterület mennyiségét, és a általános célúra és üzletileg kritikusra vonatkoznak. A nagy kapacitású-ben tárolt adatokkal és naplózási tárolóval kapcsolatos részletekért lásd: [nagy kapacitású szolgáltatási szintje](sql-database-service-tier-hyperscale.md).

- A lefoglalt tárterületet az adatfájlok (MDF) és a naplófájlok (LDF) használják.
- Minden egyes adatbázis számítási mérete támogatja a maximális adatbázis-méretet, amely az alapértelmezett maximális méret 32 GB.
- A szükséges Egyadatbázisos méret (az MDF-fájl mérete) beállításakor a rendszer automatikusan hozzáadja a további tárterületet az LDF-fájlok támogatásához.
- A SQL Database felügyelt példányok tárolási méretét a 32 GB-os többszörösében kell megadni.
- Az egyes adatbázisok mérete 10 GB és a támogatott maximális érték között választható ki.
  - A standard vagy általános célú szolgáltatási rétegekben történő tároláshoz a 10 GB-os növekmények méretének növelése vagy csökkentése.
  - A prémium vagy üzleti szempontból kritikus szolgáltatási rétegekben a méret növeléséhez vagy csökkentéséhez növelje vagy csökkentse a 250 GB-os növekményeket.
- Az általános célú szolgáltatási szinten a `tempdb` egy csatlakoztatott SSD-t használ, és ez a tárolási költség a virtuális mag ár részét képezi.
- Az üzleti szempontból kritikus szolgáltatási rétegben `tempdb` megosztja a csatlakoztatott SSD-t az MDF-és az LDF-fájlokkal, és a `tempdb` tárolási költséget a virtuális mag ára tartalmazza.

> [!IMPORTANT]
> Az MDF-és az LDF-fájlokhoz lefoglalt teljes tárterületért kell fizetnie.

Az MDF-és az LDF-fájlok aktuális teljes méretének figyeléséhez használja a [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Az egyes MDF-és LDF-fájlok aktuális méretének figyeléséhez használja a következőt: [sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy az adatbázist fel kell zsugorodnia a fel nem használt területek visszaigényléséhez. További információ: [a tárterület kezelése Azure SQL Databaseban](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Biztonsági másolatok és tárolás

Az adatbázis-biztonsági másolatok tárolását a rendszer lefoglalta a SQL Database időponthoz kötött visszaállításának (PITR) és [hosszú távú adatmegőrzési (ltr)](sql-database-long-term-retention.md) képességeinek támogatásához. Ezt a tárolót külön kell lefoglalni az egyes adatbázisokhoz, és a számlázást két külön adatbázis-díj alapján számoljuk el.

- **PITR**: az egyes adatbázisok biztonsági másolatait a rendszer automatikusan átmásolja az [olvasási hozzáférésű geo-redundáns (ra-GRS) tárolóba](../storage/common/storage-designing-ha-apps-with-ragrs.md) . A tárterület mérete dinamikusan növekszik, ahogy új biztonsági másolatok jönnek létre. A tárterületet hetente teljes biztonsági mentések, napi különbözeti biztonsági másolatok és tranzakciónapló-biztonsági másolatok használják, amelyek 5 percenként másolódnak át. A tárterület-felhasználás az adatbázis változásának és a biztonsági mentések megőrzési idejének a függvénye. Minden adatbázishoz külön megőrzési időtartamot állíthat be 7 és 35 nap között. Az adatbázis méretének 100 százalékkal (1x) megegyező minimális tárterülete külön díj nélkül elérhető. A legtöbb adatbázis esetében ez az érték elegendő a biztonsági másolatok 7 napos tárolására.
- **Ltr**: SQL Database lehetőséget nyújt a teljes biztonsági másolatok hosszú távú megőrzésének beállítására akár 10 évig. Ha LTR szabályzatot állít be, akkor ezeket a biztonsági másolatokat az RA-GRS tárolóban automatikusan tárolja a rendszer, de szabályozhatja, hogy a biztonsági másolatok milyen gyakran legyenek átmásolva. A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A kiválasztott konfiguráció határozza meg, hogy mekkora tárterületet használ a rendszer a LTR biztonsági mentések esetén. A LTR-tároló költségének megbecsléséhez használhatja a LTR árképzési számológépet. További információ: [SQL Database hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

## <a name="next-steps"></a>További lépések

- Az általános célú és az üzleti szempontból kritikus szolgáltatási rétegekben elérhető egyedi számítási méretekről és tárolási méretekről az [önálló adatbázisok SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-single-databases.md)ismertető részben olvashat bővebben.
- Az általános célú és az üzleti szempontból kritikus szolgáltatási rétegekben található rugalmas készletekhez rendelkezésre álló számítási méretekről és tárolási méretekről a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](sql-database-vcore-resource-limits-elastic-pools.md)ismertető cikk nyújt tájékoztatást.
