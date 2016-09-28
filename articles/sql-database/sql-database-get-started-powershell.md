<properties
    pageTitle="Új SQL-adatbázis beállítása a PowerShell segítségével | Microsoft Azure"
    description="Ismerje meg, hogyan hozhat létre SQL-adatbázist a PowerShell segítségével. Az általános adatbázis-beállítási feladatok PowerShell-parancsmagokkal is kezelhetőek."
    keywords="új sql-adatbázis létrehozása,adatbázis beállítása"
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
    ms.date="08/19/2016"
    ms.author="sstein"/>


# SQL-adatbázis létrehozása és általános adatbázis-beállítási feladatok elvégzése PowerShell-parancsmagokkal


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C##](sql-database-get-started-csharp.md)



Ismerje meg, hogyan hozhat létre SQL-adatbázist PowerShell-parancsmagok használatával. (Rugalmas adatbázisok létrehozásához lásd: [Új rugalmas adatbáziskészlet létrehozása a PowerShell segítségével](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Adatbázis beállítása: Erőforráscsoport, kiszolgáló és tűzfalszabály létrehozása

Mihelyt engedélye van rá, hogy parancsmagokat futtasson a kiválasztott Azure-előfizetésen, a következő lépésként hozza létre azt az erőforráscsoportot, amely tartalmazza az adatbázis helyéül szolgáló kiszolgálót. A következő parancsban az Ön által választott bármely érvényes helyet megadhatja. Az érvényese helyek listáját a **(Get-AzureRmLocation | Where-Object { $_.Providers -eq "Microsoft.Sql" }).Location** paranccsal jelenítheti meg.

Futtassa az alábbi parancsot egy erőforráscsoport létrehozásához:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### A kiszolgáló létrehozása

Az SQL-adatbázisok Azure SQL Database-kiszolgálókon hozhatók létre. Futtassa a **New-AzureRmSqlServer** parancsot egy kiszolgáló létrehozásához. A kiszolgáló nevének minden Azure SQL Database-kiszolgáló esetében egyedinek kell lennie. Ha a kiszolgálónév már foglalt, hibaüzenet jelenik meg. Érdemes azt is megjegyezni, hogy a parancs végrehajtása több percet is igénybe vehet. A parancsban az Ön által választott bármely érvényes helyet megadhatja, viszont ügyeljen arra, hogy ugyanazt a helyet adja meg, amelyet az előző lépésben az erőforráscsoport létrehozásánál használt.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Amikor futtatja a parancsot, a rendszer a felhasználónév és a jelszó megadását kéri. Ne adja meg Azure hitelesítő adatait. Ehelyett adja meg a kiszolgáló-rendszergazda számára létrehozni kívánt felhasználónevet és jelszót. A cikk alján található szkript bemutatja, hogyan állíthatók be a kiszolgálói hitelesítő adatok a kód használatával.

A kiszolgáló sikeres létrehozása után megjelennek a kiszolgáló részletei.

### A kiszolgálóhoz való hozzáférést engedélyező tűzfalszabály létrehozása

A kiszolgáló eléréséhez egy tűzfalszabályt kell létrehoznia. Futtassa a következő parancsot a kezdő és a záró IP-címeket kicserélve a számítógép által használt valós címmel.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

A tűzfalszabály sikeres létrehozása után megjelennek a tűzfalszabály részletei.

Ha engedélyezni szeretné más Azure-szolgáltatásoknak a kiszolgálóhoz való hozzáférést, vegyen fel egy tűzfalszabályt, és állítsa a StartIpAddress és az EndIpAddress paraméter értékét egyaránt 0.0.0.0.-ra. Ez a szabály bármely Azure-előfizetésből engedélyezi a kiszolgálón az Azure-forgalmat.

További információ: [Azure SQL Database-tűzfal](sql-database-firewall-configure.md).


## SQL-adatbázis létrehozása

Ezen a ponton rendelkezik egy erőforráscsoporttal, egy kiszolgálóval, valamint egy tűzfalszabállyal, amely lehetővé teszi a hozzáférést a kiszolgálóhoz.

A következő parancs létrehoz egy (üres) SQL-adatbázist a Standard szolgáltatásszinten, S1 teljesítményszinttel:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Az adatbázis sikeres létrehozása után megjelennek az adatbázis részletei.

## SQL-adatbázis létrehozása PowerShell-szkripttel

A következő PowerShell-szkript létrehoz egy SQL-adatbázist, valamint annak minden függő erőforrását. Az összes `{variables}` elemet cserélje le az előfizetéshez és az erőforrásokhoz tartozó értékekre (az értékek megadásakor törölje a **{}** karaktereket).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Következő lépések
Az SQL-adatbázis létrehozása és az általános adatbázis-beállítási feladatok elvégzése után készen áll a következőre:

- [Az SQL Database kezelése PowerShell használatával](sql-database-command-line-tools.md)
- [Csatlakozás az SQL Database-hez az SQL Server Management Studio segítségével, és T-SQL-mintalekérdezés futtatása](sql-database-connect-query-ssms.md)


## További források

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Sep16_HO4-->


