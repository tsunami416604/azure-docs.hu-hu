<properties
   pageTitle="SQL Data Warehouse létrehozása Powershell használatával | Microsoft Azure"
   description="SQL Data Warehouse létrehozása Powershell használatával"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# SQL Data Warehouse létrehozása Powershell használatával

> [AZURE.SELECTOR]
- [Azure portálra](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Előfeltételek
Mielőtt elkezdi a műveletet, ellenőrizze az alábbi előfeltételek meglétét.

- **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][].
- **V12 Azure SQL Server**: Lásd: [Create an Azure SQL Database logical server with the Azure Portal][] (Azure SQL Database logikai kiszolgáló létrehozása az Azure portál használatával) vagy [Create an Azure SQL Database logical server with PowerShell][] (Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával).
- **Erőforráscsoport neve**: Használja ugyanazt az erőforráscsoportot, mint a V12 Azure SQL Server, vagy tekintse át az [erőforráscsoportok][] részt egy új erőforráscsoport létrehozásához.
- **PowerShell 1.0.3-as vagy újabb verzió**: A verziószámot a **Get-Module -ListAvailable -Name Azure** futtatásával ellenőrizheti.  A legújabb verziót a [Microsoft Webplatform-telepítőből][] telepítheti.  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][] (Az Azure PowerShell telepítése és konfigurálása).

> [AZURE.NOTE] Az új SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  A díjszabással kapcsolatos további információkért lásd: [SQL Data Warehouse díjszabása][].

## SQL Data Warehouse-adatbázis létrehozása
1. Nyissa meg a Windows PowerShellt.
2. A parancsmag futtatásával jelentkezzen be az Azure Resource Managerbe.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Válassza ki a jelenlegi munkamenethez használni kívánt előfizetést.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Hozza létre az adatbázist. Ebben a példában egy új adatbázist hozunk létre „mynewsqldw” néven, „DW400” szintű szolgáltatási céllal az „sqldwserver1” elnevezésű kiszolgálóra, amely a „mywesteuroperesgp1” nevű erőforráscsoportban található.

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
    ```

A parancsmag kötelezően megadandó paraméterei:

- **RequestedServiceObjectiveName**: A kért DWU-mennyiség, „DWXXX” formátumban. A DWU bizonyos mennyiségű processzor- és memóriakiosztást jelöl.  Az egyes DWU-értékek az erőforrások lineárisan növekvő kapacitását jelölik.  A jelenleg támogatott értékek a következők: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500 és 2000.
- **DatabaseName**: A létrehozandó SQL Data Warehouse neve.
- **ServerName**: A létrehozás során használt kiszolgáló neve (V12-nek kell lennie).
- **ResourceGroupName**: A használt erőforráscsoport.  Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
- **Edition**: Egy SQL Data Warehouse létrehozásához a kiadást állítsa „DataWarehouse” értékre.

A paraméterbeállításokkal kapcsolatos további információkért lásd: [Create Database (Azure SQL Data Warehouse)][] (Adatbázis létrehozása (Azure SQL Data Warehouse)).
A parancsreferenciáért lásd: [New-AzureRmSqlDatabase][]

## További lépések
Miután az SQL Data Warehouse kiépítése befejeződött, [mintaadatokat tölthet be][], vagy további részleteket tudhat meg a [fejlesztés][], [betöltés][] vagy [áttelepítés][] mikéntjéről.

Ha érdekli az SQL Data Warehouse programozott módon való kezelése, tekintse meg a [PowerShell-parancsmagok és a REST API-k][] használatával kapcsolatos cikkünket.

<!--Image references-->

<!--Article references-->
[áttelepítés]: ./sql-data-warehouse-overview-migrate.md
[fejlesztés]: ./sql-data-warehouse-overview-develop.md
[betöltés]: ./sql-data-warehouse-load-with-bcp.md
[mintaadatokat tölthet be]: ./sql-data-warehouse-get-started-manually-load-samples.md
[PowerShell-parancsmagok és a REST API-k]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[tűzfalszabályok]: ./sql-database-configure-firewall-settings.md
[How to install and configure Azure PowerShell]: ../powershell/powershell-install-configure.md
[SQL Data Warehouse létrehozása az Azure portálról]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell (Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával)]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]:https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Webplatform-telepítőből]: https://aka.ms/webpi-azps
[SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-fiók létrehozása]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Havi Azure-kredit a Visual Studio-előfizetőknek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Jun16_HO2-->


