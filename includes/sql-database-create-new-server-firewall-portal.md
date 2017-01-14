
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

1. Az SQL Server tűzfalához tartozó panel megnyitásához kattintson a Beállítások terület alatt található **Tűzfal** elemre az SQL Server paneljén.

    ![sql server tűzfal](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png)
    
2. Ellenőrizze a megjelenő ügyfél IP-címet, és egy internetes böngészőben győződjön meg róla, hogy valóban ez az Ön által használt IP-cím (vannak olyan weboldalak, amelyek megadják ezt az információt). Időnként előfordulhat, hogy az IP-címek nem egyeznek.

    ![a használt IP-cím](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. Amennyiben az IP-címek egyeznek, kattintson az **Ügyfél IP-címének hozzáadása** elemre az eszköztárban.

    ![ügyfél IP-címének hozzáadása](../articles/sql-database/media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Az SQL Database-tűzfalat a kiszolgálón egyetlen IP-cím vagy egy teljes IP-címtartomány előtt is megnyithatja. A tűzfal megnyitását követően az SQL-rendszergazdák és -felhasználók a kiszolgáló bármely olyan adatbázisába bejelentkezhetnek, amelyhez érvényes hitelesítő adatokkal rendelkeznek.
    >

4. A kiszolgálószintű tűzfalszabály mentéséhez kattintson a **Mentés** gombra az eszköztárban, majd kattintson az **OK** gombra.

    ![ügyfél IP-címének hozzáadása](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

> [!Tip]
> Az oktatóanyagot lásd a [Kiszolgáló, kiszolgálószintű tűzfalszabály, mintaadatbázis és adatbázisszintű tűzfalszabály létrehozása, és csatlakozás az SQL Server Management Studióhoz](../articles/sql-database/sql-database-get-started.md) című témakörben.    
>


<!--HONumber=Jan17_HO1-->


