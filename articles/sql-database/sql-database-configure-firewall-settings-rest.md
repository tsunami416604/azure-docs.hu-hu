---
title: "REST API: Az Azure SQL Database kiszolgálószintű tűzfalszabályai | Microsoft Docs"
description: "A cikk azt ismerteti, hogyan konfigurálhatja a kiszolgálószintű tűzfalszabályokat azon IP-címek esetében, amelyek a REST API használatával férnek hozzá az Azure SQL-adatbázisokhoz."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: 6ac9bdf34e8cf0a8ca8e06ae3ed6cd2dd1cf408b


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a REST API használatával
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

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Kiszolgálószintű tűzfalszabályok kezelése REST API-val
1. A tűzfalszabályok REST API-val történő kezelését hitelesíteni kell. Ezzel kapcsolatban az [Azure Resource Manager API-val végzett engedélyezés fejlesztői útmutatója](../azure-resource-manager/resource-manager-api-authentication.md) tartalmaz további információkat.
2. A REST API-val létrehozhat, frissíthet, illetve törölni is tud kiszolgálószintű szabályokat.
   
    Kiszolgálószintű tűzfalszabály létrehozásához vagy frissítéséhez futtassa a PUT metódust az alábbiak használatával:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    A kérelem törzse
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Meglévő kiszolgálószintű tűzfalszabály eltávolításához futtassa a DELETE metódust az alábbiak használatával:

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Tűzfalszabályok kezelése a REST API használatával
* [Tűzfalszabály létrehozása vagy frissítése](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Tűzfalszabály törlése](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Tűzfalszabály lekérése](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Az összes tűzfalszabály listázása](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>Következő lépések
Ha a Transact-SQL segítségével szeretne létrehozni kiszolgálószintű és adatbázisszintű tűzfalszabályokat, tekintse meg az [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok T-SQL használatával végzett konfigurálására](sql-database-configure-firewall-settings-tsql.md) vonatkozó témakört. 

A kiszolgálószintű tűzfalszabályok más módszerekkel történő létrehozásával kapcsolatban tekintse meg az alábbi cikkeket: 

* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása az Azure Portalon](sql-database-configure-firewall-settings.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a PowerShell-lel](sql-database-configure-firewall-settings-powershell.md)

Az adatbázisok létrehozásával foglalkozó oktatóanyag: [SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon](sql-database-get-started.md).
Ha nyílt forráskódú vagy külső alkalmazásokból szeretne kapcsolódni az Azure SQL Database-hez, lásd az [SQL Database gyors üzembe helyezési ügyfélkódmintáival](https://msdn.microsoft.com/library/azure/ee336282.aspx) foglalkozó cikket.
Az adatbázisok megnyitásával kapcsolatban az [adatbázis-hozzáférés és a bejelentkezési biztonság kezelésével](https://msdn.microsoft.com/library/azure/ee336235.aspx) foglalkozó cikkben tájékozódhat.

## <a name="additional-resources"></a>További források
* [Az adatbázis biztonságossá tétele](sql-database-security-overview.md)
* [Security Center az SQL Server adatbázismotorhoz és az Azure SQL Database-hez](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->





<!--HONumber=Jan17_HO1-->


