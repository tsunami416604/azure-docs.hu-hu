---
title: Kvóta növelésének kérése
description: Ez az oldal azt ismerteti, hogyan hozhat létre támogatási kérést, hogy növelje a kvótákat Azure SQL Database önálló adatbázisok, kiszolgálók és felügyelt példányok esetében.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586155"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>A kérelmek kvótájának növekedése Azure SQL Database

Ez a cikk az önálló adatbázisok, kiszolgálók és felügyelt példányok Azure SQL Database kvóta-növelésének igénylését ismerteti. Azt is ismerteti, hogyan engedélyezhető az előfizetés-hozzáférés egy adott régióhoz.

## <a id="newquota"></a>Új támogatási kérelem létrehozása

A következő lépésekkel hozzon létre egy új támogatási kérést a SQL Database Azure Portal.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A Súgó + támogatás hivatkozása](./media/quota-increase-request/help-plus-support.png)

1. A **Súgó és támogatás**területen válassza az **új támogatási kérelem**lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/quota-increase-request/new-support-request.png)

1. A **probléma típusa**beállításnál válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/quota-increase-request/select-quota-issue-type.png)

1. Az **előfizetés**mezőben válassza ki azt az előfizetést, amelynek a kvótáját emelni szeretné.

   ![Válassza ki az előfizetést egy nagyobb kvótához](./media/quota-increase-request/select-subscription-support-request.png)

1. A **kvóta típusa**beállításnál válasszon a következő típusú kvóták közül:

   - **SQL Database** az önálló adatbázisok és a rugalmas készlet kvótái számára.
   - Felügyelt példányok **SQL Database felügyelt példánya** .

   Ezután válassza a **következő: megoldások > >** .

   ![Adja meg a kvóta típusát](./media/quota-increase-request/select-quota-type.png)

1. A **részletek** ablakban válassza a **részletek** megadása lehetőséget a további információk megadásához.

   ![A "részletek megadása" hivatkozás](./media/quota-increase-request/provide-details-link.png)

A **részletek megadása** lehetőségre kattintva megjelenik a **kvóta részletei** ablak, amely lehetővé teszi további információk hozzáadását. A következő szakaszok a **felügyelt példányok** **SQL Database** és SQL Database különböző lehetőségeit ismertetik.

## <a id="sqldbquota"></a>SQL Database kvóta típusai

A következő szakaszok három kvóta-növelési lehetőséget ismertetnek a **SQL Database** kvóták típusaihoz:

- Adatbázis-tranzakciós egységek (DTU) kiszolgálónként
- Kiszolgálók/előfizetés
- Előfizetés-hozzáférés engedélyezése egy régióhoz

### <a name="database-transaction-units-dtus-per-server"></a>Adatbázis-tranzakciós egységek (DTU) kiszolgálónként

A következő lépésekkel növelheti a DTU egy kiszolgálón.

1. Válassza ki az **adatbázis-tranzakciós egységeket (DTU) a kiszolgálói** kvóta típusa mezőben.

1. Az **erőforrás** listában válassza ki a célként használni kívánt erőforrást.

1. Az **új kvóta** mezőben adja meg a kért új DTU-korlátot.

   ![DTU-kvóta részletei](./media/quota-increase-request/quota-details-dtus.png)

További információ: az [önálló adatbázisok erőforrás-korlátai a DTU beszerzési modell](sql-database-dtu-resource-limits-single-databases.md) és az [erőforrások korlátai a rugalmas készletekhez a DTU beszerzési modell használatával](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Kiszolgálók/előfizetés

A következő lépések végrehajtásával növelheti a kiszolgálók számát az előfizetésben.

1. Válassza ki a **kiszolgálók/előfizetés** típusú kvótát.

1. A **hely** listában válassza ki a használni kívánt Azure-régiót. Az egyes régiókban a kvóta/előfizetés.

1. Az **új kvóta** mezőben adja meg az adott régióban található kiszolgálók maximális számára vonatkozó kérelmét.

   ![Kiszolgálók kvótájának részletei](./media/quota-increase-request/quota-details-servers.png)

További információ: [SQL Database erőforrás-korlátozások és erőforrás-szabályozás](sql-database-resource-limits-database-server.md).

### <a id="other"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Egyes ajánlati típusok nem érhetők el minden régióban. Előfordulhat, hogy a következő hibaüzenet jelenik meg:

`This location is not available for subscription`

Ha az előfizetés egy adott régióban fér hozzá, akkor a hozzáférés kéréséhez használja az **egyéb kvóta kérése** lehetőséget. A kérelemben adja meg az ajánlat és az SKU adatait, amelyeket engedélyezni szeretne a régió számára. Az ajánlat és az SKU beállításainak megismeréséhez tekintse meg a [Azure SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Egyéb kvóta részletei](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>Felügyelt példányok kvóta típusa

A **SQL Server felügyelt példány** kvótájának típusa a következő lépésekkel végezhető el:

1. A **régió** listáról válassza ki a célként használni kívánt Azure-régiót.

1. Adja meg az **alhálózat** és a **virtuális mag**számára kért új korlátozásokat.

   ![Felügyelt példány kvótájának részletei](./media/quota-increase-request/quota-details-managed-instance.png)

További információ: a [felügyelt példányok erőforrás-korlátainak áttekintése Azure SQL Database](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Kérelem elküldése

Az utolsó lépés az SQL Database kvóta-kérelem hátralévő részleteinek kitöltése. Ezután válassza a **Tovább: Áttekintés + > > létrehozása**elemet, majd a kérelem részleteinek áttekintése után kattintson a **Létrehozás** gombra a kérelem elküldéséhez.

## <a name="next-steps"></a>Következő lépések

A kérés elküldése után a rendszer áttekinti a kérelmet. Az űrlapon megadott információk alapján választ kaphat a rendszer.

Az egyéb Azure-korlátokkal kapcsolatos további információkért lásd az [Azure-előfizetések és-szolgáltatások korlátozásait, kvótáit és korlátozásait](../azure-resource-manager/management/azure-subscription-service-limits.md)ismertető témakört.
