

## <a name="create-new-database-user-using-ssms"></a>Új adatbázis-felhasználó létrehozása az SSMS használatával
Kövesse a következő lépéseket, hogy létrehozzon egy adatbázis-felhasználót egy létező adatbázisban az SSMS használatával. 

Ezek a lépések azt feltételezik, hogy csatlakozik az SQL Database szolgáltatáshoz az SSMS Object Exploreréből, és az SQL Database logikai kiszolgálóhoz kiszolgálószintű fő rendszergazdaként vagy új felhasználó létrehozásához szükséges engedélyekkel rendelkező felhasználói fiókkal csatlakozik. 

1. Az Object Explorerben bontsa ki a Databases (Adatbázisok) csomópontot, és válassza ki az adatbázist, amelyben létre kívánja hozni az új felhasználói fiókot.
   
     ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)
2. Kattintson a jobb egérgombbal a kiválasztott adatbázisra, majd kattintson a **Query** (Lekérdezés) elemre.
   
     ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)
3. A lekérdezési ablakban szerkessze és használja a következő Transact-SQL utasítást egy felhasználó létrehozásához a felhasználói adatbázisban. 
   
    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';
    ```
   
     ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)



<!--HONumber=Nov16_HO2-->


