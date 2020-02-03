---
title: Támogatási jegy létrehozása
description: Támogatási jegy létrehozása az SQL Data Warehouse-hoz.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717838"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Támogatási jegy létrehozása az SQL Data Warehouse-hoz
Ha problémája merül fel a SQL Data Warehouseával kapcsolatban, hozzon létre egy támogatási jegyet, hogy a mérnöki támogatási csapat segítséget nyújtson Önnek.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása
1. Nyissa meg az [Azure Portal](https://portal.azure.com/).
2. A kezdőképernyőn kattintson a **Súgó és támogatás** fülre.
   
    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. A Súgó és támogatás panelen kattintson az **Új támogatási kérelem** elemre, és töltse ki az **Alapvető beállítások** panelt.

   Jelölje ki az [Azure támogatási csomagot](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **hibajavítási** támogatás a [fejlesztői](https://azure.microsoft.com/support/plans/developer/), a standard, a [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)vagy a [Premier](https://azure.microsoft.com/support/plans/premier/) [szintű](https://azure.microsoft.com/support/plans/standard/)támogatáson keresztül érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatás**[Közvetlen professzionális támogatás](https://azure.microsoft.com/support/plans/prodirect/) és [Premier](https://azure.microsoft.com/support/plans/premier/) esetén érhető el. 
     
     Premier szintű támogatás megléte esetén az SQL Data Warehouse-szal kapcsolatos problémákat is jelentheti a [Microsoft Premier online portálon](https://premier.microsoft.com/). Tekintse meg az [Azure-támogatási terveket](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) a különböző támogatási csomagokról, beleértve a hatókört, a válaszidőt, a díjszabást stb.  Az Azure-támogatással kapcsolatos gyakori kérdésekért tekintse meg az [Azure-támogatással](https://azure.microsoft.com/support/faq/)kapcsolatos gyakori kérdéseket.  
        
     ![Alapvető beállítások panel](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Alapvető beállítások panel1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Töltse ki a **Probléma** panelt.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy 45 000 egységnyi **DTU-kvótával**. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát egy támogatási jegy létrehozásával, és a kérelem típusaként a *Kvóta* kiválasztásával növelheti meg. A DTU-szükségletek kiszámításához szorozza meg a 7,5-et az összes szükséges [DWU](sql-data-warehouse-overview-what-is.md) . Ha például két DW6000-et szeretne üzemeltetni egy SQL-kiszolgálón, akkor 90 000 egységnyi DTU-kvótát kell igényelnie.  Az aktuális DTU-felhasználást az SQL-kiszolgáló panelen tekintheti meg a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. 
   > 
   > 
   
5. Töltse ki a **kapcsolattartási adatait**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése
A támogatási kérelem elküldését követően az Azure-támogatási csapat kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Minden támogatási kérelem** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Egyéb erőforrások
A SQL Data Warehouse Közösséggel is csatlakozhat [stack overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) vagy a [Azure SQL Data Warehouse MSDN fórumán](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)keresztül.

 
