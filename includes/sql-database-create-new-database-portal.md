
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## Új Azure SQL Database létrehozása

Kövesse a következő lépéseket az Azure Portalon, hogy létrehozzon egy új Azure SQL Database adatbázist egy új vagy egy létező Azure SQL Database logikai kiszolgálón.

1. Ha jelenleg nincs csatlakozva, jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Kattintson az **Új** gombra, írja be az **SQL Database** (SQL-adatbázis) kifejezést, majd kattintson az **SQL Database (new database)** (SQL-adatbázis (új adatbázis)) elemre

     ![új adatbázis](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Kattintson az SQL Database (new database) (SQL-adatbázis (új adatbázis)) elemre.

     ![új adatbázis](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
   
4. Kattintson a **Létrehozás** gombra az új adatbázis létrehozásához az SQL Database szolgáltatásban.

     ![új adatbázis](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Adja meg az alábbi kiszolgálótulajdonságok értékeit:

 - Adatbázis neve
 - Előfizetés (csak akkor, ha több előfizetéssel rendelkezik)
 - Erőforráscsoport (ha ezek az első lépései, használja a logikai kiszolgáló erőforráscsoportját)
 - Forrás kiválasztása (választhat üres adatbázist, mintaadatokat vagy egy Azure-adatbázis biztonsági mentését – egy helyszíni SQL Server-adatbázis áttelepítéséhez vagy adatok a BCP használatával történő betöltéséhez tekintse meg a cikk végén található hivatkozásokat)
 - Kiszolgáló (egy új vagy meglévő logikai kiszolgáló)
 - Kiszolgáló-rendszergazdai jelszava
 - Jelszó
 - Tarifacsomag (ha ezek az első lépései, használja az alapértelmezett értéket – S0)
 - Rendezés (csak akkor, ha üres adatbázist választott)

        ![new database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Kattintson a **Létrehozás** gombra – ekkor az értesítési területen látható, hogy az üzembe helyezés megkezdődött.

     ![új adatbázis](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Mielőtt folytatja a következő lépéssel, várja meg, míg az üzembe helyezés befejeződik.

     ![új adatbázis](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=sep16_HO1-->


