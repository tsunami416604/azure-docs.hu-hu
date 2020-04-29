---
title: A kérelmek kvótájának növelése és támogatás kérése
description: Támogatási kérelem létrehozása az Azure szinapszis Analytics Azure Portaljában. A kérelmek kvótájának növelése vagy a probléma megoldására vonatkozó támogatás beszerzése.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80350902"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>A kérelmek kvótájának növekedése és az Azure szinapszis Analytics támogatásának beszerzése

Ez a cikk azt ismerteti, hogyan lehet támogatási jegyet beküldeni az Azure szinapszis Analytics Azure Portal. Ez a folyamat lehetővé teszi a kvóta növelését vagy technikai támogatási kérés beküldését a mérnöki támogatási csapat számára.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Az alábbi lépéseket követve hozzon létre egy új támogatási kérést az Azure szinapszis Analytics Azure Portalból.

1. A [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A Súgó + támogatás hivatkozása](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. A **Súgó és támogatás**területen válassza az **új támogatási kérelem**lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Tekintse át az [Azure-támogatási csomagot](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **hibajavítási** támogatás a [fejlesztői](https://azure.microsoft.com/support/plans/developer/), a standard, a [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)vagy a [Premier](https://azure.microsoft.com/support/plans/premier/) [szintű](https://azure.microsoft.com/support/plans/standard/)támogatáson keresztül érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatások** a [közvetlen professzionális](https://azure.microsoft.com/support/plans/prodirect/) támogatás és a [Premier](https://azure.microsoft.com/support/plans/premier/) szintű támogatási szinteken érhetők el.

   Premier szintű támogatási csomaggal a [Microsoft Premier online portálon](https://premier.microsoft.com/)is jelentheti az Azure szinapszis Analytics szolgáltatással kapcsolatos problémákat. Tekintse meg az [Azure-támogatási terveket](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) a különböző támogatási csomagokról, beleértve a hatókört, a válaszidőt, a díjszabást stb.  Az Azure-támogatással kapcsolatos gyakori kérdésekért tekintse meg az [Azure-támogatással](https://azure.microsoft.com/support/faq/)kapcsolatos gyakori kérdéseket.

1. A **probléma típusa**mezőben válassza ki a megfelelő típusú problémát. A hibajavítási problémák esetén válassza a **technikai**lehetőséget. A kvóta-növelési kérelmek esetében válassza a **szolgáltatás-és előfizetési korlátok (kvóták)** lehetőséget.

   ![Probléma típusának kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Ez a cikk a kvóta növelésére vonatkozó főként. A probléma megoldásához szükséges támogatási kérelmek esetében azonban a **technikai** lehetőséget is kiválaszthatja. Ha a **technikai**lehetőséget választja, a rendszer megkéri, hogy adjon meg egy összefoglalást, majd azonosítsa a probléma típusát a **probléma típusának**kiválasztása lehetőség kiválasztásával. Előfordulhat, hogy a probléma megoldásához megoldásokat talál. Ha a bemutatott megoldások nem oldják meg a problémát, válassza a **Next (tovább** ) gombot, és fejezze be az űrlapot a támogatási jegy elküldéséhez.

1. A kvóta-növelési kérelmek esetében válassza az **Azure szinapszis Analytics** lehetőséget a **kvóta típusához**. Ezután válassza a **Tovább: megoldások >>** elemet.

   ![Adja meg a kvóta típusát](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. A **részletek** ablakban válassza a **részletek** megadása lehetőséget a további információk megadásához.

   ![A "részletek megadása" hivatkozás](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Kvóta-kérelmek típusai

A **részletek megadása** lehetőségre kattintva megjelenik a **kvóta részletei** ablak, amely lehetővé teszi további információk hozzáadását. A következő szakaszok ismertetik az Azure szinapszis Analytics szolgáltatáshoz elérhető különböző kvóták kéréseit.

### <a name="data-warehouse-units-dwus-per-server"></a>Adatraktár-egységek (DWU-EK) kiszolgálónkénti bontásban

A következő lépésekkel növelheti a DWU egy kiszolgálón.

1. Válassza ki az **adatraktár-egységek (DTU) számát a kiszolgálói** kvóta típusa mezőben.

1. Az **erőforrás** listában válassza ki a célként használni kívánt erőforrást.

1. A **kérelem kvóta** mezőjébe írja be a kért új DWU-korlátot.

   ![DWU-kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Kiszolgálók/előfizetés

A következő lépések végrehajtásával növelheti a kiszolgálók számát az előfizetésben.

1. Válassza ki a **kiszolgálók/előfizetés** típusú kvótát.

1. A **hely** listában válassza ki a használni kívánt Azure-régiót. Az egyes régiókban a kvóta/előfizetés.

1. Az **új kvóta** mezőben adja meg az adott régióban található kiszolgálók maximális számára vonatkozó kérelmét.

   ![Kiszolgálók kvótájának részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Egyes ajánlati típusok nem érhetők el minden régióban. Előfordulhat, hogy a következő hibaüzenet jelenik meg:

`This location is not available for subscription`

Ha az előfizetés egy adott régióban fér hozzá, akkor a hozzáférés kéréséhez használja az **egyéb kvóta kérése** lehetőséget. A kérelemben adja meg az ajánlat és az SKU adatait, amelyeket engedélyezni szeretne a régió számára. Az ajánlat és az SKU lehetőségeinek megismeréséhez lásd: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Egyéb kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Kérelem elküldése

Az utolsó lépés a SQL Database támogatási kérelem hátralévő részleteinek kitöltése. Ezután válassza a **Tovább: Áttekintés +>>létrehozása **elemet, majd a kérelem részleteinek áttekintése után kattintson a **Létrehozás** gombra a kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése

A támogatási kérelem elküldését követően az Azure-támogatási csapat kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Minden támogatási kérelem** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Egyéb erőforrások

Az Azure szinapszis Analytics-Közösséggel [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) vagy a [Azure SQL Data Warehouse MSDN-fórumon](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)keresztül is csatlakozhat.

