---
title: Erőforrás-korlátok – felügyelt példány
description: Ez a cikk áttekintést nyújt a felügyelt példányok Azure SQL Database erőforrásának korlátairól.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: d46746b03935ff5d7893d149b9eb744bb65293ed
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774225"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>A felügyelt példányok erőforrás-korlátainak áttekintése Azure SQL Database

Ez a cikk áttekintést nyújt a Azure SQL Database felügyelt példányának technikai jellemzőiről és erőforrás-korlátairól, valamint arról, hogy miként kérhető a határértékek növelésére.

> [!NOTE]
> A támogatott funkciók és a T-SQL utasítások közötti különbségekért lásd a [funkciók](sql-database-features.md) és a [t-SQL-utasítások támogatását](sql-database-managed-instance-transact-sql-information.md). Az önálló adatbázis és a felügyelt példány szolgáltatási szintjei közötti általános különbségekért lásd a [szolgáltatási réteg összehasonlítását](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Hardver generálási jellemzői

A felügyelt példány olyan tulajdonságokkal és erőforrás-korlátozásokkal rendelkezik, amelyek az alapul szolgáló infrastruktúrától és architektúrától függenek. Azure SQL Database felügyelt példány két hardveres generációra telepíthető: Gen4 és Gen5. A hardveres generációk különböző jellemzőkkel rendelkeznek, az alábbi táblázatban leírtak szerint:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4-GHz processzorok, csatlakoztatott SSD virtuális mag = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2,3-GHz és Intel SP-8160 (Skylake) processzorok, gyors NVMe SSD, virtuális mag = 1 LP (Hyper-thread) |
| Virtuális magok száma | 8, 16, 24 virtuális mag | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| Maximális memória (memória/mag arány) | 7 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. | 5,1 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| Memóriában tárolt OLTP memória maximális száma | Példány korlátja: 1 – 1,5 GB/virtuális mag| Példány korlátja: 0,8 – 1,65 GB/virtuális mag |
| Példányok maximálisan fenntartott tárterülete |  Általános célú: 8 TB<br/>Üzletileg kritikus: 1 TB | Általános célú: 8 TB<br/> Üzletileg kritikus 1 TB, 2 TB vagy 4 TB a magok számától függően |

> [!IMPORTANT]
> - A Gen4 hardverek fokozatos kiépítése folyamatban van, és az új központi telepítések esetében már nem érhető el. Minden új felügyelt példányt telepíteni kell a Gen5 hardveren.
> - Érdemes [áthelyezni a felügyelt példányokat az Gen 5](sql-database-service-tiers-vcore.md) hardverre a virtuális mag és a tárterület méretezhetőségének, a gyorsított hálózatkezelésnek, a legjobb IO-teljesítménynek és a minimális késésnek a megtapasztalása érdekében.

### <a name="in-memory-oltp-available-space"></a>Memóriában tárolt OLTP szabad területe 

[Üzletileg kritikus](sql-database-service-tier-business-critical.md) szolgáltatási szinten a memóriában lévő OLTP-terület mennyisége a virtuális mag és a hardveres generáció számától függ. A következő táblázatban a memóriában tárolt OLTP-objektumok számára felhasználható memória korlátai láthatók.

| Memóriában tárolt OLTP terület  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuális mag  | 3,14 GB | |   
| 8 virtuális mag  | 6,28 GB | 8 GB |
| 16 virtuális mag | 15,77 GB | 20 GB |
| 24 virtuális mag | 25,25 GB | 36 GB |
| 32 virtuális mag | 37,94 GB | |
| 40 virtuális mag | 52,23 GB | |
| 64 virtuális mag | 99,9 GB    | |
| 80 virtuális mag | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Szolgáltatási szintek jellemzői

A felügyelt példány két szolgáltatási szintet tartalmaz: [általános célú](sql-database-service-tier-general-purpose.md) és [üzletileg kritikus](sql-database-service-tier-business-critical.md). Ezek a szintek [különböző képességeket](sql-database-service-tiers-general-purpose-business-critical.md)biztosítanak, az alábbi táblázatban leírtak szerint.

> [!Important]
> A üzletileg kritikus Service-réteg további beépített példányt (másodlagos replikát) tartalmaz, amelyek csak olvasható számítási feladatokhoz használhatók. Ha elkülönítheti az írási és olvasási lekérdezéseket és a csak olvasható/analitikus/jelentéskészítési lekérdezéseket, akkor a rendszer kétszer virtuális mag és memóriát is biztosít ugyanarra az árakra. Előfordulhat, hogy a másodlagos replika néhány másodpercen belül elmarad az elsődleges példány mögött, ezért olyan jelentéskészítési/elemzési számítási feladatok kiszervezésére lett kialakítva, amelyek nem igényelnek pontos aktuális állapotot. Az alábbi táblázatban a **csak olvasható lekérdezések** a másodlagos replikán végrehajtott lekérdezések.

| **Szolgáltatás** | **általános célú** | **üzletileg kritikus** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4:8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*A csak olvasható lekérdezések esetében azonos számú virtuális mag van hozzárendelve. |
| Maximális memória | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB – 408 GB (5.1 GB/virtuális mag)<br/>További virtuális mag hozzáadásával további memóriát érhet el. | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB-408 GB (5.1 GB/virtuális mag) olvasási és írási lekérdezésekhez<br/>+ további 20,4 GB – 408 GB (5.1 GB/virtuális mag) a csak olvasható lekérdezésekhez.<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| Példány maximális tárolási mérete (fenntartott) | -2 TB 4 virtuális mag (csak Gen5)<br/>– 8 TB más méretekben | Gen4:1 TB <br/> Gen5 <br/>-1 TB 4, 8, 16 virtuális mag<br/>-2 TB 24 virtuális mag<br/>-4 TB 32, 40, 64, 80 virtuális mag |
| Adatbázisok maximális mérete | Az aktuálisan elérhető példány mérete (legfeljebb 2 TB-8 TB a virtuális mag számától függően). | Akár jelenleg elérhető példány mérete (max. 1 TB-4 TB a virtuális mag számától függően). |
| Maximális tempDB-méret | Legfeljebb 24 GB/virtuális mag (96-1 920 GB) és jelenleg elérhető példányok tárolási mérete.<br/>További virtuális mag hozzáadásával további TempDB lemezterületet érhet el.<br/> A naplófájl mérete legfeljebb 120 GB lehet.| Az aktuálisan elérhető példányok tárolási mérete. |
| Adatbázisok maximális száma egy példányon | 100, kivéve, ha elérte a példány tárolási méretének korlátját. | 100, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Adatbázisfájlok maximális száma egy példányban | Akár 280-ig, kivéve, ha elérte a példány tárolási méretét vagy az [Azure Premium lemez tárterületének lefoglalási](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) korlátját. | 32 767 fájl/adatbázis, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Az adatfájlok maximális mérete | A jelenleg elérhető példányok tárolási mérete (max. 2 TB-8 TB) és az [Azure Premium Disk Storage kiosztási területe](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)korlátozódik. | A jelenleg elérhető példányok tárolási méretére korlátozódik (legfeljebb 1 TB-4 TB). |
| Naplófájl maximális mérete | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. |
| Adat/napló IOPS (hozzávetőleges) | Akár 30-40 K IOPS *, 500-7500/fájl<br/>\*[A fájlméret növelésével további IOPS érhet el](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (2500 IOPS/virtuális mag)<br/>További virtuális mag hozzáadásával jobb i/o-teljesítményt érhet el. |
| Napló írási átviteli korlátja (/példány) | 3 MB/s/virtuális mag<br/>Max. 22 MB/s | 4 MB/s/virtuális mag<br/>Maximális 48 MB/s |
| Adatátviteli sebesség (hozzávetőleges) | 100 – 250 MB/s/fájl<br/>\*[A fájlméret növelése jobb i/o-teljesítmény eléréséhez](#file-io-characteristics-in-general-purpose-tier) | Nem korlátozott. |
| Tárolási IO-késés (hozzávetőleges) | 5-10 MS | 1-2 MS |
| Memóriában tárolt OLTP | Nem támogatott | Elérhető, [a méret a virtuális mag számától függ](#in-memory-oltp-available-space) . |
| Munkamenetek maximális száma | 30000 | 30000 |
| [Írásvédett replikák](sql-database-read-scale-out.md) | 0 | 1 (az ár tartalmazza) |
| Számítási elkülönítés | Gen5<br/>– 80 virtuális mag esetén támogatott<br/>-más méretek esetében nem támogatott<br/><br/>Az Gen4 nem támogatott az elavultság miatt|Gen5<br/>– 60, 64, 80 virtuális mag esetén támogatott<br/>-más méretek esetében nem támogatott<br/><br/>Az Gen4 nem támogatott az elavultság miatt|

> [!NOTE]
> - A **jelenleg elérhető példányok tárolási mérete** a fenntartott példányok méretének és a felhasznált tárolóhelynek a különbsége.
> - A felhasználói és a rendszeradatbázisokban lévő adatfájlok és a naplófájlok mérete is szerepel a tárolók maximális méretével összehasonlítva. A <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> rendszernézet használatával határozza meg az adatbázisok teljes felhasznált területét. A hibanapló nem marad meg, és nem szerepel a méretben. A tárolók mérete nem tartalmazza a biztonsági mentéseket.
> - Az átviteli sebesség és a IOPS általános célú szinten a felügyelt példányok által kifejezetten nem korlátozott [fájlméretet](#file-io-characteristics-in-general-purpose-tier) is függ.
> - Az automatikus feladatátvételi csoportok használatával egy másik Azure-régióban is létrehozhat egy olvasható replikát.
> - A példányok maximális IOPS a fájlok elrendezésével és a számítási feladatok eloszlásával függ. Tegyük fel például, hogy ha 7 x TB-os fájlt hoz létre, amely Max 5K IOPS-t és 7 kis fájlt (128 GB-nál kisebb) a 500 38500 IOPS Vegye figyelembe, hogy az automatikus biztonsági mentések esetében bizonyos mennyiségű IOPS is használatban van.

> [!NOTE]
> A [felügyelt példányok készletében található erőforrás-korlátokkal](sql-database-instance-pools.md#instance-pools-resource-limitations)kapcsolatos további információkat ebben a cikkben talál.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>A fájl i/o-jellemzői általános célú szinten

Általános célú szolgáltatási szinten minden adatbázisfájl dedikált IOPS és adatátviteli sebességet kap, amely a fájlmérettől függ. A nagyobb fájlok egyre több IOPS és átviteli sebességgel rendelkeznek. Az adatbázisfájlok IO-jellemzői az alábbi táblázatban láthatók:

| Fájlméret | >= 0 és <= 128 GiB | >128 és <= 256 GiB | >256 és <= 512 GiB | >0,5 és <= 1 TiB    | >1 és <= 2 TiB    | >2 és <= 4 TiB | >4 és <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Átviteli sebesség fájlonként | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Ha bizonyos adatbázisfájlok esetében magas IO-késleltetést észlel, vagy ha azt látja, hogy a IOPS/átviteli sebesség eléri a korlátot, [a fájlméret növelésével](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)növelheti a teljesítményt.

Léteznek olyan példány-szintű korlátok is, mint a maximális írási sebesség (22 MB/s), ezért előfordulhat, hogy a naplófájlt a rendszer nem tudja elérni a naplófájlban, mert a példány átviteli sebessége elérte a határértéket.

## <a name="supported-regions"></a>Támogatott régiók

Felügyelt példányok csak a [támogatott régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)hozhatók létre. Ha a felügyelt példányt olyan régióban szeretné létrehozni, amely jelenleg nem támogatott, akkor [a Azure Portalon keresztül küldhet támogatási kérést](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

A felügyelt példány jelenleg csak a következő típusú előfizetések esetében támogatja a telepítést:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Utólagos elszámolás](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőalapú szolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Előfizetések havi Azure-Kredittel a Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionális erőforrásokra vonatkozó korlátozások

A támogatott előfizetési típusok régiónként korlátozott számú erőforrást tartalmazhatnak. A felügyelt példányok Azure-régiónként két alapértelmezett korláttal rendelkeznek (ez az igény szerint növelhető, ha egy speciális [támogatási kérést hoz létre a Azure Portal az](quota-increase-request.md) előfizetés típusától függően:

- **Alhálózat korlátja**: azon alhálózatok maximális száma, amelyekben a felügyelt példányok egyetlen régióban vannak üzembe helyezve.
- **virtuális mag-egység korlátja**: az egyetlen régió összes példányán üzembe helyezhető virtuális mag egységek maximális száma. Az egyik GP-virtuális mag egy virtuális mag egységet használ, és az egyik BC-virtuális mag 4 virtuális mag-egységet vesz igénybe. A példányok teljes száma nincs korlátozva, amíg az virtuális mag-egység korlátján belül van.

> [!Note]
> Ezek a korlátok alapértelmezett beállítások, és nem technikai korlátozások. Ha az aktuális régióban több felügyelt példányra van szüksége, a korlátokat igény szerint növelheti a [Azure Portal](quota-increase-request.md) . Alternatív megoldásként új felügyelt példányokat is létrehozhat egy másik Azure-régióban támogatási kérések küldése nélkül.

A következő táblázat a támogatott előfizetési típusok **alapértelmezett regionális korlátait** mutatja be (az alapértelmezett határértékek az alább ismertetett támogatási kérelem használatával bővíthetők):

|Előfizetés típusa| Felügyelt példányok alhálózatai maximális száma | Virtuális mag egységek maximális száma * |
| :---| :--- | :--- |
|Utólagos elszámolás|3|320|
|CSP |8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Utólagos elszámolású fejlesztési/tesztelési funkciók|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional és MSDN platformok|2|32|

\*Az üzembe helyezések megtervezése során vegye figyelembe, hogy üzletileg kritikus (BC) szolgáltatási szintet négy (4) alkalommal kell virtuális mag, mint a általános célú (GP) szolgáltatási szintet. Például: 1 GP virtuális mag = 1 virtuális mag egység és 1 BC virtuális mag = 4 virtuális mag egység. Ha egyszerűsíteni szeretné a használati elemzést az alapértelmezett korlátokkal, foglalja össze a virtuális mag egységeket azon régió összes alhálózatán, amelyben a felügyelt példányok telepítve vannak, és hasonlítsa össze az eredményeket az előfizetési típushoz tartozó példány-egység korlátaival. A **virtuális mag-egységek maximális száma** a régió minden előfizetésére érvényes. Az egyes alhálózatokon nincs korlát, kivéve, hogy a több alhálózaton üzembe helyezett összes virtuális mag összegének kisebbnek vagy egyenlőnek kell lennie a **virtuális mag egységek maximális számával**.

\*\*A nagyobb alhálózat-és virtuális mag korlátozások a következő régiókban érhetők el: Kelet-Ausztrália, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, Dél-Európa, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati régiója.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Kvóta növelésének kérése az SQL felügyelt példányához

Ha az aktuális régiókban több felügyelt példányra van szüksége, küldjön egy támogatási kérést a kvóta kiterjesztéséhez a Azure Portal használatával. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Az első felügyelt példány létrehozásával kapcsolatos információkért tekintse meg [a gyors üzembe helyezési útmutatót](sql-database-managed-instance-get-started.md).
