---
title: "Az Azure SQL Database felügyelete az Azure Portallal | Microsoft Docs"
description: "Rövid összefoglalás a relációs adatbázisok az Azure Portallal történő felhőalapú kezeléséről."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 420b2153f6115dd712d3033e30f11f79b18cd80f
ms.openlocfilehash: be89a2799af3bdc2938f73e3d54f00f81d9ab9cd


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Azure SQL Database-adatbázisok kezelése az Azure Portallal
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Az [Azure Portalon](https://portal.azure.com/) létrehozhatja, monitorozhatja és kezelheti az Azure SQL Database-adatbázisokat és -kiszolgálókat. Ez a cikk a leggyakoribb feladatok rövid leírását és a részletes információkra mutató hivatkozásokat tartalmazza.

> [!TIP]
> A kiszolgálók és a kiszolgálóalapú tűzfalak létrehozásáról, a kiszolgáló tulajdonságainak megtekintéséről, az SQL Server Management Studióval való csatlakozásról, a master adatbázis lekérdezéséről, a mintaadatbázis, illetve üres adatbázis létrehozásáról, az adatbázis tulajdonságainak lekérdezéséről, valamint az SQL Server Management Studióval való kapcsolódásról és a mintaadatbázis lekérdezéséről szóló oktatóanyagok az [Első lépéseket ismertető oktatóanyagban](sql-database-get-started.md) találhatók.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Az Azure SQL Database-adatbázisok, -kiszolgálók és -készletek megtekintése
Az elérhető SQL Database-szolgáltatások listájának megtekintéséhez kattintson a **További szolgáltatások** elemre, és a keresőmezőbe írja be az **SQL** kifejezést:

![SQL Database](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Hogyan hozhatók létre vagy tekinthetők meg az Azure SQL Database-adatbázisok?
Az **SQL Database-adatbázisok** panel megnyitásához kattintson az **SQL Database-adatbázisok** elemre, majd kattintson a használni kívánt adatbázisra, vagy kattintson a **+Hozzáadás** elemre, és hozzon létre egy SQL Database-adatbázist. További részletek: [SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon](sql-database-get-started.md).

![SQL Database-adatbázisok](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Hogyan hozhatók létre vagy tekinthetők meg az Azure SQL Server-kiszolgálók?
Az **SQL Server-kiszolgálók** panel megnyitásához kattintson az **SQL Server-kiszolgálók** elemre, majd kattintson a használni kívánt kiszolgálóra, vagy kattintson a **+Hozzáadás** elemre, és hozzon létre egy SQL Server-kiszolgálót. További részletek: [SQL Database-adatbázis létrehozása pillanatok alatt az Azure Portalon](sql-database-get-started.md).

![SQL Server-kiszolgálók](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Hogyan hozhatók létre vagy tekinthetők meg a rugalmas SQL-készletek?
A **Rugalmas SQL-készletek** panel megnyitásához kattintson a **Rugalmas SQL-készletek** elemre, majd kattintson a használni kívánt készletre, vagy kattintson a **+Hozzáadás** elemre, és hozzon létre egy készletet. További részletek a [Rugalmas készlet létrehozása az Azure Portalon](sql-database-elastic-pool-create-portal.md) című részben találhatók.

![Rugalmas SQL-készletek](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Hogyan frissíthetők vagy tekinthetők meg az SQL Database-adatbázisok beállításai?
Az adatbázis-beállítások megtekintéséhez vagy frissítéséhez kattintson a kívánt beállításra az SQL Database-adatbázis panelen:

![SQL Database-adatbázis beállításai](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Hogyan kérdezhető le az SQL Database-adatbázisok teljes kiszolgálóneve?
Az adatbázisok kiszolgálónevének megtekintéséhez kattintson az **Áttekintés** elemre az **SQL Database-adatbázis** panelen, és jegyezze fel a kiszolgáló nevét:

![SQL Database-adatbázis beállításai](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Hogyan kezelhetők a tűzfalszabályok az SQL-kiszolgálóhoz és -adatbázishoz való hozzáférés vezérléséhez?
A tűzfalszabályok megtekintéséhez, létrehozásához és frissítéséhez kattintson a **Kiszolgálótűzfal beállítása** elemre az **SQL Database-adatbázis** panelen. Részletes információ: [Azure SQL Database kiszolgálószintű tűzfalszabály létrehozása az Azure Portal használatával](sql-database-configure-firewall-settings.md).

![tűzfalszabályok](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Hogyan módosítható az SQL Database-adatbázis szolgáltatásszintje és teljesítményszintje?
Az SQL Database-adatbázis szolgáltatásszintjének és teljesítményszintjének frissítéséhez kattintson az **Tarifacsomag (DTU-k méretének beállítása)** elemre az **SQL Database-adatbázis** panelen. Bővebb információ: [SQL Database-adatbázisok szolgáltatás- és teljesítményszintjének (tarifacsomag) módosítása](sql-database-scale-up.md).

![tarifacsomagok](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Hogyan konfigurálható a naplózás és a fenyegetésészlelés az SQL Database-adatbázisokhoz?
Ha konfigurálni szeretné a naplózást és a fenyegetésészlelést az SQL Database-adatbázishoz, kattintson a **Naplózás és fenyegetésészlelés** elemre az **SQL Database-adatbázis** panelen. Részletes információk: [Ismerkedés az Ismerkedés az SQL Database naplózási szolgáltatásával](sql-database-auditing-get-started.md) és [Ismerkedés az SQL Database fenyegetésészlelési szolgáltatásával](sql-database-threat-detection-get-started.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Hogyan konfigurálható a dinamikus adatmaszkolás az SQL Database-adatbázisokban?
Az SQL Database dinamikus adatmaszkolásának konfigurálásához kattintson a **Dinamikus adatmaszkolás** elemre az **SQL Database-adatbázis** panelen. További információ: [Az SQL Database dinamikus adatmaszkolásának első lépései](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Hogyan konfigurálható transzparens adattitkosítás (TDE) az SQL Database-adatbázisokban?
Az SQL Database transzparens adattitkosításának konfigurálásához kattintson a **Transzparens adattitkosítás** elemre az **SQL Database-adatbázis** panelen. További információ: [Transzparens adattitkosítás engedélyezése a portálon](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Hogyan módosítható az SQL Database-adatbázis maximális mérete?
Az SQL Database-adatbázisok méretének megtekintéséhez vagy módosításához kattintson az **Adatbázis mérete** elemre az **SQL Database-adatbázis** panelen. Az adatbázis maximális méretét a szolgáltatásszint és a teljesítményszint módosításával frissítheti. Bővebb információ: [SQL Database-adatbázisok szolgáltatás- és teljesítményszintjének (tarifacsomag) módosítása](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Hogyan monitorozható és javítható az SQL Database-adatbázisok teljesítménye?
Az SQL Database-adatbázisok teljesítményjellemzőinek monitorozásához és javításához kattintson a **Teljesítmény áttekintése** elemre az **SQL Database-adatbázis** panelen. További információ: [SQL Database-teljesítményelemzés](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Hogyan konfigurálható a georeplikáció?
Ha georeplikációt kíván beállítani az SQL Database-adatbázishoz, kattintson a **Georeplikáció** elemre az **SQL Database-adatbázis** panelen. További információ: [Georeplikáció konfigurálása az Azure SQL Database-adatbázishoz az Azure Portalon](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Hogyan lehet feladatátvételt beállítani egy georeplikált SQL Database-adatbázishoz?
Ha feladatátvételt szeretne beállítani egy georeplikált másodlagos adatbázishoz, kattintson a **Georeplikáció** elemre az **SQL Database-adatbázis** panelen, majd kattintson a **Feladatátvétel** elemre. További információ: [Tervezett vagy nem tervezett feladatátvétel inicializálása az Azure SQL Database-hez az Azure Portal használatával](sql-database-geo-replication-failover-portal.md).

## <a name="how-do-i-copy-a-sql-database"></a>Hogyan másolható egy SQL Database-adatbázis?
Az SQL Database-adatbázisok másolásához kattintson a **Másolás** elemre az **SQL Database-adatbázis** panelen. További információ: [Azure SQL Database-adatbázisok másolása az Azure Portal használatával](sql-database-copy-portal.md).

![SQL Database-adatbázis beállításai](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Hogyan archiválhatók az Azure SQL Database-adatbázisok BACPAC-fájlba?
Ha BACPAC-fájlt szeretne létrehozni egy SQL Database-adatbázisból, kattintson az **Exportálás** elemre az **SQL Database-adatbázis** panelen. További információ: [Azure SQL Database-adatbázisok archiválása BACPAC-fájlba az Azure Portal használatával](sql-database-export.md).

![SQL Database-adatbázis exportálása](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Hogyan állítható vissza egy SQL Database-adatbázis egy korábbi időpontra?
Az SQL Database-adatbázis visszaállításához kattintson a **Visszaállítás** elemre az **SQL Database-adatbázis** panelen. További információ: [Azure SQL Database-adatbázis visszaállítása egy korábbi időpontra az Azure Portal használatával](sql-database-point-in-time-restore.md).

![SQL Database-adatbázis beállításai](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Hogyan hozható létre Azure SQL Database-adatbázis egy BACPAC-fájlból?
Ha SQL Database-adatbázist kíván létrehozni egy BACPAC-fájlból, kattintson az **Adatbázis importálása** elemre az **SQL Database-adatbázis** panelen. További információ: [BACPAC-fájl importálása Azure SQL Database-adatbázis létrehozásához](sql-database-import.md).

![SQL Server](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Hogyan állíthatók vissza a törölt SQL Database-adatbázisok?
Törölt SQL Database-adatbázis visszaállításához kattintson a **Törölt adatbázisok** elemre az **SQL Server-kiszolgáló** panelen (a törölt adatbázist tartalmazó SQL Server-kiszolgálón). További információ: [Törölt Azure SQL Database-adatbázis visszaállítása az Azure Portal használatával](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Hogyan törölhető egy SQL Database-adatbázis?
SQL Database-adatbázis törléséhez kattintson a **Törlés** elemre a **SQL Database-adatbázis** panelen. 

![SQL Database-adatbázis beállításai](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>További források
* [SQL Database](sql-database-technical-overview.md)
* [Rugalmas készlet monitorozása és kezelése az Azure Portalon](sql-database-elastic-pool-manage-portal.md)




<!--HONumber=Dec16_HO3-->


