<properties
   pageTitle="SQL Data Warehouse létrehozása az Azure Portalon | Microsoft Azure"
   description="Tudnivalók Azure SQL Data Warehouse létrehozásáról az Azure Portalon"
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
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# Azure SQL Data Warehouse létrehozása

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Ebben az oktatóanyagban az Azure Portalon létrehoz egy SQL Data Warehouse-t, amely egy AdventureWorksDW mintaadatbázist tartalmaz.


## Előfeltételek

A kezdéshez a következők szükségesek:

- **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][].
- **Azure SQL-kiszolgáló**: További információ: [Create an Azure SQL Database logical server with the Azure Portal][].

> [AZURE.NOTE] Egy SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása][].

## SQL Data Warehouse létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Kattintson a **+ Új** > **Adatok + tárolás** > **SQL Data Warehouse** lehetőségre.

    ![Létrehozás](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Az **SQL Data Warehouse** panelen adja meg a szükséges információkat, majd kattintson a „Létrehozás” gombra az adatraktár létrehozásához.

    ![Adatbázis létrehozása](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Kiszolgáló**: Javasoljuk, hogy először válassza ki a kiszolgálót.  

    - **Adatbázis neve**: Az SQL Data Warehouse-ra való hivatkozáshoz használt név.  Ennek a kiszolgálón egyedinek kell lennie.
    
    - **Teljesítmény**: Javasoljuk, hogy kiindulásként 400 [DWU-t][DWU] adjon meg. A csúszka jobbra-balra mozgatásával állíthatja az adatraktár teljesítményét, vagy skálázhatja azt vertikálisan le vagy fel a létrehozást követően.  A DWU egységekkel kapcsolatos további információkért tekintse meg [skálázással](./sql-data-warehouse-manage-compute-overview.md) kapcsolatos dokumentációnkat vagy [díjszabási oldalunkat][SQL Data Warehouse díjszabása]. 

    - **Előfizetés**: Válassza ki az [előfizetést], amelyikre az adott SQL Data Warehouse díjai számlázva lesznek.

    - **Erőforráscsoport**: Az [erőforráscsoportok][Erőforráscsoportok] olyan tárolók, amelyek segítségével Azure-erőforrások gyűjteményét kezelheti. További információk az [erőforráscsoportokról](../resource-group-overview.md).

    - **Forrás kiválasztása**: Kattintson a **Forrás kiválasztása** > **Minta** lehetőségre. Az Azure automatikusan feltölti a **Minta kiválasztása** beállítást az AdventureWorksDW elemmel.

> [AZURE.NOTE] Az SQL Data Warehouse alapértelmezett rendezése az SQL_Latin1_General_CP1_CI_AS. Ha másik rendezésre van szüksége, a [T-SQL][] használatával hozhatja létre az adatbázist másik rendezéssel.

4. Kattintson a **Létrehozás** gombra az SQL Data Warehouse létrehozásához.

5. Várjon néhány percet. Amikor az adatraktár elkészült, a rendszer visszalépteti az [Azure Portal](https://portal.azure.com). Az SQL Data Warehouse az irányítópulton található meg az SQL Database adatbázisok listájában vagy a létrehozásához használt erőforráscsoportban. 

    ![portál nézet](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## Következő lépések

Miután létrehozta az SQL Data Warehouse-t, készen áll a [Csatlakozásra](./sql-data-warehouse-connect-overview.md) és lekérdezések indítására.

Adatok az SQL Data Warehouse-ba való betöltésével kapcsolatban lásd a [betöltést áttekintő](./sql-data-warehouse-overview-load.md) cikket.

Ha egy meglévő adatbázist szeretne áttelepíteni az SQL Data Warehouse-ba, tekintse meg az [áttelepítés áttekintését](./sql-data-warehouse-overview-migrate.md), vagy használja az [áttelepítő segédprogramot](./sql-data-warehouse-migrate-migration-utility.md).

Tűzfalszabályok is a Transact-SQL segítségével konfigurálhatók. További információ: [sp_set_firewall_rule][] és [sp_set_database_firewall_rule][].

Érdemes megtekinteni az [Ajánlott eljárások][] is.

<!--Article references-->
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok]: ../resource-group-template-deploy-portal.md
[Ajánlott eljárások]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[előfizetést]: ../azure-glossary-cloud-terminology.md#subscription
[erőforráscsoport]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-fiók létrehozása]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Havi Azure-kredit a Visual Studio-előfizetőknek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F




<!--HONumber=sep16_HO1-->


