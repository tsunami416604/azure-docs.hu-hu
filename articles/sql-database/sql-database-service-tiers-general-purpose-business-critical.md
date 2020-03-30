---
title: Általános cél és üzleti szempontból kritikus
description: A cikk ismerteti az általános célú és üzleti legkritikusabb szolgáltatási szintek a virtuálismag-alapú vásárlási modell.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937843"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database szolgáltatási szintek

Az Azure SQL Database az SQL Server adatbázis-motor architektúráján alapul, amely a felhőkörnyezethez igazodva 99,99 százalékos rendelkezésre állást biztosít, még akkor is, ha infrastruktúra-hiba történt. Három szolgáltatási szint az Azure SQL Database-ben használatos, amelyek mindegyike más architekturális modellel. Ezek a szolgáltatási szintek a következők:

- [Általános cél](sql-database-service-tier-general-purpose.md), amelyet költségvetés-orientált munkaterhelések számára terveztek.
- [A nagy kapacitású](sql-database-service-tier-hyperscale.md), amelyet a legtöbb üzleti számítási feladathoz terveztek, jól méretezhető tárolást, olvasási horizontális felskálázást és gyors adatbázis-visszaállítási képességeket biztosítva.
- [Üzleti legkritikusabb](sql-database-service-tier-business-critical.md), amely úgy tervezték, alacsony késésű számítási feladatok nagy rugalmasságot a hibák és a gyors feladatátvétel.

Ez a cikk ismerteti a különbségeket lehet·tween a szolgáltatási szintek, a tárolási és biztonsági mentési szempontok az általános célra és az üzleti legkritikusabb szolgáltatási szintek a virtuálismag-alapú vásárlási modell.

## <a name="service-tier-comparison"></a>Szolgáltatási szint összehasonlítása

Az alábbi táblázat a legújabb generációs szolgáltatásszintek (Gen5) közötti legfontosabb különbségeket ismerteti. Vegye figyelembe, hogy a szolgáltatási szint jellemzői eltérőek lehetnek az egyetlen adatbázis ban és a felügyelt példányban.

| | Erőforrás típusa | Általános célú |  Rugalmas skálázás | Üzleti kritikus |
|:---:|:---:|:---:|:---:|:---:|
| **A legjobb** | |  Költségvetés-orientált kiegyensúlyozott számítási és tárolási lehetőségeket kínál. | A legtöbb üzleti számítási feladatok. Akár 100 TB-os automatikus méretezés, gördülékeny függőleges és vízszintes számítási méretezés, gyors adatbázis-visszaállítás. | OLTP alkalmazások magas tranzakciós aránnyal és alacsony I/o késéssel. A hibákkal és a gyors feladatátvételekkel szembeni legnagyobb ellenálló képességet kínálja több szinkron módon frissített kópia használatával.|
|  **Erőforrástípusban érhető el:** ||Egyetlen adatbázis / rugalmas készlet / felügyelt példány | Önálló adatbázis | Egyetlen adatbázis / rugalmas készlet / felügyelt példány |
| **Számítási méret**|Egyetlen adatbázis / rugalmas készlet | 1–80 virtuális mag | 1–80 virtuális mag | 1–80 virtuális mag |
| | Felügyelt példány | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| | Felügyelt példánykészletek | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag | N/A | N/A |
| **Tárolás típusa** | Összes | Prémium szintű távtároló (példányonként) | Nem kapcsolt tároló helyi SSD-gyorsítótárral (példányonként) | Szupergyors helyi SSD-tároló (példányonként) |
| **Adatbázisméret** | Egyetlen adatbázis / rugalmas készlet | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **Tárhely mérete** | Egyetlen adatbázis / rugalmas készlet | 5 GB – 4 TB | Akár 100 TB | 5 GB – 4 TB |
| | Felügyelt példány  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB-méret** | Egyetlen adatbázis / rugalmas készlet | [32 GB virtuális magonként](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB virtuális magonként](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB virtuális magonként](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Felügyelt példány  | [24 GB virtuális magonként](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | Akár 4 TB - [a tárolóméret korlátozza](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Naplóírási átviteli adatátus** | Önálló adatbázis | [1,875 MB/s/s virtuális magonként (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s virtuális magonként (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Felügyelt példány | [3 MB/s virtuális magonként (max. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [4 MB/s/s/vcore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Rendelkezésre állás**|Összes| 99.99% |  [99,95% egy másodlagos replikával, 99,99% több replikával](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [99,995% zónaredundáns egyetlen adatbázissal](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Biztonsági másolatok**|Összes|RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap)| RA-GRS, 7 nap, állandó időpont-időpont helyreállítás (PITR) | RA-GRS, 7-35 nap (alapértelmezés szerint 7 nap) |
|**Memórián belüli OLTP** | | N/A | N/A | Elérhető |
|**Írásvédett kópiák**| | 0 beépített <br> 0 - 4 [a georeplikáció](sql-database-active-geo-replication.md) használatával | 0 - 4 beépített | 1 beépített, árban foglalt <br> 0 - 4 [a georeplikáció](sql-database-active-geo-replication.md) használatával |
|**Árképzés/számlázás** | Önálló adatbázis | [a virtuális mag, a fenntartott tárhely és](https://azure.microsoft.com/pricing/details/sql-database/single/) a biztonsági mentési tárhely díja. <br/>Az IOPS nem számít fel díjat. | [az egyes replika és a használt tárolók virtuális magja](https://azure.microsoft.com/pricing/details/sql-database/single/) díjazva van. <br/>Az IOPS még nincs feltöltve. | [a virtuális mag, a fenntartott tárhely és](https://azure.microsoft.com/pricing/details/sql-database/single/) a biztonsági mentési tárhely díja. <br/>Az IOPS nem számít fel díjat. |
|| Felügyelt példány | [a virtuális mag, a fenntartott tárhely és](https://azure.microsoft.com/pricing/details/sql-database/managed/) a biztonsági mentési tároló díja. <br/>Az IOPS nem kerül felszámításra| N/A | [a virtuális mag, a fenntartott tárhely és](https://azure.microsoft.com/pricing/details/sql-database/managed/) a biztonsági mentési tároló díja. <br/>Az IOPS nem számít fel díjat.| 
|**Kedvezményes modellek**| | [Fenntartott példányok](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el fejlesztési és tesztelési előfizetésekesetén)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [kifizetendő](https://azure.microsoft.com/offers/ms-azr-0023p/) fejlesztői/tesztelési előfizetések| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el fejlesztési és tesztelési előfizetésekesetén)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [kifizetendő](https://azure.microsoft.com/offers/ms-azr-0023p/) fejlesztői/tesztelési előfizetések| [Fenntartott példányok](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (nem érhető el fejlesztési és tesztelési előfizetésekesetén)<br/>[Nagyvállalati](https://azure.microsoft.com/offers/ms-azr-0148p/) és [kifizetendő](https://azure.microsoft.com/offers/ms-azr-0023p/) fejlesztői/tesztelési előfizetések|

További információkért tekintse meg az egyetlen adatbázis [(virtuális mag)](sql-database-vcore-resource-limits-single-databases.md), az [egyadatbázis-készletek (virtuálismag)](sql-database-dtu-resource-limits-single-databases.md), az [egyadatbázis(DTU),](sql-database-dtu-resource-limits-single-databases.md) [az egyadatbázis-készletek (DTU)](sql-database-dtu-resource-limits-single-databases.md)és a [felügyelt példánylapok](sql-database-managed-instance-resource-limits.md) szolgáltatási szintjei közötti részletes különbségeket.

> [!NOTE]
> A virtuálismag-alapú vásárlási modell nagy méretű szolgáltatási szintjéről a [nagy kapacitású szolgáltatási szint című témakörben](sql-database-service-tier-hyperscale.md)talál további információt. A virtuálismag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlítását az [Azure SQL Database modellek és erőforrások vásárlása](sql-database-purchase-models.md)című témakörben ismeri.

## <a name="data-and-log-storage"></a>Adatok és naplótárolás

A következő tényezők befolyásolják az adatok és naplófájlok tárolásának mennyiségét, és az Általános cél és az üzleti szempontból kritikus elemre vonatkoznak. A Nagy kapacitású adatokkal és naplótárolással kapcsolatos részletekért olvassa el a [Nagykapacitású szolgáltatási szint .](sql-database-service-tier-hyperscale.md)

- A lefoglalt tárolót adatfájlok (MDF) és naplófájlok (LDF) használják.
- Minden egyes adatbázis-számítási méret támogatja a maximális adatbázisméretet, az alapértelmezett maximális mérete 32 GB.
- A szükséges egyetlen adatbázisméret (az MDF-fájl mérete) konfigurálásakor a rendszer automatikusan 30 százalékkal több további tárhelyet ad hozzá az LDF-fájlok támogatásához.
- Az SQL Database felügyelt példányainak tárolási méretét 32 GB többszöröseiben kell megadni.
- A 10 GB és a támogatott maximális méret között tetszőleges adatbázisméretet választhat.
  - A szabványos vagy általános célú szolgáltatási szinteken való tároláshoz növelje vagy csökkentse a méretet 10 GB-os lépésekben.
  - A prémium vagy üzleti legkritikusabb szolgáltatási szinteken való tároláshoz növelje vagy csökkentse a méretet 250 GB-os lépésekben.
- Az általános célú szolgáltatási `tempdb` szint, egy csatolt SSD-t használ, és ez a tárolási költség szerepel a virtuális mag árában.
- Az üzleti legkritikusabb `tempdb` szolgáltatási szinten megosztja a csatolt SSD-t `tempdb` az MDF- és LDF-fájlokkal, és a tárolási költség benne van a virtuális mag árában.

> [!IMPORTANT]
> Az MDF- és LDF-fájlok teljes lefoglalt tárhelye díjat számít fel.

Az MDF- és LDF-fájlok aktuális teljes méretének figyeléséhez használja [a sp_spaceused.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) Az egyes MDF- és LDF-fájlok aktuális méretének figyeléséhez használja a [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

## <a name="backups-and-storage"></a>Biztonsági mentések és tárolás

Az adatbázis-biztonsági mentések tárolása az SQL Database időpont-időpont visszaállítási (PITR) és [hosszú távú megőrzési (LTR)](sql-database-long-term-retention.md) képességeinek támogatásához van lefoglalva. Ez a tár terület külön van lefoglalva minden egyes adatbázishoz, és adatbázisonként két külön díjként kerül számlázásra.

- **PITR**: Az egyes adatbázis-biztonsági mentések másolása automatikusan [olvasási hozzáférésű georedundáns (RA-GRS) tároló.](../storage/common/storage-designing-ha-apps-with-ragrs.md) A tároló mérete dinamikusan növekszik az új biztonsági mentések létrehozásakor. A tárolót heti teljes biztonsági mentések, napi különbözeti biztonsági mentések és tranzakciónapló-biztonsági mentések használják, amelyeket 5 percenként másol. A tárolási felhasználás az adatbázis változásának sebességétés a biztonsági mentések megőrzési időszaka függ. Minden adatbázishoz 7 és 35 nap között külön megőrzési időszakot állíthat be. Az adatbázis méretének 100 százalékával (1x) egyenlő minimális tárterület-összeg ingyenesen biztosított. A legtöbb adatbázis esetében ez az összeg 7 nap biztonsági mentés tárolására elegendő.
- **LTR**: Az SQL Database lehetőséget kínál a teljes biztonsági mentések hosszú távú megőrzésének konfigurálására akár 10 évig. Ha beállít egy LTR-házirendet, ezek a biztonsági mentések automatikusan az RA-GRS tárolóban tárolódnak, de szabályozhatja, hogy milyen gyakran másolja a rendszer a biztonsági másolatokat. A különböző megfelelőségi követelmények teljesítéséhez különböző megőrzési időszakokat választhat ki a heti, havi és/vagy éves biztonsági mentéshez. A választott konfiguráció határozza meg, hogy mennyi tárhelyet fog használni az LTR biztonsági mentések. Az LTR-tárhely költségének becsléséhez használhatja az LTR díjkalkulátorát. További információt az [SQL Database hosszú távú megőrzése](sql-database-long-term-retention.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

- Az általános célú és az üzleti szempontból kritikus fontosságú szolgáltatási szinteken egyetlen adatbázishoz elérhető konkrét számítási méretekkel és tárterületekkel kapcsolatos részletekért tekintse meg az [SQL Database virtuálismagalapú erőforráskorlátait az egyes adatbázisokhoz.](sql-database-vcore-resource-limits-single-databases.md)
- Az általános célú és az üzleti legkritikusabb szolgáltatási szintek rugalmas készleteihez rendelkezésre álló konkrét számítási méretekről és tárolási méretekről az [SQL Database virtuálismagalapú erőforráskorlátok rugalmas készletekhez című](sql-database-vcore-resource-limits-elastic-pools.md)témakörében talál.
