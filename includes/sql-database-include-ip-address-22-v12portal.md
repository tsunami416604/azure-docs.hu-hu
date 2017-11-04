
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) , http://portal.azure.com/.
2. Kattintson a bal oldali szalagcím **összes TALLÓZÁSA**. A **Tallózás** panel jelenik meg.
3. Görgetéssel **SQL Server-kiszolgálók**. A **SQL Server-kiszolgálók** panel jelenik meg.
   
    ![Az Azure SQL adatbázis-kiszolgáló található a portálon][b21-FindServerInPortal]
4. Kényelmi okokból kattintson a kis méretűvé a korábbi **Tallózás** panelen.
5. A Szűrő mezőbe írja be annak a kiszolgálónak a nevét elindításához. A sor jelenik meg.
6. A kiszolgáló sorára kattintson. A kiszolgáló egy panel jelenik meg.
7. A kiszolgáló paneljén kattintson **beállítások**. A **beállítások** panel jelenik meg.
8. Kattintson a **tűzfal**. A **tűzfalbeállítások** panel jelenik meg.
   
    ![Kattintson a beállítások > tűzfal][b31-SettingsFirewallNavig]
9. Kattintson a **adja hozzá ügyfél IP**. Adjon meg egy nevet az új szabály az első szövegmezőbe.
10. Adja meg a kis és nagy IP cím a tartomány értékeinek engedélyezni szeretné.
    
    * Azok az alacsony érték end jöhet **.0** és a magas **.255**.
    
    ![Engedélyezi az IP-címtartomány hozzáadása][b41-AddRange]
11. Kattintson a **Save** (Mentés) gombra.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
