---
title: "SQL Data Warehouse létrehozása PowerShell használatával | Microsoft Docs"
description: "SQL Data Warehouse létrehozása PowerShell használatával"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3d13d4a0dd1d6e0b7361a57e167b06f0b717bfb4


---
# <a name="create-sql-data-warehouse-using-powershell"></a>SQL Data Warehouse létrehozása PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Ebből a cikkből megtudhatja, hogyan hozható létre az SQL Data Warehouse a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következők szükségesek:

* **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-próbalehetőség][Ingyenes Azure-próbalehetőség] vagy [MSDN Azure-kreditek][MSDN Azure-kreditek].
* **Azure SQL-kiszolgáló**: További részletes információ: [Azure SQL Database logikai kiszolgáló létrehozása az Azure Portal segítségével][Azure SQL Database logikai kiszolgáló létrehozása az Azure Portal segítségével] vagy [Azure SQL Database logikai kiszolgáló létrehozása a PowerShell segítségével][Azure SQL Database logikai kiszolgáló létrehozása a PowerShell segítségével].
* **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, mint az Azure SQL-kiszolgáló, vagy tekintse át az [erőforráscsoportok létrehozásával foglalkozó][erőforráscsoportok létrehozása] cikket.
* **PowerShell 1.0.3-as vagy újabb verzió**: A verziószámot a **Get-Module -ListAvailable -Name Azure** futtatásával ellenőrizheti.  A legújabb verziót a [Microsoft Webplatform-telepítőből][Microsoft Webplatform-telepítőből] telepítheti.  A legújabb verzió telepítésével kapcsolatban lásd: [Az Azure PowerShell telepítése és konfigurálása][Az Azure PowerShell telepítése és konfigurálása].

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  1A díjszabással kapcsolatos további információkért lásd: [SQL Data Warehouse díjszabása][SQL Data Warehouse díjszabása].
> 
> 

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse létrehozása
1. Nyissa meg a Windows PowerShellt.
2. A parancsmag futtatásával jelentkezzen be az Azure Resource Managerbe.
   
    ```Powershell
    Login-AzureRmAccount
    ```
3. Válassza ki a jelenlegi munkamenethez használni kívánt előfizetést.
   
    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
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

A paraméterbeállításokkal kapcsolatos további információk: [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] és [Adatbázis létrehozása (Azure SQL Data Warehouse)][Adatbázis létrehozása (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Következő lépések
Miután az SQL Data Warehouse kiépítése befejeződött, [mintaadatokat tölthet be][mintaadatok betöltése], vagy további részleteket tudhat meg a [fejlesztés][fejlesztés], [betöltés][betöltés] vagy [áttelepítés][áttelepítés] mikéntjéről.

Ha érdekli az SQL Data Warehouse programozott módon való kezelése, tekintse meg a [PowerShell-parancsmagok és a REST API-k][PowerShell-parancsmagok és a REST API-k] használatával kapcsolatos cikkünket.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[áttelepítés]: ./sql-data-warehouse-overview-migrate.md
[fejlesztés]: ./sql-data-warehouse-overview-develop.md
[betöltés]: ./sql-data-warehouse-load-with-bcp.md
[mintaadatok betöltése]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell-parancsmagok és a REST API-]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[tűzfalszabályok]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)]: ../powershell-install-configure.md
[SQL Data Warehouse létrehozása az Azure Portalról]: ./sql-data-warehouse-get-started-provision.md
[Azure SQL Database logikai kiszolgáló létrehozása az Azure Portal segítségével]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Azure SQL Database logikai kiszolgáló létrehozása PowerShell segítségével]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[erőforráscsoportok létrehozása]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Adatbázis létrehozása (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Webplatform-telepítőből]: https://aka.ms/webpi-azps
[Az SQL Data Warehouse díjszabása]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Ingyenes Azure-próbalehetőség]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-kreditek]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


