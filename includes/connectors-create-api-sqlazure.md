### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [Azure SQL Database](../articles/sql-database/sql-database-get-started.md) és annak kapcsolati adatait, beleértve a kiszolgáló neve, az adatbázisnév és a felhasználónév/jelszó. Ez az információ az SQL adatbázis-kapcsolati karakterlánc szerepel:
  
    Kiszolgáló = tcp:*yoursqlservername*. database.windows.net,1433;Initial katalógus =*yourqldbname*; Biztonsági információ megőrzése = False; Felhasználói azonosító = {your_username}; Jelszó = {your_password}; MultipleActiveResultSets eredménykészleteket = False; Titkosítani = True; TrustServerCertificate = False; Kapcsolódási időtúllépés = 30.
  
    Tudjon meg többet az [Azure SQL-adatbázisok](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> Egy Azure SQL-adatbázis létrehozásakor az SQL mellékelt mintaadatbázisokat is létrehozhat. 
> 
> 

A logikai alkalmazást az Azure SQL Database használatához csatlakozni az SQL-adatbázis. Ehhez egyszerűen a logikai alkalmazásban, az Azure portálon.  

Csatlakozás az Azure SQL-adatbázis, az alábbi lépéseket követve:  

1. Logikai alkalmazás létrehozása. A Logic Apps-tervezőben adja hozzá egy eseményindító, és adja hozzá az műveletet. Válassza ki **megjelenítése Microsoft felügyelt API-k** a legördülő listában, majd adja meg az "sql" be a keresőmezőbe. A műveletek közül választhat:  
   
    ![Az SQL Azure kapcsolat létrehozását lépést](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Ha még nem korábban hozott létre a kapcsolatokat, az SQL Database, kéri a kapcsolat adatai:  
   
    ![Az SQL Azure kapcsolat létrehozását lépést](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Írja be az SQL-adatbázis adatait. Tulajdonságok csillaggal szükség.
   
   | Tulajdonság | Részletek |
   | --- | --- |
   | Csatlakozás az átjárón keresztül |Hagyja üresen ezt. Ez használatos, amikor csatlakozik egy helyszíni SQL Server. |
   | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
   | SQL Server neve * |Adja meg a kiszolgáló nevét; Ez az hasonlót *servername.database.windows.net*. A kiszolgáló nevét az SQL adatbázis-tulajdonságok az Azure-portálon jelenik meg, és a kapcsolati karakterláncot is megjelenik. |
   | SQL-adatbázis neve * |Adja meg a neve, mint az SQL-adatbázis. Ez az SQL adatbázis-tulajdonságok a kapcsolati karakterláncban szereplő: Initial Catalog =*yoursqldbname*. |
   | Felhasználónév * |Megadja a felhasználónevét, az SQL-adatbázis létrehozásakor létrehozott. Ez az SQL adatbázis-tulajdonságok az Azure-portálon jelenik meg. |
   | Jelszó * |Adja meg a jelszót, amelyet az SQL-adatbázis létrehozásakor. |
   
    Ezek a hitelesítő adatok segítségével engedélyezik a Logic Apps alkalmazást, és az SQL-adatok elérése. Művelet befejeződése után a kapcsolat adatai keressen a következőhöz hasonló:  
   
    ![Az SQL Azure kapcsolat létrehozását lépést](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Kattintson a **Létrehozás** gombra. 
5. Figyelje meg, a kapcsolat létrejött. Most folytassa a Logic Apps alkalmazást más lépéseket: 
   
    ![Az SQL Azure kapcsolat létrehozását lépést](./media/connectors-create-api-sqlazure/table.png)

