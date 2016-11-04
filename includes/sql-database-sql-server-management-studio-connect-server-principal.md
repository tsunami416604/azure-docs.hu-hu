

## Csatlakozás az Azure SQL Database-hez SQL Server-hitelesítéssel
A következő lépések bemutatják, hogyan kapcsolódhat Azure SQL Server-kiszolgálóhoz és -adatbázishoz az SSMS használatával. Ha nincs kiszolgálója és adatbázisa, a létrehozásukhoz tekintse meg az [SQL-adatbázis létrehozása pillanatok alatt](../articles/sql-database/sql-database-get-started.md) című dokumentumot.

1. Az SSMS elindításához írja be a **Microsoft SQL Server Management Studio** kifejezést a Windows keresőmezőjébe, majd kattintson az asztali alkalmazásra.
2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) ablakban adja meg a következő adatokat (ha az SSMS már fut, kattintson a **Connect > Database Engine** (Csatlakozás > Adatbázismotor) elemre a **Connect to Server** (Kapcsolódás a kiszolgálóhoz) ablak megnyitásához):
   
   * **Server type** (Kiszolgáló típusa): Az alapértelmezett adatbázismotor; ne módosítsa ezt az értéket.
   * **Server name** (Kiszolgáló neve): Adja meg az Azure SQL Database-kiszolgáló teljes nevét a következő formátumban: *&lt;kiszolgálónév>*.**database.windows.net**
   * **Authentication type** (Hitelesítés típusa): Ez a cikk ismerteti, hogyan csatlakozhat **SQL Server-hitelesítéssel**. További információ az Azure Active Directoryhoz való kapcsolódásról: [Kapcsolódás Active Directory integrált hitelesítésel](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-integrated-authentication), [Kapcsolódás Active Directory jelszavas hitelesítéssel](../articles/sql-database/sql-database-aad-authentication.md#connect-using-active-directory-password-authentication) és [Kapcsolódás Active Directory univerzális hitelesítéssel](../articles/sql-database/sql-database-ssms-mfa-authentication.md).
   * **User name** (Felhasználónév): Adjon meg egy felhasználónevet, amelynek hozzáférése van a kiszolgálón található adatbázishoz (például a kiszolgáló létrehozásakor beállított *kiszolgálói rendszergazdát*). 
   * **Password** (Jelszó): Adja meg a megadott felhasználó jelszavát (például a kiszolgáló létrehozásakor beállított *jelszót*).
     
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/connect.png)
3. Kattintson a **Connect** (Csatlakozás) gombra.
4. Alapértelmezés szerint az új kiszolgálók nem rendelkeznek meghatározott [tűzfalszabályokkal](../articles/sql-database/sql-database-firewall-configure.md), így az ügyfelek csatlakozása kezdetben le van tiltva. Ha a kiszolgáló még nem rendelkezik olyan tűzfalszabállyal, amely lehetővé teszi egy adott IP-cím csatlakozását, az SSMS rákérdez, hogy létrehozzon-e egy kiszolgálószintű tűzfalszabályt.
   
    Kattintson a **Bejelentkezés** gombra, és hozzon létre kiszolgálószintű tűzfalszabályt. A kiszolgálószintű tűzfalszabály létrehozásához Azure-rendszergazdának kell lennie.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-server-principal/newfirewallrule.png)
5. Miután sikeresen csatlakozott az Azure SQL Database-hez, megnyílik az **Object Explorer**, és elérheti az adatbázisát [adminisztratív feladatok elvégzéséhez vagy adatok lekérdezéséhez.](../articles/sql-database/sql-database-manage-azure-ssms.md).
   
     ![új kiszolgálószintű tűzfal](./media/sql-database-sql-server-management-studio-connect-server-principal/connect-server-principal-5.png)

## Csatlakozási hibák elhárítása
A kapcsolati hibák leggyakoribb okai a kiszolgálónév hibái és a hálózati kapcsolati hibák. Ne feledje, hogy a <*kiszolgálónév*> a kiszolgáló neve, nem az adatbázisé, és teljes kiszolgálónevet kell megadnia: `<servername>.database.windows.net`

Ezenkívül győződjön meg róla, hogy a felhasználónév és a jelszó nem tartalmaz elütéseket vagy felesleges szóközöket (a felhasználónevek nem tesznek különbséget a kis- és nagybetűk között, de a jelszavak igen). 

Explicit módon beállíthat egy protokollt és portszámot is a kiszolgáló nevével, a következőhöz hasonlóan: `tcp:servername.database.windows.net,1433`

A hálózati kapcsolati hibák csatlakozási hibákat és időtúllépéseket is okozhatnak. Elég lehet, ha egyszerűen újra megpróbál csatlakozni (ha tudja, hogy a kiszolgáló neve, a hitelesítő adatok és a tűzfalszabályok helyesek).

További részleteket a kapcsolódási hibákról a [Troubleshoot, diagnose, and prevent SQL connection errors and transient errors for SQL Database](../articles/sql-database/sql-database-connectivity-issues.md) (SQL-csatlakozási hibák és átmeneti hibák elhárítása, diagnosztizálása és megelőzése) című cikk tartalmaz.

<!--HONumber=Oct16_HO1-->


