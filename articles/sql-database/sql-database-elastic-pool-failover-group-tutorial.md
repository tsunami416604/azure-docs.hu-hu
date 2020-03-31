---
title: 'Oktatóanyag: Rugalmas készlet hozzáadása feladatátvételi csoporthoz'
description: Adjon hozzá egy Azure SQL Database rugalmas készletet egy feladatátvételi csoporthoz az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268976"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz

Konfiguráljon egy feladatátvételi csoportot egy Azure SQL Database rugalmas készlethez, és tesztelje a feladatátvételt az Azure Portalhasználatával.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database egyetlen adatbázist.
> - Adja hozzá az egyetlen adatbázist egy rugalmas készletbe. 
> - [Feladatátvételi csoport](sql-database-auto-failover-group.md) létrehozása két rugalmas készlethez két logikai SQL-kiszolgáló között.
> - Feladatátvétel tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.


## <a name="1---create-a-single-database"></a>1 - Egyetlen adatbázis létrehozása 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Egyetlen adatbázis hozzáadása a rugalmas készlethez
Ebben a lépésben hozzon létre egy rugalmas készletet, és adja hozzá az egyetlen adatbázist. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozzon létre rugalmas készletet az Azure Portal használatával. 


1. Válassza az **Azure SQL** az Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza a **+ Add lehetőséget** az SQL kiválasztása telepítési **lehetőség** lap megnyitásához. A különböző adatbázisokra vonatkozó további információkat az Adatbázisok csempén a Részletek megjelenítése lehetőséget választva tekintheti meg.
1. Válassza ki a **Rugalmas készletet** az **SQL-adatbázisok** csempén található **Erőforrástípus** legördülő listából. A rugalmas készlet létrehozásához válassza a **Létrehozás** lehetőséget. 

    ![Rugalmas készlet kiválasztása](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurálja a rugalmas készletet a következő értékekkel:
   - **Név**: Adjon meg egyedi nevet a `myElasticPool`rugalmas készletnek, például . 
   - **Előfizetés**: Válassza ki az előfizetést a legördülő menüből.
   - **Erőforráscsoport**: `myResourceGroup` Válassza ki a legördülő menüből az 1. 
   - **Kiszolgáló**: Válassza ki az 1.  

       ![Új kiszolgáló létrehozása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Számítás + tárolás:** Válassza **a Rugalmas készlet konfigurálása** a számítási, tárolási, és adja hozzá az egyetlen adatbázisa a rugalmas készlet. A **Készletbeállítások** lapon hagyja meg a Gen5 alapértelmezett, 2 virtuális maggal és 32 gb-os magokkal. 

1. A **Konfigurálás** lapon válassza az **Adatbázisok** lapot, majd válassza az **Adatbázis hozzáadása lehetőséget.** Válassza ki az 1. **Apply** A rugalmas készletbeállításainak alkalmazásához és a **Konfigurálás** lap bezárásához válassza ismét az **Alkalmaz** lehetőséget. 

    ![SQL DB hozzáadása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Válassza **a Véleményezés + létrehozás** lehetőséget a rugalmas készlet beállításainak áttekintéséhez, majd a **Létrehozás gombra** a rugalmas készlet létrehozásához. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Rugalmas készletek és a másodlagos kiszolgáló a PowerShell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Új-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbáziskészletet hoz létre egy Azure SQL-adatbázishoz.| 
| [Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase) | Beállítja az adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe. | 

---

## <a name="3---create-the-failover-group"></a>3 - A feladatátvételi csoport létrehozása 
Ebben a lépésben egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) hoz létre egy meglévő Azure SQL-kiszolgáló és egy másik régióban lévő új Azure SQL-kiszolgáló között. Ezután adja hozzá a rugalmas készletet a feladatátvételi csoporthoz. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot az Azure Portalon. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza ki az előző szakaszban létrehozott `myElasticPool`rugalmas készletet, például a . 
1. Az **Áttekintő** ablaktáblán válassza ki a kiszolgáló nevét a **Kiszolgáló neve** csoportban a kiszolgáló beállításainak megnyitásához.
  
    ![Kiszolgáló megnyitása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Válassza a **Feladatátvételi csoportok** lehetőséget a **Beállítások** ablaktáblán, majd a **Csoport hozzáadása lehetőséget** új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. A **Feladatátvevő csoport** lapon adja meg vagy jelölje ki a következő értékeket, majd válassza a **Létrehozás**lehetőséget:
    - **Feladatátvételi csoport neve**: Írjon be `failovergrouptutorial`egy egyedi feladatátvételi csoport nevét, például . 
    - **Másodlagos kiszolgáló**: Válassza ki a *kívánt beállítások konfigurálásának* lehetőségét, majd válassza **az Új kiszolgáló létrehozása**lehetőséget. Másik lehetőségként másodlagos kiszolgálóként választhat egy már meglévő kiszolgálót. Miután megadta az új másodlagos kiszolgáló következő értékeit, válassza a **Kijelölés lehetőséget.** 
        - **Kiszolgáló neve**: Írja be a másodlagos kiszolgáló `mysqlsecondary`egyedi nevét, például . 
        - **Kiszolgálórendszergazdai bejelentkezés:** Írja be a típust`azureuser`
        - **Jelszó**: Írjon be egy összetett jelszót, amely megfelel a jelszókövetelményeknek.
        - **Hely**: Válasszon egy helyet a `East US`legördülő menüből, például . Ez a hely nem lehet ugyanaz a hely, mint az elsődleges kiszolgáló.

       > [!NOTE]
       > A kiszolgáló bejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 
    
       ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoportszámára](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Válassza **az adatbázisok a csoporton belül,** majd válassza ki a 2. Egy figyelmeztetés jelenik meg, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Jelölje ki a figyelmeztetést, majd az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 
        
    ![Rugalmas készlet hozzáadása feladatátvételi csoporthoz](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Válassza **a Kijelölés lehetőséget,** ha a rugalmas készlet beállításait a feladatátvételi csoportra szeretné alkalmazni, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet hozzáadása a feladatátvételi csoporthoz automatikusan elindítja a georeplikációs folyamatot.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot a PowerShell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabályt hoz létre egy logikai kiszolgálóhoz. | 
| [Új-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbáziskészletet hoz létre egy Azure SQL-adatbázishoz.| 
| [Új-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis hozzáadása egy feladatátvételi csoporthoz. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Leésezése vagy listázása Az Azure SQL Database feladatátvételi csoportok. |

---


## <a name="4---test-failover"></a>4 - Feladatátvétel tesztje 
Ebben a lépésben sikertelen lesz a feladatátvételi csoport a másodlagos kiszolgálóra, majd az Azure Portal használatával. 


# <a name="portal"></a>[Portál](#tab/azure-portal)

Tesztelje a feladatátvételi csoport feladatátvételi az Azure Portalon keresztül. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza ki az előző szakaszban létrehozott `myElasticPool`rugalmas készletet, például a . 
1. A **kiszolgáló beállításainak** megnyitásához a Kiszolgáló neve csoportban válassza ki a kiszolgáló nevét.

    ![Kiszolgáló megnyitása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Válassza a **Feladatátvételi csoportok lehetőséget** a **Beállítások** ablaktáblán, majd válassza ki a 2. 
  
   ![Válassza ki a feladatátvételi csoportot a portálról](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, és melyik kiszolgáló másodlagos. 
1. Válassza **a feladatátvétel** lehetőséget a munkaablakból a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételi csoportát. 
1. Válassza az **Igen** lehetőséget a figyelmeztetésen, amely értesíti, hogy a TDS-munkamenetek megszakadnak. 

   ![Feladatátvétel az SQL-adatbázist tartalmazó feladatátvételi csoport felett](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, melyik kiszolgáló másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak szerepköröket kellett cserélnie. 
1. Válassza **a Feladatátvétel** lehetőséget, ha a feladatátvételi csoportot vissza szeretné adni az eredeti beállításokhoz. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Tesztelje a feladatátvételi csoport feladatátvételi csoport powershell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

A feladatátvételi csoport átad a másodlagos kiszolgálóra, majd a PowerShell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Leésezése vagy listázása Az Azure SQL Database feladatátvételi csoportok. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Egy Azure SQL Database feladatátvételi csoport feladatátvételt hajt végre. |


---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Az erőforráscsoport törlésével törölje az erőforrásokat. 


# <a name="portal"></a>[Portál](#tab/azure-portal)


1. Nyissa meg az erőforráscsoportot az [Azure Portalon.](https://portal.azure.com)
1. Válassza **az Erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint magának az erőforráscsoportnak a törléséhez. 
1. Írja be az erőforráscsoport `myResourceGroup`nevét , a mezőbe, majd válassza a **Törlés** gombot az erőforráscsoport törléséhez. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az erőforrások karbantartása a PowerShell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmast használja:

| Parancs | Megjegyzések |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 

---

> [!IMPORTANT]
> Ha meg szeretné tartani az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, törlés előtt távolítsa el azt a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, az kiszámíthatatlan viselkedést okozhat. 

## <a name="full-script"></a>Teljes szkript

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabályt hoz létre egy logikai kiszolgálóhoz. | 
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Új Azure SQL Database egyetlen adatbázist hoz létre. | 
| [Új-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Rugalmas adatbáziskészletet hoz létre egy Azure SQL-adatbázishoz.| 
| [Set-AzSqlDatabase Adatbázis](/powershell/module/az.sql/set-azsqldatabase) | Beállítja az adatbázis tulajdonságait, vagy áthelyez egy meglévő adatbázist egy rugalmas készletbe. | 
| [Új-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre. |
| [Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase) | Beggyelelt egy vagy több SQL-adatbázist. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis hozzáadása egy feladatátvételi csoporthoz. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Leésezése vagy listázása Az Azure SQL Database feladatátvételi csoportok. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Egy Azure SQL Database feladatátvételi csoport feladatátvételt hajt végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 


# <a name="portal"></a>[Portál](#tab/azure-portal)
Nincsenek elérhető parancsfájlok az Azure Portalon.

---

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure SQL Database rugalmas készletet adott hozzá egy feladatátvételi csoporthoz, és tesztelt feladatátvételt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database egyetlen adatbázist.
> - Adja hozzá az egyetlen adatbázist egy rugalmas készletbe. 
> - [Feladatátvételi csoport](sql-database-auto-failover-group.md) létrehozása két rugalmas készlethez két logikai SQL-kiszolgáló között.
> - Feladatátvétel tesztelése.

Tovább a következő oktatóanyaghoz a DMS használatával történő áttelepítésről.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az SQL Server áttelepítése készletezésű adatbázisba A DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
