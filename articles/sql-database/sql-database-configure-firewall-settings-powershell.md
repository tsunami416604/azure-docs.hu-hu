---
title: "PowerShell: Az Azure SQL Database-tűzfalszabályok konfigurálása | Microsoft Docs"
description: "Ismerje meg, hogyan konfigurálhatja a kiszolgálói szintű tűzfalszabályokat azon IP-címek esetében, amelyek PowerShell használatával férnek hozzá az Azure SQL-adatbázisokhoz."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
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
ms.openlocfilehash: d80bd1fbb5cdb0492e521a4d600f657fac0e3325


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Áttekintés](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Az Azure SQL Database tűzfalszabályaival engedélyezheti a kiszolgálókhoz és az adatbázisokhoz történő csatlakozást. A master és a felhasználói adatbázisok kiszolgáló- és adatbázisszintű tűzfalbeállításait az SQL Database-kiszolgálón adhatja meg az adatbázisok hozzáférésének szelektív engedélyezéséhez.

> [!IMPORTANT]
> Annak engedélyezéséhez, hogy az Azure-alkalmazások csatlakozhassanak az adatbázis-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. A tűzfalszabályokkal és az Azure-kapcsolatok engedélyezésével kapcsolatos további információkat lásd az [Azure SQL Database-tűzfallal](sql-database-firewall-configure.md) foglalkozó témakörben. Ha az Azure-felhő határain belül létesít kapcsolatokat, előfordulhat, hogy meg kell nyitnia néhány további TCP-portot. További információért tekintse meg az [ADO.NET 4.5 és az SQL Database V12 1433-ason túli portjaival](sql-database-develop-direct-route-ports-adonet-v12.md) foglalkozó cikk az SQL Database V12 belső és külső kapcsolatait ismertető szakaszát.
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Kiszolgálói tűzfalszabályok létrehozása
A kiszolgálószintű tűzfalszabályok az Azure PowerShell-lel is létrehozhatók, frissíthetők és törölhetők.

Új kiszolgálószintű tűzfalszabály létrehozásához futtassa a [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) parancsmagot. Az alábbi példa egy IP-címtartományt engedélyez a Contoso kiszolgálón.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Meglévő kiszolgálószintű tűzfalszabály módosításához futtassa a [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx) parancsmagot. Az alábbi példa módosítja a ContosoFirewallRule nevű szabály elfogadható IP-címeinek tartományát.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

Meglévő kiszolgálószintű tűzfalszabály törléséhez futtassa a [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx) parancsmagot. Az alábbi példa a ContosoFirewallRule nevű szabályt törli.

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Tűzfalszabályok kezelése a PowerShell-lel
A PowerShell a tűzfalszabályok kezelésére is használható. További információkért tekintse át a következők témaköröket:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Következő lépések
Ha a Transact-SQL segítségével szeretne létrehozni kiszolgálószintű és adatbázisszintű tűzfalszabályokat, tekintse meg az [Azure SQL Database kiszolgálószintű és adatbázisszintű tűzfalszabályok T-SQL használatával végzett konfigurálására](sql-database-configure-firewall-settings-tsql.md) vonatkozó témakört.

A kiszolgálószintű tűzfalszabályok más módszerekkel történő létrehozásával kapcsolatos információkért tekintse meg az alábbi cikkeket:

* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása az Azure Portalon](sql-database-configure-firewall-settings.md)
* [Kiszolgálószintű Azure SQL Database-tűzfalszabályok konfigurálása a REST API használatával](sql-database-configure-firewall-settings-rest.md)

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


