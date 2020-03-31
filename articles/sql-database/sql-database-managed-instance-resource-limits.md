---
title: Erőforráskorlátok - felügyelt példány
description: Ez a cikk áttekintést nyújt az Azure SQL Database erőforrás-korlátok felügyelt példányok.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365387"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Áttekintő Az Azure SQL Database felügyelt példányerőforrás-korlátai

Ez a cikk áttekintést nyújt az Azure SQL Database felügyelt példányának műszaki jellemzőiről és erőforráskorlátairól, és tájékoztatást nyújt arról, hogyan kérhet növekedést ezekre a korlátokra.

> [!NOTE]
> A támogatott szolgáltatások és a T-SQL utasítások közötti különbségeket [lásd: Szolgáltatáskülönbségek](sql-database-features.md) és [T-SQL utasítástámogatás.](sql-database-managed-instance-transact-sql-information.md) Az egyetlen adatbázisban és a felügyelt példányban lévő szolgáltatásszintek közötti általános különbségekről a [Szolgáltatási szint összehasonlítása](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)című témakörben van.

## <a name="hardware-generation-characteristics"></a>A hardver generálási jellemzői

A felügyelt példány olyan jellemzőkkel és erőforráskorlátokkal rendelkezik, amelyek az alapul szolgáló infrastruktúrától és architektúrától függenek. Az Azure SQL Database által felügyelt példány két hardvergenerációra telepíthető: a Gen4 és a Gen5. A hardvergenerációk eltérő jellemzőkkel rendelkeznek, az alábbi táblázatban leírtak szerint:

|   | **Gen4 (1988)** | **Gen5 (1998)** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorok, csatlakoztatott SSD virtuális mag = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2,3 GHz-es és Intel SP-8160 (Skylake) processzorok, gyors NVMe SSD, vCore=1 LP (hiperszálas) |
| Virtuális magok száma | 8, 16, 24 virtuális mag | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| Maximális memória (memória/mag arány) | 7 GB virtuális magonként<br/>Adjon hozzá további virtuális magokat, hogy több memóriát kapjon. | 5,1 GB virtuális magonként<br/>Adjon hozzá további virtuális magokat, hogy több memóriát kapjon. |
| Max memórián belüli OLTP memória | Példánykorlát: 1-1,5 GB virtuális magonként| Példánykorlát: 0,8 – 1,65 GB virtuális magonként |
| Példány lefoglalt maximális tárolása |  Általános cél: 8 TB<br/>Üzleti kritikus: 1 TB | Általános cél: 8 TB<br/> Üzleti kritikus 1 TB, 2 TB vagy 4 TB a magok számától függően |

> [!IMPORTANT]
> - A Gen4 hardver fokozatosan megszűnik, és már nem érhető el az új telepítésekhez. Minden új felügyelt példányt gen5 hardveren kell telepíteni.
> - Fontolja meg [a felügyelt példányok Gen 5 hardverre való áthelyezését](sql-database-service-tiers-vcore.md) a virtuális mag és a tárolási méretezhetőség szélesebb körének, a gyorsított hálózatkezelésnek, a legjobb I/O-teljesítménynek és a minimális késésnek a megtapasztalása érdekében.

### <a name="in-memory-oltp-available-space"></a>Memórián belüli OLTP szabad hely 

A memórián belüli OLTP-terület mennyisége az [üzleti legkritikusabb](sql-database-service-tier-business-critical.md) szolgáltatási szinten a virtuális magok és a hardvergenerálás számától függ. Az alábbi táblázatban a memórián belüli OLTP objektumokhoz használható memóriakorlátok szerepelnek.

| Memórián belüli OLTP-tér  | **Gen5 (1998)** | **Gen4 (1988)** |
| --- | --- | --- |
| 4 virtuális mag  | 3,14 GB | |   
| 8 virtuális mag  | 6,28 GB | 8 GB |
| 16 virtuális mag | 15,77 GB | 20 GB |
| 24 virtuális mag | 25,25 GB | 36 GB |
| 32 virtuális mag | 37,94 GB | |
| 40 virtuális mag | 52,23 GB | |
| 64 virtuális mag | 99,9 GB    | |
| 80 virtuális mag | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Szolgáltatási szint jellemzői

A felügyelt példány két szolgáltatási szinttel rendelkezik: [Általános cél](sql-database-service-tier-general-purpose.md) és [Üzleti szempontból kritikus.](sql-database-service-tier-business-critical.md) Ezek a szintek [különböző képességeket biztosítanak](sql-database-service-tiers-general-purpose-business-critical.md)az alábbi táblázatban leírtak szerint.

> [!Important]
> Az üzleti legkritikusabb szolgáltatásszint további beépített példányt (másodlagos replika) biztosít, amely csak olvasható számítási feladatokhoz használható. Ha külön írási és írási lekérdezések és csak olvasható/analitikus/jelentési lekérdezések, kétszer virtuális magok és a memória ugyanazon az áron. Másodlagos replika előfordulhat, hogy néhány másodperccel lemarad az elsődleges példány, ezért úgy tervezték, hogy tehermentesíti a jelentéskészítési/analitikus számítási feladatok, amelyek nem igényelnek pontos aktuális adatállapot. Az alábbi táblázatban az **írásvédett lekérdezések** a másodlagos replika n végrehajtott lekérdezések.

| **Szolgáltatás** | **Általános cél** | **Üzleti kritikus** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Azonos számú virtuális mag van elkülönében írásvédett lekérdezésekhez. |
| Maximális memória | Gen4: 56 GB - 168 GB (7 GB/virtuális mag)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/virtuális mag)<br/>Adjon hozzá további virtuális magokat, hogy több memóriát kapjon. | Gen4: 56 GB - 168 GB (7 GB/virtuális mag)<br/>Gen5: 20,4 GB – 408 GB (5,1 GB/virtuális mag) írási és írási lekérdezésekhez<br/>+ további 20,4 GB - 408 GB (5.1GB/vCore) írásvédett lekérdezésekhez.<br/>Adjon hozzá további virtuális magokat, hogy több memóriát kapjon. |
| Példány maximális tárolási mérete (fenntartva) | - 2 TB 4 virtuális maghoz (csak Gen5 esetén)<br/>- 8 TB más méretekben | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB 4, 8, 16 virtuális mag<br/>- 2 TB 24 virtuális mag<br/>- 4 TB 32, 40, 64, 80 virtuális mag |
| Adatbázisok maximális mérete | A virtuális magok számától függően legfeljebb 2 TB – 8 TB a jelenleg rendelkezésre álló példányméretig). | A virtuális magok számától függően legfeljebb 1 TB – 4 TB a jelenleg rendelkezésre álló példányméretig). |
| Maximális hőmérsékletméret | Legfeljebb 24 GB/virtuális mag (96 – 1920 GB) és jelenleg rendelkezésre álló példánytárhely mérete.<br/>Adjon hozzá további virtuális magokat, hogy több TempDB tárhelyet kapjon.<br/> A naplófájl mérete 120 GB-ra korlátozódik.| Akár a jelenleg rendelkezésre álló példány tárolási mérete. |
| Adatbázisok maximális száma példányonként | 100, kivéve, ha a példány tárolási méretkorlátelérte. | 100, kivéve, ha a példány tárolási méretkorlátelérte. |
| Adatbázisfájlok maximális száma példányonként | Legfeljebb 280, kivéve, ha a példány tárolási mérete vagy az [Azure Premium Disk tárhelyfoglalási területkorlát](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) ot elérte. | Adatbázisonként 32 767 fájl, kivéve, ha elérte a példány tárolási méretkorlátját. |
| Az adatfájl maximális mérete | A jelenleg rendelkezésre álló példánytárterületre (max. 2 TB – 8 TB) és [az Azure Premium Disk tárhelyfoglalási területére korlátozódik.](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) | A jelenleg rendelkezésre álló példánytároló méretére korlátozva (legfeljebb 1 TB – 4 TB). |
| Naplófájl maximális mérete | Legfeljebb 2 TB és jelenleg rendelkezésre álló példány tárolási mérete. | Legfeljebb 2 TB és jelenleg rendelkezésre álló példány tárolási mérete. |
| Adatok/naplóIOPS (hozzávetőleges) | Akár 30–40 K IOPS példányonként*, 500–7500 fájlonként<br/>\*[A fájlméret növelése az IOPS további iops-jának növeléséhez](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Adjon hozzá további virtuális magokat a jobb I/O-teljesítmény érdekében. |
| Írási átviteli korlát naplózása (példányonként) | 3 MB/s virtuális magonként<br/>Legfeljebb 22 MB/s | 4 MB/s virtuális magonként<br/>Max 48 MB/s |
| Adatátviteli átmenő (hozzávetőleges) | 100 – 250 MB/s fájlonként<br/>\*[A fájlméret növelése a jobb i/o teljesítmény érdekében](#file-io-characteristics-in-general-purpose-tier) | Nem korlátozott. |
| Tárolási IO késés (hozzávetőleges) | 5-10 ms | 1-2 ms |
| Memórián belüli OLTP | Nem támogatott | Elérhető, [a méret a virtuális mag számától függ](#in-memory-oltp-available-space) |
| Maximális munkamenetek | 30000 | 30000 |
| [Írásvédett kópiák](sql-database-read-scale-out.md) | 0 | 1 (az ár tartalmazza) |

> [!NOTE]
> - **A jelenleg rendelkezésre álló példány tárolómérete** a fenntartott példány mérete és a használt tárhely közötti különbség.
> - Mind az adatok, mind a naplófájlok mérete a felhasználói és a rendszeradatbázisokban szerepel a példány tárolási méretében, amely összehasonlítja a maximális tárolási méretkorlátot. A <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> rendszernézetben határozhatja meg az adatbázisok által felhasznált teljes területet. A hibanaplók nem maradnak meg, és nem szerepelnek a méretben. A biztonsági mentések nem szerepelnek a tárhely méretében.
> - Átviteli és IOPS általános célú rétegben is függ a [fájl mérete,](#file-io-characteristics-in-general-purpose-tier) amely nem kifejezetten korlátozza a felügyelt példány.
> - Létrehozhat egy másik olvasható replika a különböző Azure-régióban automatikus feladatátvételi csoportok használatával.
> - A maximális példányIOPS a fájlelrendezéstől és a munkaterhelés elosztásától függ. Például, ha 7 x 1TB-os fájlokat hoz létre max 5K IOPS mindegyikés 7 kis fájlok (kisebb, mint 128 GB) 500 IOPS minden, akkor kap 38500 IOPS példányonként (7x5000+7x500), ha a munkaterhelés használhatja az összes fájlt. Vegye figyelembe, hogy bizonyos mennyiségű IOPS is használják az automatikus biztonsági mentések.

> [!NOTE]
> A [felügyelt példánykészletekerőforrás-korlátairól](sql-database-instance-pools.md#instance-pools-resource-limitations)ebben a cikkben talál további információt.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Fájl I/O-jellemzők az általános célú rétegben

Általános célú szolgáltatási szint minden adatbázisfájl kap dedikált IOPS és átviteli, amely a fájl méretétől függ. A nagyobb fájlok egyre több IOPS-t és átviteli műveletet kapnak. Az adatbázisfájlok IO-jellemzői az alábbi táblázatban láthatók:

| Fájlméret | >=0 és <=128 GiB | >128 és <=256 GiB | >256 és <= 512 GiB | >0,5 és <=1 TiB    | >1 és <=2 TiB    | >2 és <=4 TiB | 4. >és <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS fájlonként       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Átviteli-átviteli rendszer fájlonként | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Ha azt észleli, magas I/O-késés néhány adatbázis-fájl, vagy azt látja, hogy Az IOPS/átviteli teljesítmény eléri a korlátot, javíthatja a teljesítményt [a fájl méretének növelésével.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)

Vannak is példányszintű korlátok, például a maximális napló írási átviteli sebesség 22 MB/s, így előfordulhat, hogy nem tudja elérni a fájlt a naplófájlban, mert eléri a példány átviteli korlátját.

## <a name="supported-regions"></a>Támogatott régiók

A felügyelt példányok csak [támogatott régiókban hozhatók](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)létre. Ha egy olyan régióban szeretne felügyelt példányt létrehozni, amely jelenleg nem támogatott, [küldjön egy támogatási kérelmet az Azure Portalon keresztül.](quota-increase-request.md)

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

A felügyelt példány jelenleg csak a következő típusú előfizetéseken támogatja a telepítést:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Felosztó-ki-kiosztó](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Havi Azure-egyenleggel rendelkező előfizetések visual studio-előfizetők számára](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionális erőforrás-korlátozások

A támogatott előfizetési típusok régiónként korlátozott számú erőforrást tartalmazhatnak. A felügyelt példány két alapértelmezett korláttal rendelkezik Az Azure-régiónként (amely igény szerint növelhető egy speciális támogatási kérelem létrehozásával [az Azure Portalon az](quota-increase-request.md) előfizetés típusától függően:

- **Alhálózati korlát**: Azoknak az alhálózatoknak a maximális száma, ahol a felügyelt példányok egyetlen régióban vannak telepítve.
- **virtuálismag-egységkorhatár:** Az egy régió összes példányában telepíthető virtuálismag-egységek maximális száma. Egy GP virtuális mag egy virtuális magegységet használ, egy BC virtuális mag pedig 4 virtuálismag-egységet. A példányok teljes száma nem korlátozott, amíg a virtuális mag egységkorláton belül van.

> [!Note]
> Ezek a korlátok alapértelmezett beállítások, nem pedig technikai korlátozások. A korlátok igény szerint növelhetők egy speciális támogatási kérelem létrehozásával [az Azure Portalon,](quota-increase-request.md) ha több felügyelt példányra van szüksége az aktuális régióban. Alternatív megoldásként új felügyelt példányokat hozhat létre egy másik Azure-régióban támogatási kérelmek küldése nélkül.

Az alábbi táblázat a támogatott előfizetéstípusok **alapértelmezett területi korlátait** mutatja be (az alapértelmezett korlátok az alábbiakban ismertetett támogatási kérelemmel bővíthetők):

|Előfizetés típusa| Felügyelt példány alhálózatainak maximális száma | Virtuálismag-egységek maximális száma* |
| :---| :--- | :--- |
|Felosztó-ki-kiosztó|3|320|
|CSP |8 (egyes régiókban 15**)|960 (egyes régiókban 1440*)|
|Felosztó-kidolgozás–fejlesztés/tesztelés|3|320|
|Enterprise Dev/Test|3|320|
|Ea|8 (egyes régiókban 15**)|960 (egyes régiókban 1440*)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional és MSDN platformok|2|32|

\*A központi telepítések tervezésekor vegye figyelembe, hogy az üzleti legkritikusabb (BC) szolgáltatási szint négy (4) szor nagyobb virtuálismag-kapacitást igényel, mint az általános célú (GP) szolgáltatási szint. Például: 1 GP virtuálismag = 1 virtuálismag-egység és 1 BC virtuálismag = 4 virtuálismag egység. A felhasználás elemzésének az alapértelmezett korlátokkal szembeni egyszerűsítése érdekében összegezze a virtuálismag-egységeket a régió összes alhálózatában, ahol a felügyelt példányok vannak telepítve, és hasonlítsa össze az eredményeket az előfizetéstípus példányegység-korlátaival. **A virtuálismag-egységek** maximális száma egy régió minden egyes előfizetésére vonatkozik. Az egyes alhálózatokra nem vonatkozik korlátozás, azzal a különbséggel, hogy a több alhálózaton üzembe helyezett összes virtuális mag összegének alacsonyabbnak vagy egyenlőnek kell lennie a **virtuálismag-egységek maximális számával.**

\*\*A nagyobb alhálózati és virtuálismag-korlátok a következő régiókban érhetők el: Ausztrália kelet-, USA keleti régiója, USA keleti régiója 2, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság dél, Nyugat-Európa, USA nyugati régiója 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Kvótanövelés kérése az SQL által felügyelt példányhoz

Ha további felügyelt példányokra van szüksége a jelenlegi régiókban, küldjön egy támogatási kérelmet a kvóta kiterjesztéséhez az Azure Portalon keresztül. További információ: [Request kvóta emelése az Azure SQL Database.](quota-increase-request.md)

## <a name="next-steps"></a>További lépések

- A felügyelt példányról a [Mi a felügyelt példány című témakörben](sql-database-managed-instance.md)talál további információt.
- A díjszabásról az [SQL Database által felügyelt példányok díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/)című témakörben talál.
- Az első felügyelt példány létrehozásáról a rövid útmutató ismerteti [a rövid útmutatót.](sql-database-managed-instance-get-started.md)
