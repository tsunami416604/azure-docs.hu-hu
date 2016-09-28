## Kapcsolódás az Azure SQL Database szolgáltatáshoz felhasználóként

Kövesse a következő lépéseket, hogy az SSMS használatával felhasználóként csatlakozzon az Azure SQL Database szolgáltatáshoz.

1. Írja be a „Microsoft SQL Server Management Studio” kifejezést a Windows keresőmezőbe, majd kattintson az asztali alkalmazásra az SSMS elindításához.

2. A Connect to Server (Kapcsolódás a kiszolgálóhoz) ablakban adja meg a következő adatokat:

- **Server type** (Kiszolgáló típusa): Az alapértelmezett adatbázismotor; ne módosítsa ezt az értéket.
 - **Server name** (Kiszolgáló neve): Adja meg az SQL-adatbázisát üzemeltető kiszolgáló nevét a következő formátumban: *&lt;kiszolgálónév>*.**database.windows.net**
 - **Authentication type** (Hitelesítés típusa): Ha ezek az első lépései, válassza az SQL-hitelesítést. Ha engedélyezte az Active Directory szolgáltatást az SQL Database logikai kiszolgálójához, választhatja az Active Directory jelszavas hitelesítést, vagy az Active Directory integrált hitelesítést is.
 - **User name** (Felhasználónév): Ha az SQL-hitelesítést vagy az Active Directory jelszavas hitelesítést választotta, adjon meg egy a felhasználónevet, amelynek hozzáférése van a kiszolgálón található adatbázishoz.
 - **Password** (Jelszó): Ha az SQL-hitelesítést vagy az Active Directory jelszavas hitelesítést választotta, adja meg a megadott felhasználóhoz tartozó jelszót.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

3. Kattintson az **Options** (Beállítások) lehetőségre az adatbázis megadásához, amelyhez kapcsolódni szeretne.

      ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
 
4. A **Connect to Database** (Csatlakozás az adatbázishoz) ablakban válassza ki azt az adatbázist, amelyhez kapcsolódni szeretne.

     ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)

5. Kattintson a **Connect** (Csatlakozás) gombra.
 
6. Ha az ügyfél IP-címének nincs hozzáférése az SQL Database logikai kiszolgálóhoz, a rendszer arra kéri, hogy jelentkezzen be az Azure-fiókjába, és hozzon létre egy kiszolgálószintű tűzfalszabályt. Ha Ön Azure-előfizetési rendszergazda, kattintson a **Bejelentkezés** gombra a kiszolgálószintű tűzfalszabály létrehozásához. Ha nem az, kérjen meg egy rendszergazdát egy kiszolgálószintű vagy adatbázisszintű tűzfalszabály létrehozására ahhoz az adatbázishoz, amelyhez csatlakozni szeretne.
 
      ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
 
7. Ha a hitelesítő adatai megadják a hozzáférést az adott adatbázishoz, megnyílik az Object Explorer – ekkor a felhasználói engedélyektől függően elvégezheti az adminisztratív feladatokat és adatokat is lekérdezhet.
  
      ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)
      
 
## Csatlakozási hibák elhárítása

A csatlakozási hibák leggyakoribb okai a kiszolgáló nevében található hibák (ne feledje, a <*servername*> a logikai kiszolgáló neve, nem az adatbázisé), a helytelen felhasználónév vagy jelszó, valamint a biztonsági okok, melyek miatt a kiszolgáló nem engedélyez kapcsolatokat. 




<!--HONumber=Sep16_HO4-->


