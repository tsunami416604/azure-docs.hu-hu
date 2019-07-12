---
title: Az Azure SQL Database erőforráskorlátok – felügyelt példány |} A Microsoft Docs
description: Ez a cikk az Azure SQL Database erőforráskorlátok áttekintést nyújt a felügyelt példányokhoz.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: f4e19b916553912e36f2c3beee3f6a518b244e4d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707005"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Áttekintés az Azure SQL Database felügyelt példány erőforráskorlátok

Ez a cikk a erőforráskorlátok áttekintést nyújt az Azure SQL Database felügyelt példány, és ismerteti a határértékek növelését.

> [!NOTE]
> Támogatott szolgáltatások és a T-SQL eltérései utasításokat lásd [különbségek](sql-database-features.md) és [T-SQL utasítás támogatása](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>A példányszintű erőforráskorlátok

Felügyelt példány rendelkezik jellemzőit és erőforrás-korlátozások, amelyek az alapul szolgáló infrastruktúra és architektúra függenek. Korlátok hardver és a szolgáltatási szint függenek.

### <a name="hardware-generation-characteristics"></a>Hardverjellemzők generáció

Az Azure SQL Database felügyelt példány két hardvergenerációk telepíthető: Gen4 és Gen5. Hardvergenerációk eltérő jellemezőkkel rendelkeznek, az alábbi táblázatban leírtak szerint:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokkal, SSD virtuális mag csatolt = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorokkal, gyors NVMe SSD, virtuális mag = 1. LP (a hyper-szál) |
| Virtuális magok száma | 8, 16, 24 virtuális mag | 4, 8, 16, 24, 32, 40, 64, 80 virtuális magok |
| Maximális memória (memória/mag arány) | 7 GB / virtuális mag<br/>Adjon hozzá további virtuális magok memóriáját. | 5.1 GB / virtuális mag<br/>Adjon hozzá további virtuális magok memóriáját. |
| Maximális In-Memory OLTP memória | Instance-határérték: 3 GB / virtuális mag<br/>Adatbázis-korlátozások:<br/> – 8 magos: 8 GB adatbázisonként<br/> – 16 magos: 20 GB / adatbázis<br/> – 24-core: 36 GB adatbázisonként | Instance-határérték: 2,5 GB / virtuális mag<br/>Adatbázis-korlátozások:<br/> – 8 magos: 13 GB adatbázisonként<br/> – 16 magos: 32 GB / adatbázis |
| Maximális fenntartott storage (általános célú) |  8 TB | 8 TB |
| Maximális fenntartott storage (üzletileg kritikus) | 1 TB | 1 TB-os, 2 TB vagy 4 TB-os attól függően, a magok számát |

> [!IMPORTANT]
> Új Gen4 adatbázisok már nem támogatottak a Kelet-Ausztrália régióban.

### <a name="service-tier-characteristics"></a>Szolgáltatási szint tulajdonságok

Felügyelt példány két szolgáltatási csomagban rendelkezik: Általános célú és a kritikus fontosságú üzleti. Ezek a csomagok különböző képességeket biztosítanak, az alábbi táblázatban leírtak szerint:

| **Funkció** | **Általános célú** | **Üzletileg kritikus** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Maximális memória | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/virtuális mag)<br/>Adjon hozzá további virtuális magok memóriáját. | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/virtuális mag)<br/>Adjon hozzá további virtuális magok memóriáját. |
| Maximális foglalt tárméret | – 2 TB az 4 virtuális magra jogosult (csak Gen5)<br/>– A további méretek 8 TB | Gen4: 1 TB <br/> Gen5: <br/>– 1 TB-os 4, 8, 16 virtuális mag<br/>– A 24 virtuális mag 2 TB<br/>– 4 TB-os 32, 40, 64, 80 virtuális magok |
| Adatbázisok maximális mérete | Határozza meg a maximális tárhelyméretet a példány | Határozza meg a maximális tárhelyméretet a példány |
| Egy példány adatbázisok maximális száma | 100 | 100 |
| Adatbázisfájlok példányonként maximális száma | Legfeljebb 280 | – 32 767 fájlt adatbázisonként |
| Adatok/Log/iops-érték (becsült) | 500 – 7500 fájlonként<br/>\*[További IOPS lekérni a fájl méretét](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K - 110 K (1375/vCore)<br/>Adjon hozzá további virtuális mag a jobb i/o-teljesítmény eléréséhez. |
| Napló írási átviteli Sebességhatár | 3 MB/s / virtuális mag<br/>Maximális 22-es MB/s-példányonként | 4 MB/s / virtuális mag<br/>Maximális száma 48 MB/s-példányonként|
| A fájlmegosztásra (becsült) | 100 - fájlonként 250 MB/s<br/>\*[A fájl méretét a jobb i/o-teljesítmény](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | – |
| Tárolási i/o várakozási ideje (becsült) | 5-10 ms | 1-2 ms |
| Max. tempDB mérete | 192 - 1,920 GB (24 GB / virtuális mag)<br/>Adjon hozzá további virtuális magra jogosult a hely a TempDB bővítése. | Attól függ, a maximális példányméret. A TempDB napló mérete jelenleg legfeljebb 24GB/virtuális mag. |
| Munkamenetek maximális száma | 30000 | 30000 |

> [!NOTE]
> - Adat- és a naplófájlok méretét a felhasználó- és rendszer-adatbázisokat a storage példányméret, a rendszer összehasonlítja a maximális méretkorlát szerepelnek. Használat <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> rendszernézet meghatározni az összes adatbázis által felhasznált terület. Hibanaplók nem megőrzött és a méret nem tartalmazza. Biztonsági mentések nem szerepelnek a tárterület méretét.
> - Adatátviteli sebessége és IOPS is függ az oldal méretét, amely a felügyelt példány explicit módon nem korlátozódik.

## <a name="supported-regions"></a>Támogatott régiók

Csak a felügyelt példány hozható létre [támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Felügyelt példány létrehozása jelenleg nem támogatott régióban, is [küldjön egy támogatási kérést az Azure Portalon keresztül](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Támogatott előfizetéstípusok

Felügyelt példány üzembe helyezési jelenleg csak a következő típusú előfizetések a használatát támogatja:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Nagyvállalati fejlesztés és tesztelés](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [A Visual Studio-előfizetőknek járó havi Azure-kredit az előfizetések](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionális erőforrás-korlátozások

Támogatott előfizetéstípusok erőforrások régiónként csak korlátozott számú is tartalmazhat. Felügyelt példány két alapértelmezett korlátokkal rendelkeznek a Azure-régiónként egy előfizetés típusú típusától függően:

- **Alhálózathoz megadott korlátot**: Alhálózatok, ahol egy adott régióban felügyelt példányok üzembe helyezése maximális számát.
- **virtuális mag korlát**: A virtuális magok, egyetlen régión belüli összes példányán telepíthető maximális számát.

> [!Note]
> Ezek a korlátok alapértelmezett beállításokat, és nem technikai korlátai. A korlátok lehet nagyobb az igény szerinti hozzon létre egy speciális [támogatási kérést az Azure Portalon](#obtaining-a-larger-quota-for-sql-managed-instance) Ha a jelenlegi régióban több felügyelt példányok van szüksége. Alternatív megoldásként a támogatási kérések elküldése nélkül létrehozhat új felügyelt példányok egy másik Azure-régióban.

Az alábbi táblázat a támogatott előfizetések az alapértelmezett területi korlátozásait:

|Előfizetés típusa| Felügyelt példány alhálózatok maximális száma | Maximális számú virtuális mag egység * |
| :---| :--- | :--- |
|Utólagos, használatalapú fizetés|3|320|
|CSP |8 (az egyes régiókban ** 15)|960 (az egyes régiókban ** 1440)|
|Fejlesztés/tesztelés használatalapú fizetéssel|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (az egyes régiókban ** 15)|960 (az egyes régiókban ** 1440)|
|Visual Studio Enterprise|2 |64|
|A Visual Studio Professional és MSDN-platformok|2|32|

\* Ha azt tervezi, hogy az üzemelő példányokat, vegye figyelembe, hogy a kritikus fontosságú üzleti (BC) virtuális mag (miatt hozzáadott redundancia) használ fel további kapacitás általános célú (GP) virtuális mag, mint x 4. Igen, a számítások, 1 GP virtuális mag = 1 virtuális mag egység és 1 BC virtuális mag = 4 virtuális mag egység. A használati elemzés szemben az alapértelmezés szerinti korlátozásoknak leegyszerűsítése foglalják össze a virtuális mag egységek minden, a régióban, ahol a felügyelt példányok vannak telepítve, és az eredményeket hasonlítsa össze az előfizetés-típus példánykorlátok egység alhálózatok közötti. **Virtuális mag egységek maximális száma** korlát vonatkozik minden egyes előfizetés régióban. Nincs korlátozva az egyes alhálózatok száma, azzal a különbséggel, hogy több alhálózaton túlnyúló telepített összes virtuális magok összege alacsonyabb vagy azzal egyenlőnek kell lennie. **virtuális mag egység maximális**.

\*\* Nagyobb alhálózat és virtuális mag korlátait az alábbi régiókban érhetők el: Ausztrália keleti régiója, USA keleti RÉGIÓJA, USA 2. keleti régiója, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Egyesült Királyság déli régiója, Nyugat-Európa, USA 2. nyugati.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Nagyobb kvótát beszerzése az SQL felügyelt példánya

Ha a jelenlegi régióban több felügyelt példányok van szüksége, küldjön támogatási kérelmet a kvóta az Azure portal használatával kiterjesztheti.
A folyamat lehet beszerezni a nagyobb kvótát kezdeményezéséhez:

1. Nyissa meg **súgó + támogatás**, és kattintson a **új támogatási kérelem**.

   ![Súgó és támogatás](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Az alapismeretek lapon az új támogatási kérelem:
   - A **Problématípus**válassza **szolgáltatás és az előfizetések korlátai (kvóták)** .
   - Az **Előfizetés** beállításnál válassza ki az előfizetését.
   - A **kvótatípus**válassza **SQL Database felügyelt példányain**.
   - A **támogatási csomag**, válassza ki a támogatási csomagot.

     ![Probléma típusa kvóta](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kattintson a **Tovább** gombra.
4. Az a **probléma lapon** az új támogatási kérelem:
   - A **súlyossági**, válassza ki a súlyossági szintet a problémát.
   - A **részletek**, adjon meg további információt a problémáról, beleértve a hibaüzeneteket is.
   - A **fájlfeltöltés**, és azok bővebb fájl csatolása (legfeljebb 4 MB).

     ![Probléma részletei](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Egy érvényes kérelmet kell tartalmaznia:
     > - Régió, hogy melyik előfizetéssel korlátot növelni kell.
     > - Virtuális magok száma a szolgáltatási rétegben lévő alhálózatok a kvóta növelése után szükséges számú növelése (ha az egyik létező alhálózathoz van szüksége, ki kell bővíteni.
     > - Szükséges új alhálózatok számát és a szolgáltatási szinten belül az új alhálózatok száma virtuális magok teljes száma (ha üzembe kell helyeznie az új alhálózatokra felügyelt példányok).

5. Kattintson a **Tovább** gombra.
6. Adja meg a kapcsolattartási adatai lap az új támogatási kérelmet az elsődleges kapcsolattartási módszert (e-mail cím vagy telefonszám) és a kapcsolattartási adatait.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

- Felügyelt példánnyal kapcsolatos további információkért lásd: [Mi az a felügyelt példány?](sql-database-managed-instance.md).
- Díjszabási információkért tekintse meg a [SQL Database felügyelt példány díjszabása](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Ismerje meg, hogyan hozhat létre az első felügyelt példányhoz, lásd: [a rövid útmutató](sql-database-managed-instance-get-started.md).
