
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali listában válassza ki a **minden szolgáltatás**. 

3. Görgessen és válassza ki **SQL Server-kiszolgálók**. 
   
    ![Az Azure SQL adatbázis-kiszolgáló található a portálon][b21-FindServerInPortal]
5. A Szűrő mezőbe írja be annak a kiszolgálónak a nevét elindításához. A sor jelenik meg.

6. Jelölje ki a kiszolgáló sort. A kiszolgáló egy panel jelenik meg.

7. Válassza ki a kiszolgálójuk paneljéről **beállítások**. 

8. Válassza ki **tűzfal**. 
   
    ![Válassza ki a beállítások > tűzfal][b31-SettingsFirewallNavig]
9. Válassza ki **adja hozzá ügyfél IP**. Az első szövegmezőbe írja be az új szabály nevét.

10. Adja meg a kis és nagy IP cím a tartomány értékeinek engedélyezni szeretné.
    
    * Azok az alacsony érték end jöhet **.0** és a nagy értékű végződhet **.255**.
    
    ![Engedélyezi az IP-címtartomány hozzáadása][b41-AddRange]
11. Kattintson a **Mentés** gombra.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
