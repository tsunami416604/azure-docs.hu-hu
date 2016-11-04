---
title: SQL Data Warehouse létrehozása PowerShell használatával | Microsoft Docs
description: SQL Data Warehouse létrehozása PowerShell használatával
services: sql-data-warehouse
documentationcenter: NA
author: lodipalm
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/25/2016
ms.author: lodipalm;barbkess;sonyama

---
# SQL Data Warehouse létrehozása PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan hozható létre az SQL Data Warehouse a PowerShell használatával.

## Előfeltételek
A kezdéshez a következők szükségesek:

* **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][Ingyenes Azure-fiók létrehozása] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][Havi Azure-kredit a Visual Studio-előfizetőknek].
* **Azure SQL-kiszolgáló**: További részletes információ: [Create an Azure SQL Database logical server with the Azure Portal][Create an Azure SQL Database logical server with the Azure Portal] vagy [Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával][Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával].
* **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, mint az Azure SQL-kiszolgáló, vagy tekintse át az [erőforráscsoportok létrehozásával foglalkozó][erőforráscsoportok létrehozásával foglalkozó] cikket.
* **PowerShell 1.0.3-as vagy újabb verzió**: A verziószámot a **Get-Module -ListAvailable -Name Azure** futtatásával ellenőrizheti.  A legújabb verziót a [Microsoft Webplatform-telepítőből][Microsoft Webplatform-telepítőből] telepítheti.  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  A díjszabással kapcsolatos további információkért lásd: [SQL Data Warehouse díjszabása][SQL Data Warehouse díjszabása].
> 
> 

## SQL Data Warehouse létrehozása
1. Nyissa meg a Windows PowerShellt.
2. A parancsmag futtatásával jelentkezzen be az Azure Resource Managerbe.
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. Válassza ki a jelenlegi munkamenethez használni kívánt előfizetést.
   
    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Hozza létre az adatbázist. Ebben a példában egy adatbázist hozunk létre „mynewsqldw” néven, „DW400” szintű szolgáltatási céllal az „sqldwserver1” elnevezésű kiszolgálóra, amely a „mywesteuroperesgp1” nevű erőforráscsoportban található.
   
   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

A szükséges paraméterek a következők:

* **RequestedServiceObjectiveName**: A kért [DWU][DWU]-mennyiség.  A támogatott értékek a következők: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 és DW6000.
* **DatabaseName**: A létrehozandó SQL Data Warehouse neve.
* **ServerName**: A létrehozás során használt kiszolgáló neve (V12-nek kell lennie).
* **ResourceGroupName**: A használt erőforráscsoport.  Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Edition**: Egy SQL Data Warehouse létrehozásához a „DataWarehouse” értéket kell megadni.

A választható paraméterek a következők:

* **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS.  Az adatbázisok rendezése nem módosítható.
* **MaxSizeBytes**: Alapértelmezés szerint az adatbázisok maximális mérete 10 GB.

A paraméterbeállításokkal kapcsolatos további információk: [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] és [Create Database (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)] (Adatbázis létrehozása (Azure SQL Data Warehouse)).

## Következő lépések
Miután az SQL Data Warehouse kiépítése befejeződött, [mintaadatokat tölthet be][mintaadatokat tölthet be], vagy további részleteket tudhat meg a [fejlesztés][fejlesztés], [betöltés][betöltés] vagy [áttelepítés][áttelepítés] mikéntjéről.

Ha érdekli az SQL Data Warehouse programozott módon való kezelése, tekintse meg a [PowerShell-parancsmagok és a REST API-k][PowerShell-parancsmagok és a REST API-k] használatával kapcsolatos cikkünket.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[áttelepítés]: ./sql-data-warehouse-overview-migrate.md
[fejlesztés]: ./sql-data-warehouse-overview-develop.md
[betöltés]: ./sql-data-warehouse-load-with-bcp.md
[mintaadatokat tölthet be]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell-parancsmagok és a REST API-k]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[tűzfalszabályok]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: ../powershell/powershell-install-configure.md
[SQL Data Warehouse létrehozása az Azure Portalról]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL Database logical server with the Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok létrehozásával foglalkozó]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Webplatform-telepítőből]: https://aka.ms/webpi-azps
[SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-fiók létrehozása]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Havi Azure-kredit a Visual Studio-előfizetőknek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Sep16_HO4-->


