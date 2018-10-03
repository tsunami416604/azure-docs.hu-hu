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
ms.date: 10/02/2018
ms.openlocfilehash: f2b1a8e18917c1c045c715bd3424d0bbfc0cdc67
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045383"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Áttekintés az Azure SQL Database felügyelt példányain erőforráskorlátok

Ez a cikk az Azure SQL Database felügyelt példányába erőforráskorlátok áttekintést nyújt, és ismerteti, hogyan hozhat létre az alapértelmezett előfizetési korlátozásait növelésére. 

> [!NOTE]
> Más felügyelt példány-korlátozások nem kifejezetten az adott előfizetést, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-managed-instance.md#vcore-based-purchasing-model) és [felügyelt példány szolgáltatásszintek](sql-database-managed-instance.md#managed-instance-service-tiers).

## <a name="default-subscription-level-limits-per-region"></a>Alapértelmezett előfizetés-szintű korlátok régiónként

Felügyelt példány üzembe helyezési jelenleg csak a következő típusú előfizetések a használatát támogatja:

- [Nagyvállalati Szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Felhőszolgáltató (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> Ez a korlátozás a támogatás csak bizonyos típusú előfizetésessel csak átmenetileg létezik.

Felügyelt példányok két alapértelmezett előfizetés-szintű vonatkozó korlátok Azure-régióba tartozik. Másik előfizetés-típusok különböző regionális korlátokkal rendelkeznek. Ezek a korlátok növelhető az Azure Portalon, az előfizetés típusú speciális támogatási kérés létrehozásával **kvóta**:

- **Alhálózathoz megadott korlátot**: alhálózatok, telepítve vannak a felügyelt példányok maximális száma
- **A maximális szám példány**: régiónként példányok maximális száma

> [!IMPORTANT]
> A telepítések megtervezésekor vegye figyelembe, hogy a kritikus fontosságú üzleti (BC) példány (miatt hozzáadott redundancia) általában felhasznál a nagyobb kapacitást, mint egy általános célú (GP) példány x 4. Igen, a számítások, 1 a csoportházirend-példány = 1 példány egység és 1 BC példány = 4 példány egység. Egyszerűsítése érdekében a felhasználási elemzés, szemben az alapértelmezés szerinti korlátozásoknak, a példány egységek összesítése a régióban, ahol felügyelt példányok üzembe helyezése összes alhálózat között, valamint az eredményeket hasonlítsa össze az előfizetés-típus példánykorlátok egység.

## <a name="default-limits-by-subscription-type"></a>Alapértelmezett korlátai szerint az előfizetés-típus

|Előfizetés típusa| Felügyelt példány alhálózatok maximális száma | Példányok maximális száma |Maximális száma a csoportházirend által felügyelt példány *|BC maximális száma a felügyelt példány *|
| :---| :--- | :--- |:--- |:--- |
|Utólagos, használatalapú fizetés|1 *|4 *|4 *|1 *|
|CSP |1 *|4 *|4 *|1 *|
|Nagyvállalati szerződés|3 **|12 **|12 **|3 **|

\* Vagy telepítheti 1 BC vagy az egyik alhálózat 4 GP-példány, hogy a régióban "példány egységek" száma soha nem meghaladja a 4.

** A példányok egy szolgáltatási rétegben található maximális száma vonatkozik, ha nincsenek példányok egy másik szolgáltatási rétegben található. Abban az esetben, ha azt tervezi, a csoportházirend és BC példányok ugyanazon az alhálózaton belül vegyesen, használja a következő szakasz referenciaként engedélyezett kombinációját. Egyszerű szabály alhálózatok száma nem haladhatja meg a 3, és a példány egységek száma nem haladhatja meg a 12.

## <a name="deployment-options-for-gp-and-bc-deployments-within-the-same-subnet"></a>Telepítési lehetőségek a csoportházirend és a BC ugyanazon az alhálózaton belül

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

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>SQL felügyelt példánya a nagyobb kvótát beszerzése

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