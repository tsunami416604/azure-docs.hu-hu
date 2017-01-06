---
title: "Új rugalmas készlet létrehozása a PowerShell-lel | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogy a PowerShell-lel hogyan skálázhatja fel horizontálisan az Azure SQL Database-erőforrásokat egy több adatbázist kezelő, skálázható rugalmas készlet létrehozásával."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 37a707ee-9223-43ae-8c35-1ccafde8b83e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: 679cdacc3782f667635a586df4cd77a9af7884d9


---
# <a name="create-a-new-elastic-pool-with-powershell"></a>Új rugalmas készlet létrehozása a PowerShell-lel
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>
>

Ebből a cikkből megtudhatja, hogyan hozhat létre [rugalmas készletet](sql-database-elastic-pool.md) PowerShell-parancsmagokkal.

A gyakori hibakódokat megtalálja az [SQL error codes for SQL Database client applications: Database connection error and other issues](sql-database-develop-error-messages.md) (SQL Database-ügyfélalkalmazások SQL-hibakódjai: adatbázis-kapcsolati és más hibák) című cikkben.

> [!NOTE]
> A rugalmas készletek minden Azure-régióban általánosan elérhetők, kivéve az USA északi középső régióját és Nyugat-Indiát, ahol a szolgáltatás jelenleg előzetes verzióként érhető el.  A rugalmas készletek általános elérhetősége ezekben a régiókban a lehető leghamarabb bevezetésre kerül. Emellett a rugalmas készletek jelenleg nem támogatják a [memóriában végzett OLTP-feldolgozást és elemzéseket](sql-database-in-memory.md) használó adatbázisokat.
>
>

Az Azure PowerShell 1.0-s vagy újabb verziójának kell futnia. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="create-a-new-pool"></a>Új készlet létrehozása
A [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) parancsmag egy új készletet hoz létre. A készletenkénti eDTU-értékek, valamint a DTU-k minimális és maximális értékei a szolgáltatásszinttől (alapszintű, standard vagy prémium) függenek. Lásd: [Rugalmas készletek és rugalmas adatbázisok eDTU- és tárterületi korlátozásai](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Új rugalmas adatbázis létrehozása a készletben
Használja a [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) parancsmagot. Az **ElasticPoolName** paraméter értékeként a célkészletet adja meg. További információ a már létező adatbázisok készletbe való áthelyezéséről: [Adatbázis áthelyezése rugalmas készletbe](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Készlet létrehozása és feltöltése több új adatbázissal
A portál vagy PowerShell-parancsmagok használatával egyszerre csak egyetlen adatbázist hozhat létre. Nagy számú adatbázis készletben való létrehozása ezzel a módszerrel hosszabb időt vehet igénybe. Az új készletben való létrehozás automatizálásához lásd: [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Példa: Készlet létrehozása a PowerShell használatával
Ez a parancsfájl egy új Azure-erőforráscsoportot és egy új kiszolgálót hoz létre. Ha a rendszer kéri, adja meg egy rendszergazdai jogosultságú fiók felhasználónevét és jelszavát (ne az Azure-beli hitelesítő adatait) az új kiszolgáló számára.

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Következő lépések
* [Készlet kezelése](sql-database-elastic-pool-manage-powershell.md)
* [Rugalmas feladat létrehozása](sql-database-elastic-jobs-overview.md): a rugalmas feladatok lehetővé teszik a T-SQL-szkriptek használatát a készletben lévő tetszőleges számú adatbázishoz.
* [Horizontális felskálázás az Azure SQL Database-ben](sql-database-elastic-scale-introduction.md): horizontális skálázáshoz rugalmas adatbáziseszközöket használhat.



<!--HONumber=Jan17_HO1-->


