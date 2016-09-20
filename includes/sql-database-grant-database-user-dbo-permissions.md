

## db_owner engedélyek megadása adatbázis-felhasználónak

Kövesse a következő lépéseket a db_owner engedélyek megadásához egy létező adatbázis-felhasználónak

Ezek a lépések azt feltételezik, hogy csatlakozik az SQL Database szolgáltatáshoz az SSMS Object Exploreréből, és az SQL Database logikai kiszolgálóhoz kiszolgálószintű fő rendszergazdaként vagy felhasználói engedélyek hozzáadásához szükséges engedélyekkel rendelkező felhasználói fiókkal csatlakozik. 

1. Az Object Explorerben bontsa ki a Databases (Adatbázisok) csomópontot, és válassza ki az adatbázist, amelyben megtalálható a felhasználó, akinek megadja a dbo-engedélyeket.

     ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Kattintson a jobb egérgombbal a kiválasztott adatbázisra, majd kattintson a **Query** (Lekérdezés) elemre.

     ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. A lekérdezési ablakban szerkessze és használja a következő Transact-SQL utasítást a dbo-engedélyek megadásához egy adott felhasználó számára. 

    ```ALTER ROLE db_owner ADD MEMBER user1;
    ```

     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)





<!--HONumber=sep16_HO1-->


