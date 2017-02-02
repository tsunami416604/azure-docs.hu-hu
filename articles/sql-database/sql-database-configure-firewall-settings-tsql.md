---
title: "T-SQL: Az Azure SQL Database-tűzfalszabályok áttekintése | Microsoft Docs"
description: "A cikk azt ismerteti, hogyan konfigurálhatja a kiszolgáló- és adatbázisszintű tűzszabályokat azon IP-címek esetében, amelyek a Transact-SQL használatával férnek hozzá az Azure SQL-adatbázisokhoz."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: ef51f63984d342422ee5f2a93acfee6357df57ca


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Kiszolgáló- és adatbázisszintű Azure SQL-tűzfalszabályok konfigurálása a T-SQL használatával
> [!div class="op_single_selector"]
> * [Áttekintés](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

A Microsoft Azure SQL Database tűzfalszabályokkal engedélyezi a kiszolgálókhoz és az adatbázisokhoz történő csatlakozást. A master és a felhasználói adatbázisok kiszolgáló- és adatbázisszintű tűzfalbeállításait az Azure SQL Database-kiszolgálón adhatja meg az adatbázisok hozzáférésének szelektív engedélyezéséhez.

> [!IMPORTANT]
> Annak engedélyezéséhez, hogy az Azure-alkalmazások csatlakozhassanak az adatbázis-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. A tűzfalszabályokkal és az Azure-kapcsolatok engedélyezésével kapcsolatos további információkat lásd az [Azure SQL Database-tűzfallal](sql-database-firewall-configure.md) foglalkozó témakörben. Ha az Azure-felhő határain belül létesít kapcsolatokat, előfordulhat, hogy meg kell nyitnia néhány további TCP-portot. További információért tekintse meg az [ADO.NET 4.5 és az SQL Database V12 1433-ason túli portjaival](sql-database-develop-direct-route-ports-adonet-v12.md) foglalkozó cikk az **SQL Database V12 belső és külső kapcsolatait** ismertető szakaszát.
> 
> 

## <a name="server-level-firewall-rules"></a>Kiszolgálószintű tűzfalszabályok
Kiszolgálószintű tűzfalszabályt kizárólag a kiszolgálószintű fő bejelentkező vagy az Azure Active Directory-rendszergazda hozhat létre a Transact-SQL használatával.

1. Indítson el egy lekérdezésablakot, és csatlakozzon a virtuális master adatbázishoz az SQL Server Management Studio segítségével.
2. A kiszolgálószintű tűzfalszabályok a lekérdezésablakban választhatók ki, hozhatók létre, frissíthetők vagy törölhetők.
3. Kiszolgálószintű tűzfalszabály létrehozásához vagy frissítéséhez futtassa az `sp_set_firewall_rule` tárolt eljárást. Az alábbi példa egy IP-címtartományt engedélyez a Contoso kiszolgálón.<br/>Először tekintse meg a már létező szabályokat.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Ezután adjon hozzá egy tűzfalszabályt.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Kiszolgálószintű tűzfalszabály törléséhez futtassa az sp_delete_firewall_rule tárolt eljárást. Az alábbi példa a ContosoFirewallRule nevű szabályt törli.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   További információ a tárolt eljárásokkal kapcsolatban: [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) és [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Adatbázisszintű tűzfalszabályok
Kizárólag az adatbázis **CONTROL** (Felügyelet) engedéllyel rendelkező adatbázis-felhasználója (például az adatbázis tulajdonosa) hozhat létre adatbázisszintű tűzfalszabályokat.

1. Miután létrehozott egy kiszolgálószintű tűzfalszabályt az IP-címéhez, indítson el egy lekérdezésablakot a klasszikus portálon vagy az SQL Server Management Studióban.
2. Csatlakozzon ahhoz az adatbázishoz, amelyhez adatbázisszintű tűzfalszabályt szeretne létrehozni.
   
    Új adatbázisszintű tűzfalszabály létrehozásához vagy egy meglévő frissítéséhez futtassa az `sp_set_database_firewall_rule` tárolt eljárást. Az alábbi példa a ContosoFirewallRule nevű új tűzfalszabályt hozza létre.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Meglévő adatbázisszintű tűzfalszabály törléséhez futtassa az `sp_delete_database_firewall_rule` tárolt eljárást. Az alábbi példa a ContosoFirewallRule nevű szabályt törli.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

További információ a tárolt eljárásokkal kapcsolatban: [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) és [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> Az adatbázisszintű tűzfalszabályok használatát bemutató oktatóanyag: [SQL Database oktatóanyag: SQL Server-hitelesítés, bejelentkezések és felhasználói fiókok, adatbázis-szerepkörök, engedélyek, kiszolgálószintű tűzfalszabályok és adatbázisszintű tűzfalszabályok](sql-database-control-access-sql-authentication-get-started.md).
>


## <a name="next-steps"></a>Következő lépések
A kiszolgálószintű tűzfalszabályok más módszerekkel történő létrehozásával kapcsolatban tekintse meg az alábbi cikkeket: 

* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása az Azure Portalon](sql-database-configure-firewall-settings.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a PowerShell-lel](sql-database-configure-firewall-settings-powershell.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a REST API használatával](sql-database-configure-firewall-settings-rest.md)

Az adatbázisok létrehozásával foglalkozó oktatóanyag: [SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon](sql-database-get-started.md).
Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
Az adatbázisok megnyitásával kapcsolatban az [adatbázis-hozzáférés és a bejelentkezési biztonság kezelésével](https://msdn.microsoft.com/library/azure/ee336235.aspx) foglalkozó cikkben tájékozódhat.

## <a name="additional-resources"></a>További források
* [Az adatbázis biztonságossá tétele](sql-database-security-overview.md)
* [Security Center az SQL Server adatbázismotorhoz és az Azure SQL Database-hez](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Jan17_HO3-->


