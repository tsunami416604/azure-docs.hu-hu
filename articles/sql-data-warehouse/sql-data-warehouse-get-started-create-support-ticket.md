<properties
   pageTitle="Támogatási jegy létrehozása az SQL Data Warehouse-hoz| Microsoft Azure"
   description="Támogatási jegy létrehozása az SQL Data Warehouse-hoz."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>


# Támogatási jegy létrehozása az SQL Data Warehouse-hoz
 
Ha bármilyen probléma merül fel az SQL Data Warehouses-szal kapcsolatban, hozzon létre egy támogatási jegyet, hogy mérnöki csapatunk segítségére lehessen.

## Támogatási jegy létrehozása

1. Nyissa meg az [Azure Portal][].

2. A kezdőképernyőn kattintson a **Súgó és támogatás** csempére.

    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. A Súgó és támogatás panelen kattintson a **Támogatási kérelem létrehozása** gombra.

    ![Új támogatási kérelem](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Válassza ki a **Kérelemtípust**.

    ![Kérelemtípus](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy 45 000 egységnyi **DTU-kvótával**. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát egy támogatási jegy létrehozásával, és a kérelem típusaként a *Kvóta* kiválasztásával növelheti meg. A szükséges DTU kiszámításához szorozza meg a 7,5-öt a szükséges [DWU][] összes értékével. Ha például két DW6000-et szeretne üzemeltetni egy SQL-kiszolgálón, akkor 90 000 egységnyi DTU-kvótát kell igényelnie.  Az aktuális DTU-felhasználást az SQL-kiszolgáló panelen tekintheti meg a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. 

5. Válassza ki az **Előfizetést**, amely alatt az az adatbázis fut, amellyel kapcsolatban támogatást kér.

    ![Előfizetést](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Erőforrásként adja meg a **SQL Data Warehouse** szolgáltatást.

    ![Erőforrás](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Jelölje ki az [Azure támogatási csomagot][].

    - A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
    - A **javítás/csere** támogatás a [Fejlesztői][], [Standard][], [Közvetlen professzionális támogatás][] vagy a [Premier][] támogatás esetén érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
    - A **fejlesztői mentorálás** és a **tanácsadási szolgáltatás** [Közvetlen professzionális támogatás][] és [Premier][] esetén érhető el. 
    
    Premier szintű támogatás megléte esetén az SQL Data Warehouse-szal kapcsolatos problémákat is jelentheti a [Microsoft Premier online portálon][].  Az [Azure-támogatás ügyfeleknek][Azure támogatási csomagot] című témakör részletesen bemutatja a támogatási csomagokat, beleértve azok hatókörét, a válaszidőt, a díjszabást stb.  Az Azure-támogatással kapcsolatos gyakori kérdéseket az [Azure támogatás – gyakori kérdések][] című témakör tekinti át.  

    ![Támogatási csomag](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Válassza ki a **Problem Type** (Probléma típusa) és a **Category** (Kategória) mezőt.

    ![Probléma típusa és kategóriája](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Írja le a problémát, és hogy az milyen mértékben hat az üzleti működésre.

    ![A probléma leírása](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. A támogatási jegyen előre ki lesznek töltve a **kapcsolattartási adatok**. Ha szükséges, pontosítsa azokat.

    ![Kapcsolattartási adatok](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.


## Támogatási jegy nyomon követése

A támogatási kérelem elküldése után az Azure támogatási csapata kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Támogatási kérelmek kezelése** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## Egyéb források

Ezenkívül kapcsolatba léphet az SQL Data Warehouse-közösséggel a [Stack Overflow][] webhelyen vagy az [Azure SQL Data Warehouse MSDN fórumon][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure Portal]: https://portal.azure.com/
[Azure támogatási csomagot]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Fejlesztői]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Közvetlen professzionális támogatás]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure támogatás – gyakori kérdések]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portálon]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN fórumon]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/




<!--HONumber=Sep16_HO4-->


