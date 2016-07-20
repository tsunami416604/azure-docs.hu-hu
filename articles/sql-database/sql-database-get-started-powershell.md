<properties 
    pageTitle="Új SQL-adatbázis beállítása a PowerShell segítségével | Microsoft Azure" 
    description="Ismerje meg, hogyan hozhat létre új SQL-adatbázist a PowerShell segítségével. Az általános adatbázis-beállítási feladatok PowerShell-parancsmagokkal is kezelhetőek." 
    keywords="create new sql database,database setup"
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Új SQL-adatbázis létrehozása és általános adatbázis-beállítási feladatok elvégzése PowerShell-parancsmagokkal 


> [AZURE.SELECTOR]
- [Azure-portál](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Ismerje meg, hogyan hozhat létre új SQL-adatbázist a PowerShell-parancsmagok használatával. (Rugalmas adatbázisok létrehozásához lásd: [Új rugalmas adatbáziskészlet létrehozása a PowerShell segítségével](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Adatbázis beállítása: Erőforráscsoport, kiszolgáló és tűzfalszabály létrehozása

Ezek után parancsmagokat futtathat a kiválasztott Azure-előfizetésén, ezért következő lépésként hozza létre azt az erőforráscsoportot, ami tartalmazza az adatbázis helyéül szolgáló kiszolgálót. A következő parancsban az Ön által választott bármely érvényes helyet megadhatja. Az érvényese helyek listáját a **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** paranccsal jelenítheti meg.

Futtassa a következő parancsot új erőforráscsoport létrehozásához:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Az új erőforráscsoport sikeres létrehozása után a következő információnak kell megjelennie a képernyőn: **ProvisioningState: Succeeded**


### A kiszolgáló létrehozása 

Az SQL-adatbázisok Azure SQL Database-kiszolgálókon hozhatók létre. Futtassa a **New-AzureRmSqlServer** parancsot az új kiszolgáló létrehozásához. Helyettesítse a ServerName értéket a kiszolgáló nevével. Minden Azure SQL-kiszolgáló egyedi nevet használ, ezért ha már foglalt kiszolgálónevet választ, hibaüzenet fog megjelenni. Érdemes azt is megjegyezni, hogy a parancs végrehajtása több percet is igénybe vehet. A parancsban az Ön által választott bármely érvényes helyet megadhatja, viszont ügyeljen arra, hogy ugyanazt a helyet adja meg, amelyet az előző lépésben az erőforráscsoport létrehozásánál használt.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

A parancs futtatásakor megnyílik egy ablak, amely **Felhasználónév** és **Jelszó** megadását kéri. Itt nem az Azure-beli hitelesítő adatait kell megadnia, hanem az új kiszolgáló rendszergazdai hitelesítői adataiként szolgáló felhasználónevet és jelszót.

A kiszolgáló sikeres létrehozása után megjelennek a kiszolgáló részletei.

### A kiszolgálóhoz való hozzáférést engedélyező tűzfalszabály létrehozása

Hozzon létre egy tűzfalszabályt a kiszolgáló eléréséhez. Futtassa a következő parancsot a kezdő és a záró IP-címeket kicserélve a számítógép által használt valós címmel.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

A tűzfalszabály sikeres létrehozása után megjelennek a tűzfalszabály részletei.

Ha engedélyezni szeretné más Azure-szolgáltatásoknak a kiszolgálóhoz való hozzáférést, vegyen fel egy tűzfalszabályt, és állítsa a StartIpAddress és az EndIpAddress paraméter értékét egyaránt 0.0.0.0.-ra. Vegye figyelembe, hogy ezzel bármely Azure-előfizetésből engedélyezi a kiszolgálón az Azure-forgalmat.

További információ: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).


## Új SQL-adatbázis létrehozása

Ezen a ponton rendelkezik egy erőforráscsoporttal, egy kiszolgálóval, valamint egy tűzfalszabállyal, amely lehetővé teszi a hozzáférést a kiszolgálóhoz.

A következő parancs létrehoz egy új (üres) SQL-adatbázist a Standard szolgáltatásszinten, S1 teljesítményszinttel:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Az adatbázis sikeres létrehozása után megjelennek az adatbázis részletei.

## Új SQL-adatbázis létrehozására szolgáló PowerShell-szkript

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Következő lépések
Az új SQL-adatbázis létrehozása és az általános adatbázis-beállítási feladatok elvégzése után készen áll a következőre:

- [Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása](sql-database-connect-query-ssms.md)


## További források

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Jun16_HO2-->


