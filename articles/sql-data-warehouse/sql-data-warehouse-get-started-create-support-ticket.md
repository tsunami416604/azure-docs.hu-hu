---
title: Támogatási jegy létrehozása az Azure SQL Data Warehouse |} A Microsoft Docs
description: Támogatási jegy létrehozása az SQL Data Warehouse-hoz.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 12b12ff6a6dc616ec3bb592f54862535b1318e49
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247317"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Támogatási jegy létrehozása az SQL Data Warehouse-hoz
Ha bármilyen probléma merül fel az SQL Data Warehouse-szal kapcsolatban, hozzon létre egy támogatási jegyet, hogy mérnöki támogató csapatunk a segítségére lehessen.

## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása
1. Nyissa meg az [Azure Portalt][Azure portal].
2. A kezdőképernyőn kattintson a **Súgó és támogatás** fülre.
   
    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. A Súgó és támogatás panelen kattintson az **Új támogatási kérelem** elemre, és töltse ki az **Alapvető beállítások** panelt.

   Jelölje ki az [Azure támogatási csomagot][Azure support plan].
   
   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **javítás/csere** támogatás a [Fejlesztői][Developer], a [Standard][Standard], a [Közvetlen professzionális támogatás][Professional Direct] vagy a [Premier szintű támogatás][Premier] szinten érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatás** [Közvetlen professzionális támogatás][Professional Direct] és [Premier szintű támogatás][Premier] esetén érhető el. 
     
     Premier szintű támogatás megléte esetén az SQL Data Warehouse-szal kapcsolatos problémákat is jelentheti a [Microsoft Premier online portálon][Microsoft Premier online portal].  Az [Azure-támogatás ügyfeleknek][Azure support plan] című témakör részletesen bemutatja a támogatási csomagokat, beleértve azok hatókörét, a válaszidőt, a díjszabást és egyéb információkat.  Az Azure-támogatással kapcsolatos gyakori kérdéseket az [Azure-támogatás – gyakori kérdések][Azure support FAQs] című témakör tekinti át.  
        
    ![Alapvető beállítások panel](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![Alapvető beállítások panel1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Töltse ki a **Probléma** panelt.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy 45 000 egységnyi **DTU-kvótával**. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát egy támogatási jegy létrehozásával, és a kérelem típusaként a *Kvóta* kiválasztásával növelheti meg. A DTU-igény kiszámításához szorozza meg 7,5-del az összes szükséges [DWU][DWU] értékét. Ha például két DW6000-et szeretne üzemeltetni egy SQL-kiszolgálón, akkor 90 000 egységnyi DTU-kvótát kell igényelnie.  Az aktuális DTU-felhasználást az SQL-kiszolgáló panelen tekintheti meg a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. 
   > 
   > 
   
5. Töltse ki a **kapcsolattartási adatait**.
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése
A támogatási kérelem elküldése után az Azure támogatási csapata kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Minden támogatási kérelem** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Egyéb erőforrások
Azt is megteheti, hogy kapcsolatba lép az SQL Data Warehouse-közösséggel a [Stack Overflow][Stack Overflow] webhelyen vagy az [Azure SQL Data Warehouse MSDN fórumon][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

