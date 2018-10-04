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
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 10/03/2018
ms.openlocfilehash: 6ae9b3f71cb5328c7f4a7ee8e43ec0aff8b5fcec
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48267784"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Áttekintés az Azure SQL Database felügyelt példányain erőforráskorlátok

Ez a cikk az Azure SQL Database felügyelt példányába erőforráskorlátok áttekintést nyújt, és ismerteti, hogyan hozhat létre az alapértelmezett területi előfizetési korlátozásait növelésére. 

> [!NOTE]
> Egyéb felügyelt példány korlátozások is érvényesek, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-managed-instance.md#vcore-based-purchasing-model) és [felügyelt példány szolgáltatásszintek](sql-database-managed-instance.md#managed-instance-service-tiers).

## <a name="instance-level-resource-limits"></a>A példányszintű erőforráskorlátok

Felügyelt példány jellemzőit és az adott díjcsomagtól függ az alapul szolgáló infrastruktúra és architektúra erőforráskorlátok rendelkezik. Korlátok hardver és a szolgáltatási szint függenek.

### <a name="hardware-generation-characteristics"></a>Hardverjellemzők generáció

Az Azure SQL Database felügyelt példánya is telepíthető a két hardver generációja (Gen4 és Gen5). Hardvergenerációk az alábbi táblázatban ismertetett más jellemzőkkel rendelkeznek:

|   | **A gen 4** | **A gen 5** |
| --- | --- | --- |
| Hardver | Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorokkal, SSD virtuális mag csatolt = 1 PP (fizikai mag) | Intel E5-2673 v4 (Broadwell) 2.3 GHz-es processzorokkal, SSD, virtuális mag eNVM gyors = 1. LP (a hyper-szál) |
| Compute | 8, 16, 24 virtuális mag | 8, 16, 24, 32, 40, 64, 80 virtuális magok |
| Memory (Memória) | 7 GB / virtuális mag | 5.5-ös GB / virtuális mag |
| Maximális tárterület (üzletileg kritikus) | 1 TB-OT | 1 TB-os, 2 TB-os, 4 TB-os attól függően, a magok számát |

### <a name="service-tier-characteristics"></a>Szolgáltatási szint tulajdonságok

Felügyelt példány két szolgáltatási csomagban – általános célú és az üzletileg kritikus (nyilvános előzetes verzió) rendelkezik. Ezek a csomagok különböző képességeket biztosítanak, az alábbi táblázatban leírtak szerint:

| **Funkció** | **Általános célú** | **Üzletileg kritikus (előzetes verzió)** |
| --- | --- | --- |
| Virtuális magok száma\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Memory (Memória) | Gen4: 56GB – 156GB<br/>Gen5: 44GB – 440GB<br/>\*Arányos virtuális magok száma | Gen4: 56GB – 156GB <br/> Gen5: 44GB – 440GB<br/>\*Arányos virtuális magok száma |
| Maximális tárméret | 8 TB | Általános 4: 1 TB <br/> A gen 5: <br/>– 1 TB-os 8, 16 virtuális mag<br/>– A 24 virtuális mag 2 TB<br/>– 4 TB-os 32, 40, 64, 80 virtuális magok |
| Maximális tárterület adatbázisonként | Határozza meg a maximális tárhelyméretet a példány | Határozza meg a maximális tárhelyméretet a példány |
| Egy példány adatbázisok maximális száma | 100 | 100 |
| Maximális adatbázisfájlok példányonként | Legfeljebb 280 | Korlátlan |
| Várt adatbázisonkénti maximális tárolási IOPS | Adatfájl 500-7500 IOPS ([adatok mérete attól függ,](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)). | Alapul szolgáló SSD sebességétől függ. |

## <a name="supported-regions"></a>Támogatott régiók

Csak a felügyelt Instanced hozható létre [támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database). Ha szeretne egy felügyelt példány létrehozása jelenleg nem támogatott a régióban, akkor [küldési támogatási kérést az Azure Portalon keresztül](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Támogatott előfizetéstípusok

Felügyelt példány üzembe helyezési jelenleg csak a következő típusú előfizetések a használatát támogatja:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> Ez a korlátozás csak átmenetileg létezik. Új előfizetés-típusok a jövőben engedélyezve lesz.

## <a name="regional-resource-limitations"></a>Regionális erőforrás-korlátozások

Támogatott előfizetéstípusok erőforrások régiónként csak korlátozott számú is tartalmazhat. Felügyelt példány két alapértelmezett korlátokkal rendelkeznek a Azure-régiónként egy előfizetés típusú típusától függően:

- **Alhálózathoz megadott korlátot**: alhálózatok, ahol egy adott régióban felügyelt példányok üzembe helyezése a maximális számát.
- **A maximális szám példány**:, egyetlen régióban telepíthető példányok maximális száma.

Az alábbi táblázatban láthatók a támogatott előfizetések alapértelmezett regionális korlátozásait:

|Előfizetés típusa| Felügyelt példány alhálózatok maximális száma | Példányok maximális száma |Maximális száma a csoportházirend által felügyelt példány *|BC maximális száma a felügyelt példány *|
| :---| :--- | :--- |:--- |:--- |
|Utólagos, használatalapú fizetés|1 *|4 *|4 *|1 *|
|CSP |1 *|4 *|4 *|1 *|
|Nagyvállalati szerződés|3 **|12 **|12 **|3 **|

\* Vagy telepítheti 1 BC vagy az egyik alhálózat 4 GP-példány, hogy az alhálózat "példány egységek" száma soha nem meghaladja a 4.

** A példányok egy szolgáltatási rétegben található maximális száma vonatkozik, ha nincsenek példányok egy másik szolgáltatási rétegben található. Abban az esetben, ha azt tervezi, a csoportházirend és BC példányok ugyanazon az alhálózaton belül vegyesen, használja a következő szakasz referenciaként engedélyezett kombinációját. Egyszerű szabály alhálózatok száma nem haladhatja meg a 3, és a példány egységek száma nem haladhatja meg a 12.

Ezek a korlátok növelhető a speciális létrehozásával [támogatási kérést az Azure Portalon](#obtaining-a-larger-quota-for-sql-managed-instance) Ha a jelenlegi régióban több felügyelt példány van szüksége. Alternatív megoldásként a támogatási kérések elküldése nélkül létrehozhat új felügyelt példányok egy másik Azure-régióban.

> [!IMPORTANT]
> A telepítések megtervezésekor vegye figyelembe, hogy a kritikus fontosságú üzleti (BC) példány (miatt hozzáadott redundancia) általában felhasznál a nagyobb kapacitást, mint egy általános célú (GP) példány x 4. Igen, a számítások, 1 a csoportházirend-példány = 1 példány egység és 1 BC példány = 4 példány egység. Egyszerűsítése érdekében a felhasználási elemzés, szemben az alapértelmezés szerinti korlátozásoknak, a példány egységek összesítése a régióban, ahol felügyelt példányok üzembe helyezése összes alhálózat között, valamint az eredményeket hasonlítsa össze az előfizetés-típus példánykorlátok egység.

### <a name="deployment-options-for-gp-and-bc-deployments-within-the-same-subnet"></a>Telepítési lehetőségek a csoportházirend és a BC ugyanazon az alhálózaton belül

A következő példákban üzembe helyezési esetekre, nem üres alhálózattal, és vegyes GP és a BC szolgáltatásszintek.

|Alhálózatok száma|1. alhálózata|2. alhálózata|Alhálózat 3|
|:---|:---|:---|:---|
|1|0 BC és legfeljebb 12 GP<br>1 BC és legfeljebb 8 GP<br>2 BC és akár 4 általános védelmi<br>3 BC|–| –|
|2|0 BC, akár 4 általános védelmi|0 BC, legfeljebb 8 GP<br>1 BC, akár 4 általános védelmi<br>2 BC|–|
|2|1 BC|0 BC, legfeljebb 8 GP<br>1 BC, akár 4 általános védelmi<br>2 BC|–|
|2|2 BC|0 BC, legfeljebb 8 GP<br>1 BC, akár 4 általános védelmi<br>2 BC|–|
|3|BC 1, 0 A CSOPORTHÁZIREND|BC 1, 0 A CSOPORTHÁZIREND|0 BC, akár 4 általános védelmi|
|3|1BC, 0 A CSOPORTHÁZIREND|0 BC, akár 4 általános védelmi|BC 1, 0 A CSOPORTHÁZIREND|
|3|0 BC, akár 4 általános védelmi|BC 1, 0 A CSOPORTHÁZIREND|1BC, 0 A CSOPORTHÁZIREND|

### <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL felügyelt példánya a nagyobb kvótát beszerzése

A folyamat lehet beszerezni a nagyobb kvótát kezdeményezéséhez:

1. Nyissa meg **súgó + támogatás**, és kattintson a **új támogatási kérelem**. 

   ![Súgó és támogatás](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Az alapismeretek lapon az új támogatási kérelem:
   - A **Problématípus**válassza **szolgáltatás és az előfizetések korlátai (kvóták)**.
   - Az **Előfizetés** beállításnál válassza ki az előfizetését.
   - A **kvótatípus**válassza **SQL Database felügyelt példányain**.
   - A **támogatási csomag**, válassza ki a támogatási csomagot.

     ![Probléma típusa kvóta](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Kattintson a **Tovább** gombra.
4. A probléma lapon az új támogatási kérelem:
   - A **súlyossági**, válassza ki a súlyossági szintet a problémát.
   - A **részletek**, adjon meg további információt a problémáról, beleértve a hibaüzeneteket is.
   - A **fájlfeltöltés**, és azok bővebb fájl csatolása (legfeljebb 4 MB).

     ![Probléma részletei](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Egy érvényes kérelmet kell tartalmaznia:
     > - Régió, hogy melyik előfizetéssel korlátot növelni kell
     > - Példányok száma a szolgáltatási rétegben lévő alhálózatok a kvóta növelése után szükséges száma (ha az egyik létező alhálózathoz ki kell bővíteni kell növelése
     > - Szükséges új alhálózatok számát és a példányok száma a szolgáltatási szinten belül az új alhálózatokra teljes száma (ha üzembe kell helyeznie az új alhálózatokra felügyelt példányok).
5. Kattintson a **Tovább** gombra.
6. Adja meg a kapcsolattartási adatai lap az új támogatási kérelmet az elsődleges kapcsolattartási módszert (e-mail cím vagy telefonszám) és a kapcsolattartási adatait.
7. Kattintson a **Create** (Létrehozás) gombra.

## <a name="next-steps"></a>További lépések

- Felügyelt példánnyal kapcsolatos további információkért lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md). 
- Díjszabási információkért tekintse meg a [SQL Database felügyelt példányain díjszabás](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Ismerje meg, hogyan hozhat létre az első felügyelt példányhoz, lásd: [gyors üzembe helyezési útmutató](sql-database-managed-instance-get-started.md).
