---
title: Támogatási jegy létrehozása
description: Támogatási jegy létrehozása az Azure szinapszis Analytics szolgáltatásban.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195683"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Támogatási jegy létrehozása az Azure szinapszis Analytics szolgáltatáshoz
Ha problémák merülnek fel az Azure szinapszis Analytics segítségével, hozzon létre egy támogatási jegyet, hogy a mérnöki támogatási csapat segítséget nyújtson Önnek.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása
1. Nyissa meg az [Azure Portal](https://portal.azure.com/).
1. A kezdőképernyőn kattintson a **Súgó és támogatás** fülre.
   
    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Tekintse át az [Azure-támogatási csomagot](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **hibajavítási** támogatás a [fejlesztői](https://azure.microsoft.com/support/plans/developer/), a standard, a [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)vagy a [Premier](https://azure.microsoft.com/support/plans/premier/) [szintű](https://azure.microsoft.com/support/plans/standard/)támogatáson keresztül érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatás**[Közvetlen professzionális támogatás](https://azure.microsoft.com/support/plans/prodirect/) és [Premier](https://azure.microsoft.com/support/plans/premier/) esetén érhető el. 
     
     Premier szintű támogatás megléte esetén az SQL Data Warehouse-szal kapcsolatos problémákat is jelentheti a [Microsoft Premier online portálon](https://premier.microsoft.com/). Tekintse meg az [Azure-támogatási terveket](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) a különböző támogatási csomagokról, beleértve a hatókört, a válaszidőt, a díjszabást stb.  Az Azure-támogatással kapcsolatos gyakori kérdésekért tekintse meg az [Azure-támogatással](https://azure.microsoft.com/support/faq/)kapcsolatos gyakori kérdéseket.
1. A **Súgó + támogatás** lapon válassza az **új támogatási kérelem**lehetőséget. Válassza ki a probléma típusát a legördülő menüből. Ezután folytassa az információk kitöltését az **alapvető beállítások** lapon. adja meg a probléma **összegzését** , majd válasszon ki egy **problémát** a menüből, majd válassza a mentés lehetőséget.

    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy 45 000 egységnyi **DTU-kvótával**. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát egy támogatási jegy létrehozásával, és a kérelem típusaként a *Kvóta* kiválasztásával növelheti meg. A DTU-szükségletek kiszámításához szorozza meg a 7,5-et az összes szükséges [DWU](sql-data-warehouse-overview-what-is.md) . Ha például két DW6000-et szeretne üzemeltetni egy SQL-kiszolgálón, akkor 90 000 egységnyi DTU-kvótát kell igényelnie.  Az aktuális DTU-felhasználást az SQL-kiszolgáló panelen tekintheti meg a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. 
   > 

1. Előfordulhat, hogy a probléma megoldásához megoldásokat talál. Ha a bemutatott megoldások nem oldják meg a problémát, válassza a **következő: részletek**lehetőséget. Küldje el a probléma részleteit és a kapcsolattartási adatait. Válassza a Next (tovább) lehetőséget **: Tekintse át a +** 
![részleteit](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Tekintse át az adatokat, és válassza a **Létrehozás** lehetőséget az űrlap alján a támogatási kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése
A támogatási kérelem elküldését követően az Azure-támogatási csapat kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Minden támogatási kérelem** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Egyéb erőforrások
A SQL Data Warehouse Közösséggel is csatlakozhat [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) vagy a [Azure SQL Data Warehouse MSDN fórumán](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)keresztül.

 
