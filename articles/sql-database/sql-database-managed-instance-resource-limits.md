---
title: Erőforrás-korlátok Azure SQL Database – felügyelt példány
description: Ez a cikk áttekintést nyújt a felügyelt példányok Azure SQL Database erőforrásának korlátairól.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 723704b6b81aacf086ff42b02c32259d0e2f1c6d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687911"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>A felügyelt példányok erőforrás-korlátainak áttekintése Azure SQL Database

Ez a cikk áttekintést nyújt a Azure SQL Database felügyelt példányának technikai jellemzőiről és erőforrás-korlátairól, valamint arról, hogy miként kérhető a határértékek növelésére.

> [!NOTE]
> A támogatott funkciók és a T-SQL utasítások közötti különbségekért lásd a [funkciók](sql-database-features.md) és a [t-SQL-utasítások támogatását](sql-database-managed-instance-transact-sql-information.md). Az önálló adatbázis és a felügyelt példány szolgáltatási szintjei közötti általános különbségekért lásd a [szolgáltatási réteg összehasonlítását](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Hardver generálási jellemzői

A felügyelt példány olyan tulajdonságokkal és erőforrás-korlátozásokkal rendelkezik, amelyek az alapul szolgáló infrastruktúrától és architektúrától függenek. Azure SQL Database felügyelt példány két hardveres generációra telepíthető: Gen4 és Gen5. A hardveres generációk különböző jellemzőkkel rendelkeznek, az alábbi táblázatban leírtak szerint:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4-GHz processzorok, csatlakoztatott SSD virtuális mag = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2,3-GHz processzorok, gyors NVMe SSD, virtuális mag = 1 LP (Hyper-thread) |
| Virtuális magok száma | 8, 16, 24 virtuális mag | 4, 8, 16, 24, 32, 40, 64, 80 virtuális mag |
| Maximális memória (memória/mag arány) | 7 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. | 5,1 GB/virtuális mag<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| Memóriában tárolt OLTP memória maximális száma | Példány korlátja: 1 – 1,5 GB/virtuális mag| Példány korlátja: 0,8 – 1,65 GB/virtuális mag |
| Példányok maximálisan fenntartott tárterülete |  Általános célú: 8 TB<br/>Üzletileg kritikus: 1 TB | Általános célú: 8 TB<br/> Üzletileg kritikus 1 TB, 2 TB vagy 4 TB a magok számától függően |

> [!IMPORTANT]
> - A Gen4 hardvere folyamatban van. Ajánlott új felügyelt példányokat telepíteni a Gen5 hardveren.
> - A Gen4 hardver jelenleg csak a következő régiókban érhető el: Észak-Európa, Nyugat-Európa, az USA keleti régiója, az USA déli középső régiója, az USA északi középső régiója, USA 2. nyugati régiója, az USA középső régiója, Közép-India, Délkelet-Ázsia és Közép-Korea.

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

| **Funkció** | **általános célú** | **üzletileg kritikus** |
| --- | --- | --- |
| Virtuális mag száma\* | Gen4:8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*azonos számú virtuális mag dedikált a csak olvasási lekérdezésekhez. |
| Maximális memória | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB – 408 GB (5.1 GB/virtuális mag)<br/>További virtuális mag hozzáadásával további memóriát érhet el. | Gen4:56 GB – 168 GB (7GB/virtuális mag)<br/>Gen5:20,4 GB-408 GB (5.1 GB/virtuális mag) olvasási és írási lekérdezésekhez<br/>+ további 20,4 GB – 408 GB (5.1 GB/virtuális mag) a csak olvasható lekérdezésekhez.<br/>További virtuális mag hozzáadásával további memóriát érhet el. |
| Példány maximális tárolási mérete (fenntartott) | -2 TB 4 virtuális mag (csak Gen5)<br/>– 8 TB más méretekben | Gen4:1 TB <br/> Gen5 <br/>-1 TB 4, 8, 16 virtuális mag<br/>-2 TB 24 virtuális mag<br/>-4 TB 32, 40, 64, 80 virtuális mag |
| Adatbázisok maximális mérete | Az aktuálisan elérhető példány mérete (legfeljebb 2 TB-8 TB a virtuális mag számától függően). | Akár jelenleg elérhető példány mérete (max. 1 TB-4 TB a virtuális mag számától függően). |
| Maximális tempDB-méret | Legfeljebb 24 GB/virtuális mag (96-1 920 GB) és jelenleg elérhető példányok tárolási mérete.<br/>További virtuális mag hozzáadásával további TempDB lemezterületet érhet el.<br/> A naplófájl mérete legfeljebb 120 GB lehet.| Az aktuálisan elérhető példányok tárolási mérete. |
| Adatbázisok maximális száma egy példányon | 100, kivéve, ha elérte a példány tárolási méretének korlátját. | 100, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Adatbázisfájlok maximális száma egy példányban | Akár 280-ig, kivéve, ha elérte a példány tárolási méretét vagy az [Azure Premium lemez tárterületének lefoglalási](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) korlátját. | 32 767 fájl/adatbázis, kivéve, ha elérte a példány tárolási méretének korlátját. |
| Az adatfájlok maximális mérete | A jelenleg elérhető példányok tárolási mérete (max. 2 TB-8 TB) és az [Azure Premium Disk Storage kiosztási területe](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)korlátozódik. | A jelenleg elérhető példányok tárolási méretére korlátozódik (legfeljebb 1 TB-4 TB). |
| Naplófájl maximális mérete | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. | Legfeljebb 2 TB és jelenleg elérhető példány tárolási mérete. |
| Adat/napló IOPS (hozzávetőleges) | Akár 30-40 K IOPS *, 500-7500/fájl<br/>\*[a fájlméret növelésével további IOPS érhet el](#file-io-characteristics-in-general-purpose-tier)| 5,5 k-110 K (1375 IOPS/virtuális mag)<br/>További virtuális mag hozzáadásával jobb i/o-teljesítményt érhet el. |
| Napló írási átviteli korlátja (/példány) | 3 MB/s/virtuális mag<br/>Max. 22 MB/s | 4 MB/s/virtuális mag<br/>Maximális 48 MB/s |
| Adatátviteli sebesség (hozzávetőleges) | 100 – 250 MB/s/fájl<br/>\*[a fájlméret növelése a jobb IO-teljesítmény érdekében](#file-io-characteristics-in-general-purpose-tier) | Nem korlátozott. |
| Tárolási IO-késés (hozzávetőleges) | 5-10 MS | 1-2 MS |
| Memóriában tárolt OLTP | Nem támogatott | Elérhető, [a méret a virtuális mag számától függ](#in-memory-oltp-available-space) . |
| Munkamenetek maximális száma | 30000 | 30000 |
| [Írásvédett replikák](sql-database-read-scale-out.md) | 0 | 1 (az ár tartalmazza) |

> [!NOTE]
> - A **jelenleg elérhető példányok tárolási mérete** a fenntartott példányok méretének és a felhasznált tárolóhelynek a különbsége.
> - A felhasználói és a rendszeradatbázisokban lévő adatfájlok és a naplófájlok mérete is szerepel a tárolók maximális méretével összehasonlítva. A <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> rendszernézet használatával határozza meg az adatbázisok által felhasznált teljes területet. A hibanapló nem marad meg, és nem szerepel a méretben. A tárolók mérete nem tartalmazza a biztonsági mentéseket.
> - Az átviteli sebesség és a IOPS általános célú szinten a felügyelt példányok által kifejezetten nem korlátozott [fájlméretet](#file-io-characteristics-in-general-purpose-tier) is függ.
> - Az automatikus feladatátvételi csoportok használatával egy másik Azure-régióban is létrehozhat egy olvasható replikát.
> - A példányok maximális IOPS a fájlok elrendezésével és a számítási feladatok eloszlásával függ. Ha például 7 x GB-nyi fájlt hoz létre, amely Max 5K IOPS-val rendelkezik, minden egyes és 7 kis fájl (128 GB-nál kisebb) és 500 IOPS mindegyike 38500, akkor a számítási feladatok az összes fájlt igénybe vehetik (7x5000 + 7x500). Vegye figyelembe, hogy az automatikus biztonsági mentések esetében bizonyos mennyiségű IOPS is használatban van.

> [!NOTE]
> A [felügyelt példányok készletében található erőforrás-korlátokkal](sql-database-instance-pools.md#instance-pools-resource-limitations)kapcsolatos további információkat ebben a cikkben talál.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>A fájl i/o-jellemzői általános célú szinten

Általános célú szolgáltatási szinten minden adatbázisfájl dedikált IOPS és adatátviteli sebességet kap, amely a fájlmérettől függ. A nagyobb fájlok egyre több IOPS és átviteli sebességgel rendelkeznek. Az adatbázisfájlok IO-jellemzői az alábbi táblázatban láthatók:

| Fájlméret           | 0-128 GiB | 128 – 256 GiB | 256 – 512 GiB | 0,5 – 1 TiB    | 1-2 TiB    | 2-4 TiB | 4-8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Átviteli sebesség fájlonként | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Ha bizonyos adatbázisfájlok esetében magas IO-késleltetést észlel, vagy ha azt látja, hogy a IOPS/átviteli sebesség eléri a korlátot, [a fájlméret növelésével](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)növelheti a teljesítményt.

Léteznek olyan példány-szintű korlátok is, mint a maximális írási sebesség (22 MB/s), ezért előfordulhat, hogy a naplófájlt a rendszer nem tudja elérni a naplófájlban, mert a példány átviteli sebessége elérte a határértéket.

## <a name="supported-regions"></a>Támogatott régiók

Felügyelt példányok csak a [támogatott régiókban](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)hozhatók létre. Ha a felügyelt példányt olyan régióban szeretné létrehozni, amely jelenleg nem támogatott, akkor [a Azure Portalon keresztül küldhet támogatási kérést](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

A felügyelt példány jelenleg csak a következő típusú előfizetések esetében támogatja a telepítést:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Utólagos elszámolás](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőalapú szolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Előfizetések havi Azure-Kredittel a Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionális erőforrásokra vonatkozó korlátozások

A támogatott előfizetési típusok régiónként korlátozott számú erőforrást tartalmazhatnak. A felügyelt példányok Azure-régiónként két alapértelmezett korláttal rendelkeznek (ez az igény szerint növelhető, ha egy speciális [támogatási kérelmet hoz létre a Azure Portal) az](#obtaining-a-larger-quota-for-sql-managed-instance)előfizetés típusától függően:

- **Alhálózat korlátja**: azon alhálózatok maximális száma, amelyekben a felügyelt példányok egyetlen régióban vannak üzembe helyezve.
- **virtuális mag-egység korlátja**: az egyetlen régió összes példányán üzembe helyezhető virtuális mag egységek maximális száma. Az egyik GP-virtuális mag egy virtuális mag egységet használ, és az egyik BC-virtuális mag 4 virtuális mag-egységet vesz igénybe. A példányok teljes száma nincs korlátozva, amíg az virtuális mag-egység korlátján belül van.

> [!Note]
> Ezek a korlátok alapértelmezett beállítások, és nem technikai korlátozások. Ha az aktuális régióban több felügyelt példányra van szüksége, a korlátokat igény szerint növelheti a [Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) . Alternatív megoldásként új felügyelt példányokat is létrehozhat egy másik Azure-régióban támogatási kérések küldése nélkül.

A következő táblázat a támogatott előfizetési típusok **alapértelmezett regionális korlátait** mutatja be (az alapértelmezett határértékek az alább ismertetett támogatási kérelem használatával bővíthetők):

|Előfizetés típusa| Felügyelt példányok alhálózatai maximális száma | Virtuális mag egységek maximális száma * |
| :---| :--- | :--- |
|Utólagos, használatalapú fizetés|3|320|
|CSP |8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Utólagos elszámolású fejlesztési/tesztelési funkciók|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 egyes régiókban * *)|960 (1440 egyes régiókban * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional és MSDN platformok|2|32|

\* az üzembe helyezések megtervezése során vegye figyelembe, hogy üzletileg kritikus (BC) szolgáltatási szintet négy (4) alkalommal kell virtuális mag, mint a általános célú (GP) szolgáltatási szintet. Például: 1 GP virtuális mag = 1 virtuális mag egység és 1 BC virtuális mag = 4 virtuális mag egység. Ha egyszerűsíteni szeretné a használati elemzést az alapértelmezett korlátokkal, foglalja össze a virtuális mag egységeket azon régió összes alhálózatán, amelyben a felügyelt példányok telepítve vannak, és hasonlítsa össze az eredményeket az előfizetési típushoz tartozó példány-egység korlátaival. A **virtuális mag-egységek maximális száma** a régió minden előfizetésére érvényes. Az egyes alhálózatokon nincs korlát, kivéve, hogy a több alhálózaton üzembe helyezett összes virtuális mag összegének kisebbnek vagy egyenlőnek kell lennie a **virtuális mag egységek maximális számával**.

\*\* nagyobb alhálózat-és virtuális mag-korlátok érhetők el a következő régiókban: Kelet-Ausztrália, USA keleti régiója, USA 2. keleti régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati régiója.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Nagyobb kvóta beszerzése a felügyelt SQL-példányhoz

Ha az aktuális régiókban több felügyelt példányra van szüksége, küldjön egy támogatási kérést a kvóta kiterjesztéséhez a Azure Portal használatával.
Nagyobb kvóta beszerzési folyamatának kezdeményezése:

1. Nyissa meg a **Súgó + támogatás**menüpontot, és kattintson az **új támogatási kérelem**elemre.

   ![Súgó és támogatás](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Az új támogatási kérelem alapjai lapon:
   - A **probléma típusa**beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.
   - Az **Előfizetés** beállításnál válassza ki az előfizetését.
   - A **kvóta típusa**beállításnál válassza **SQL Database felügyelt példány**lehetőséget.
   - **Támogatási csomag**esetén válassza ki a támogatási csomagot.

     ![Probléma típusú kvóta](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kattintson a **Tovább** gombra.
4. Az új támogatási kérelem **probléma lapján** :
   - A **Súlyosság**beállításnál válassza ki a probléma súlyossági szintjét.
   - **Részletekért**adja meg a hibával kapcsolatos további információkat, beleértve a hibaüzeneteket is.
   - Fájlfeltöltés **esetén**csatoljon egy fájlt további információkkal (legfeljebb 4 MB).

     ![Probléma részletei](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Érvényes kérelemnek tartalmaznia kell A következőket:
     > - Az a régió, amelyben az előfizetési korlátot növelni kell.
     > - A kvóták növekedése után a meglévő alhálózatokban lévő virtuális mag szükséges száma (ha a meglévő alhálózatok bármelyikét ki kell bontani.
     > - Szükséges számú új alhálózat és virtuális mag száma az új alhálózatokon belül (ha felügyelt példányokat kell központilag telepíteni az új alhálózatokban).

5. Kattintson a **Tovább** gombra.
6. Az új támogatási kérelem kapcsolattartási adatok lapján adja meg az előnyben részesített kapcsolattartási módszert (e-mail vagy telefon) és a kapcsolattartási adatokat.
7. Kattintson a **Létrehozás** elemre.

## <a name="next-steps"></a>További lépések

- A felügyelt példányokkal kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md).
- A díjszabással kapcsolatos információkért lásd: [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Az első felügyelt példány létrehozásával kapcsolatos információkért tekintse meg [a gyors üzembe helyezési útmutatót](sql-database-managed-instance-get-started.md).
