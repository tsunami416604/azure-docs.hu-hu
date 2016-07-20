<properties
   pageTitle="SQL Data Warehouse létrehozása az Azure portálon | Microsoft Azure"
   description="Tudnivalók Azure SQL Data Warehouse létrehozásáról az Azure portálon"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure SQL Data Warehouse létrehozása

> [AZURE.SELECTOR]
- [Azure portálra](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Ebben az oktatóanyagban az Azure portálon létrehoz egy SQL Data Warehouse-t, amely egy AdventureWorksDW mintaadatbázist tartalmaz.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Kattintson a **+ Új** > **Adatok + tárolás** > **SQL Data Warehouse** lehetőségre.

    ![Létrehozás](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Az **SQL Data Warehouse** panelen adja meg a szükséges információkat, majd kattintson a „Létrehozás” gombra az adatraktár létrehozásához.

    ![Adatbázis létrehozása](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Kiszolgáló**: Javasoljuk, hogy először válassza ki a kiszolgálót.  Kiválaszthat egy meglévő kiszolgálót, vagy [létrehozhat egy újat](./sql-data-warehouse-get-started-new-server.md). 

    - **Adatbázis neve**: Az SQL Data Warehouse-ra való hivatkozáshoz használt név.  Ennek a kiszolgálón egyedinek kell lennie.
    
    - **Teljesítmény**: Javasoljuk, hogy kiindulásként 400 DWU-t adjon meg. A csúszka jobbra-balra mozgatásával állíthatja az adatraktár teljesítményét, vagy skálázhatja azt vertikálisan le vagy fel a létrehozást követően.  A DWU egységekkel kapcsolatos további információkért tekintse meg [skálázással](./sql-data-warehouse-manage-compute-overview.md) kapcsolatos dokumentációnkat vagy [díjszabási oldalunkat](https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/). 

    - **Előfizetés**: Válassza ki az előfizetést, amelyikre az adott SQL Data Warehouse díjai számlázva lesznek.

    - **Erőforráscsoport**: Az erőforráscsoportok olyan tárolók, amelyek segítségével Azure-erőforrások gyűjteményét kezelheti. További információk az [erőforráscsoportokról](../azure-portal/resource-group-portal.md).

    - **Forrás kiválasztása**: Kattintson a **Forrás kiválasztása** > **Minta** lehetőségre. Mivel jelenleg csupán egyetlen mintaadatbázis érhető el, a Minta lehetőség kiválasztásakor az Azure automatikusan feltölti a **Minta kiválasztása** beállítást az AdventureWorksDW elemmel.

4. Kattintson a **Létrehozás** gombra az SQL Data Warehouse létrehozásához.

5. Várjon néhány percet, amíg az SQL Data Warehouse elkészül. Amikor elkészült, a rendszer visszalépteti az [Azure portálra](https://portal.azure.com). Az SQL Data Warehouse az irányítópulton található meg az SQL Database adatbázisok listájában vagy a létrehozásához használt erőforráscsoportban. 

    ![Portál nézet](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL DataBase create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## További lépések

Miután létrehozta az SQL Data Warehouse-t, készen áll a [Csatlakozásra](./sql-data-warehouse-get-started-connect.md) és lekérdezések indítására.

Adatok az SQL Data Warehouse-ba való betöltésével kapcsolatban lásd a [betöltést áttekintő](./sql-data-warehouse-overview-load.md) cikket.

Ha egy meglévő adatbázist szeretne áttelepíteni az SQL Data Warehouse-ba, tekintse meg az [áttelepítés áttekintését](./sql-data-warehouse-overview-migrate.md), vagy használja az [áttelepítő segédprogramot](./sql-data-warehouse-migrate-migration-utility.md).




<!--HONumber=Jun16_HO2-->


