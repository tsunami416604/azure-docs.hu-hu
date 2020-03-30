---
title: Kvótanövelés kérése és támogatás kérése
description: Támogatási kérelem létrehozása az Azure Synapse Analytics Azure-portálon. A kvótakérés növeli vagy problémamegoldási támogatást kap.
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
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350902"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Kvótanövelés kérése és támogatás kérése az Azure Synapse Analytics szolgáltatáshoz

Ez a cikk ismerteti, hogyan küldhet el egy támogatási jegyet az Azure Synapse Analytics Azure-portálon. Ez a folyamat lehetővé teszi, hogy kvótanövelést kérjen, vagy technikai támogatási kérelmet nyújtson be a mérnöki támogatási csapatszámára.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása

Az alábbi lépésekkel hozzon létre egy új támogatási kérelmet az Azure Synapse Analytics Azure-portálról.

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A Súgó + támogatás hivatkozás](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. A **Súgó + támogatás**csoportban válassza az Új támogatási **kérelem**lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Tekintse át az [Azure támogatási csomagját.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * **A break-fix** támogatást [a Fejlesztő,](https://azure.microsoft.com/support/plans/developer/) [a Standard,](https://azure.microsoft.com/support/plans/standard/) [a Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)vagy [a Premier](https://azure.microsoft.com/support/plans/premier/) támogatás biztosítja. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * **A fejlesztői mentorálási** és **tanácsadási szolgáltatások** a [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) és a [Premier](https://azure.microsoft.com/support/plans/premier/) támogatási szinteken érhetők el.

   Ha rendelkezik premier támogatási csomaggal, jelentheti az Azure Synapse Analytics-problémákat a [Microsoft Premier online portálon](https://premier.microsoft.com/)is. Tekintse meg az [Azure támogatási csomagok,](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) hogy többet tudjon meg a különböző támogatási csomagok, beleértve a hatókör, válaszidők, díjszabás, stb.  Az Azure-támogatással kapcsolatos gyakori kérdésekről az [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/)között talál.

1. A **Problématípus mezőben**válassza ki a megfelelő problématípust. A hibakioldási problémákhoz válassza a **Technical**lehetőséget. A kvótanövelési kérelmekhez válassza **a Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

   ![Problématípus kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Ez a cikk a kvótanövelési kérelmekre összpontosít. A problémamegoldási támogatási kérelmekhez azonban itt a **Műszaki** lehetőséget is kiválaszthatja. Ha a Technical lehetőséget **választja,** a rendszer összefoglalót kell adnia, majd azonosítania kell a problématípust a **Problématípus kiválasztása**lehetőség kiválasztásával. Előfordulhat, hogy megoldásokat talál a probléma megoldására. Ha a bemutatott megoldások nem oldják meg a problémát, válassza a **Tovább: Részletek** lehetőséget, és töltse ki az űrlapot a támogatási jegy elküldéséhez.

1. A kvótanövelési kérelmekhez válassza az **Azure Synapse Analytics** lehetőséget a **kvótatípushoz.** Ezután válassza **a Tovább: Megoldások >>** lehetőséget.

   ![Kvótatípus kiválasztása](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. A **Részletek** ablakban válassza a **Részletek megadása** lehetőséget a további információk megadásához.

   ![A "Részletek biztosítása" link](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Kvótakérelem-típusok

A **Részletek megadásával** **gombra** kattintva megjelenik a Kvóta részletei ablak, amely további információk hozzáadását teszi lehetővé. A következő szakaszok ismertetik az Azure Synapse Analytics számára elérhető különböző kvótakérelmeket.

### <a name="data-warehouse-units-dwus-per-server"></a>Adattárház egységek (DWUs) kiszolgálónként

Az alábbi lépésekkel kérheti a kiszolgálónkénti DWUs-ok növelését.

1. Válassza ki az **adatraktáregységeket (DT-k) kiszolgálókvóta-típusonként.**

1. Az **Erőforrás** listában jelölje ki a megcélozandó erőforrást.

1. A **Kvótakérése mezőben** adja meg a kért új DWU-korlátot.

   ![DWU kvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Kiszolgálók előfizetésenként

Az alábbi lépésekkel kérheti a kiszolgálók előfizetésenkénti számának növelését.

1. Válassza ki a **Kiszolgálók előfizetési kvótatípusonkénti** kiválasztását.

1. A **Hely** listában válassza ki a használni kívánt Azure-régiót. A kvóta előfizetésenként az egyes régiókban.

1. Az **Új kvóta** mezőbe írja be az adott régiókiszolgálók maximális számára vonatkozó kérelmet.

   ![Kiszolgálókvóta részletei](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Előfizetés-hozzáférés engedélyezése egy régióhoz

Egyes ajánlattípusok nem érhetők el minden régióban. A következőhez hasonló hibaüzenet jelenhet meg:

`This location is not available for subscription`

Ha az előfizetésnek hozzáférésre van szüksége egy adott régióban, használja az **Egyéb kvótakérelem lehetőséget** a hozzáférés kéréséhez. A kérelemben adja meg az ajánlat és a termékváltozat adatait, amelyeket engedélyezni szeretne a régióban. Az ajánlatés a termékváltozat beállításainak megismeréséhez tekintse meg az [Azure Synapse Analytics díjszabását.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

![Egyéb kontingensadatok](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Küldje el kérését

Az utolsó lépés az SQL Database támogatási kérelem fennmaradó részleteinek kitöltése. Ezután válassza **a Tovább: Véleményezés + létrehozás>>** lehetőséget, és a kérelem részleteinek áttekintése után kattintson a **Létrehozás** gombra a kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése

Miután elküldte a támogatási kérelmet, az Azure támogatási csapata felveszi Önnel a kapcsolatot. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Minden támogatási kérelem** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Egyéb erőforrások

Az Azure Synapse Analytics közösséghez is csatlakozhat [a veremtúlcsordulás kori túlcsorduláskor,](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) vagy az [Azure SQL Data Warehouse MSDN fórumon](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)keresztül.

