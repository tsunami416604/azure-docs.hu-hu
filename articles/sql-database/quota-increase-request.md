---
title: Kvótanövelés kérése
description: Ez a lap bemutatja, hogyan hozhat létre támogatási kérelmet az Azure SQL Database egyetlen adatbázisok, kiszolgálók és felügyelt példányok kvótáinak növeléséhez.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586155"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Az Azure SQL Database kérelemkvóta-növelése

Ez a cikk bemutatja, hogyan kérhet kvótanövelést az Azure SQL Database-hez egyetlen adatbázisokhoz, kiszolgálókhoz és felügyelt példányokhoz. Azt is ismerteti, hogyan engedélyezheti az előfizetés-hozzáférést egy régióhoz.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Új támogatási kérelem létrehozása

Az alábbi lépésekkel hozzon létre egy új támogatási kérelmet az Azure Portal sql-adatbázisból.

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A Súgó + támogatás hivatkozás](./media/quota-increase-request/help-plus-support.png)

1. A **Súgó + támogatás**csoportban válassza az Új támogatási **kérelem**lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/quota-increase-request/new-support-request.png)

1. A **Probléma típusmezőben**válassza a **Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

   ![Problématípus kiválasztása](./media/quota-increase-request/select-quota-issue-type.png)

1. **Előfizetés esetén**válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![Előfizetés kiválasztása megnövelt kvótához](./media/quota-increase-request/select-subscription-support-request.png)

1. Kvótatípus **esetén**válasszon az alábbi kvótatípusok közül:

   - **SQL-adatbázis** egyetlen adatbázishoz és rugalmas készletkvótákhoz.
   - **SQL Database felügyelt példány** a felügyelt példányok.

   Ezután válassza **a Tovább: Megoldások >>** lehetőséget.

   ![Kvótatípus kiválasztása](./media/quota-increase-request/select-quota-type.png)

1. A **Részletek** ablakban válassza a **Részletek megadása** lehetőséget a további információk megadásához.

   ![A "Részletek biztosítása" link](./media/quota-increase-request/provide-details-link.png)

A **Részletek megadásával** **gombra** kattintva megjelenik a Kvóta részletei ablak, amely további információk hozzáadását teszi lehetővé. A következő szakaszok az **SQL Database** és az SQL Database Managed Instance kvótatípusok különböző beállításait **ismertetik.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL-adatbázis kvótatípusai

A következő szakaszok az **SQL Database** kvótatípusok három kvótanövelési beállítását ismertetik:

- Adatbázistranzakció-egységek (DTUs) kiszolgálónként
- Kiszolgálók előfizetésenként
- Előfizetés-hozzáférés engedélyezése egy régióhoz

### <a name="database-transaction-units-dtus-per-server"></a>Adatbázistranzakció-egységek (DTUs) kiszolgálónként

Az alábbi lépésekkel kérheti a kiszolgálónkénti DIT-ek növelését.

1. Válassza ki az **adatbázis-tranzakcióegységeket (DDO-k) kiszolgálókvóta-típusonként.**

1. Az **Erőforrás** listában jelölje ki a megcélozandó erőforrást.

1. Az **Új kvóta** mezőbe írja be a kért új DTU-korlátot.

   ![DTU kvóta részletei](./media/quota-increase-request/quota-details-dtus.png)

További információ: [Erőforrás-korlátok egyetlen adatbázisok a DTU beszerzési modell](sql-database-dtu-resource-limits-single-databases.md) és [az erőforrások korlátok rugalmas készletek a DTU beszerzési modell használatával.](sql-database-dtu-resource-limits-elastic-pools.md)

### <a name="servers-per-subscription"></a>Kiszolgálók előfizetésenként

Az alábbi lépésekkel kérheti a kiszolgálók előfizetésenkénti számának növelését.

1. Válassza ki a **Kiszolgálók előfizetési kvótatípusonkénti** kiválasztását.

1. A **Hely** listában válassza ki a használni kívánt Azure-régiót. A kvóta előfizetésenként az egyes régiókban.

1. Az **Új kvóta** mezőbe írja be az adott régiókiszolgálók maximális számára vonatkozó kérelmet.

   ![Kiszolgálókvóta részletei](./media/quota-increase-request/quota-details-servers.png)

További információt az [SQL Database erőforráskorlátai és erőforrás-szabályozása](sql-database-resource-limits-database-server.md)című témakörben talál.

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Egyes ajánlattípusok nem érhetők el minden régióban. A következőhez hasonló hibaüzenet jelenhet meg:

`This location is not available for subscription`

Ha az előfizetésnek hozzáférésre van szüksége egy adott régióban, használja az **Egyéb kvótakérelem lehetőséget** a hozzáférés kéréséhez. A kérelemben adja meg az ajánlat és a termékváltozat adatait, amelyeket engedélyezni szeretne a régióban. Az ajánlat és a termékváltozat beállításainak megismeréséhez tekintse meg az [Azure SQL Database díjszabását.](https://azure.microsoft.com/pricing/details/sql-database/single/)

![Egyéb kontingensadatok](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Felügyelt példánykvóta típusa

Az **SQL Server felügyelt példánykvóta** típusához kövesse az alábbi lépéseket:

1. A **régió** listában válassza ki a megcélozni kívánt Azure-régiót.

1. Adja meg az **Alhálózat** és a **virtuális mag**számára kért új korlátokat.

   ![Felügyelt példánykvóta részletei](./media/quota-increase-request/quota-details-managed-instance.png)

További információt az [Áttekintés az Azure SQL Database felügyelt példányerőforrás-korlátai](sql-database-managed-instance-resource-limits.md)című témakörben talál.

## <a name="submit-your-request"></a>Küldje el kérését

Az utolsó lépés az SQL Database kvótakérelem további részleteinek kitöltése. Ezután válassza **a Tovább: Véleményezés + létrehozás>>** lehetőséget, és a kérelem részleteinek áttekintése után kattintson a **Létrehozás** gombra a kérelem elküldéséhez.

## <a name="next-steps"></a>További lépések

Miután elküldte a kérelmet, azt felülvizsgáljuk. Az űrlapon megadott információk alapján választ kapunk Önnel.

Az azure-korlátozásokról további információt az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
