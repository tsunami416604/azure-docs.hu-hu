

## Csatlakozzon az Azure SQL Database szolgáltatáshoz kiszolgálószintű fő bejelentkezéssel

Kövesse a következő lépéseket, hogy az SSMS használatával csatlakozzon az Azure SQL Database szolgáltatáshoz a kiszolgálószintű fő bejelentkezés használatával.

1. Írja be a „Microsoft SQL Server Management Studio” kifejezést a Windows keresőmezőbe, majd kattintson az asztali alkalmazásra az SSMS elindításához.

2. A Connect to Server (Kapcsolódás a kiszolgálóhoz) ablakban adja meg a következő adatokat:

 - **Server type** (Kiszolgáló típusa): Az alapértelmezett adatbázismotor; ne módosítsa ezt az értéket.
 - **Server name** (Kiszolgáló neve): Adja meg az SQL-adatbázisát üzemeltető kiszolgáló nevét a következő formátumban: *&lt;kiszolgálónév>*.**database.windows.net**
 - **Authentication type** (Hitelesítés típusa): Ha ezek az első lépései, válassza az SQL-hitelesítést. Ha engedélyezte az Active Directory szolgáltatást az SQL Database logikai kiszolgálójához, választhatja az Active Directory jelszavas hitelesítést, vagy az Active Directory integrált hitelesítést is.
 - **User name** (Felhasználónév): Ha az SQL-hitelesítést vagy az Active Directory jelszavas hitelesítést választotta, adjon meg egy a felhasználónevet, amelynek hozzáférése van a kiszolgálón található adatbázishoz.
 - **Password** (Jelszó): Ha az SQL-hitelesítést vagy az Active Directory jelszavas hitelesítést választotta, adja meg a megadott felhasználóhoz tartozó jelszót.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-1.png)

3. Kattintson a **Csatlakozás** gombra.
 
4. Ha az ügyfél IP-címének nincs hozzáférése az SQL Database logikai kiszolgálóhoz, a rendszer arra kéri, hogy jelentkezzen be az Azure-fiókjába, és hozzon létre egy kiszolgálószintű tűzfalszabályt. Ha Ön Azure-előfizetési rendszergazda, kattintson a **Bejelentkezés** gombra a kiszolgálószintű tűzfalszabály létrehozásához. Ha nem az, kérjen meg egy Azure rendszergazdát a kiszolgálószintű tűzfalszabály létrehozására.
 
      ![SQL Server Management Studio: Csatlakozás az SQL Database kiszolgálóhoz](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-2.png)
 
1. Ha Ön Azure-előfizetési rendszergazda, és be kell jelentkeznie, a bejelentkezési oldal megjelenése után adja meg az előfizetése hitelesítő adatait, és jelentkezzen be.

      ![bejelentkezés](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-3.png)
 
1. Miután sikeresen bejelentkezett az Azure-ba, ellenőrizze a tervezett kiszolgálószintű tűzfalszabályt (ezt módosíthatja, hogy egy IP-címtartományt engedélyezzen), majd kattintson az **OK** gombra a tűzfalszabály létrehozásához és az SQL Database szolgáltatáshoz való csatlakozás befejezéséhez.
 
      ![új kiszolgálószintű tűzfal](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-4.png)
 
5. Ha a hitelesítő adatai megadják a hozzáférést, megnyílik az Object Explorer – ekkor elvégezheti az adminisztratív feladatokat és adatokat is lekérdezhet. 
 
     ![új kiszolgálószintű tűzfal](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)
 
     
## Csatlakozási hibák elhárítása

A csatlakozási hibák leggyakoribb okai a kiszolgáló nevében található hibák (ne feledje, a <*servername*> a logikai kiszolgáló neve, nem az adatbázisé), a helytelen felhasználónév vagy jelszó, valamint a biztonsági okok, melyek miatt a kiszolgáló nem engedélyez kapcsolatokat. 





<!--HONumber=Jun16_HO2-->


