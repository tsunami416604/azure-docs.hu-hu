---
title: 'Oktatóanyag: Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz'
description: Adjon hozzá egy Azure SQL Database egyetlen adatbázist egy feladatátvételi csoporthoz az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061698"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database egyetlen adatbázis hozzáadása feladatátvételi csoporthoz

A [feladatátvételi csoport](sql-database-auto-failover-group.md) egy deklaratív absztrakciós réteg, amely lehetővé teszi a többmint földhöz kötött georeplikált adatbázisok csoportosítását. Ismerje meg, hogyan konfigurálhat feladatátvételi csoportot egy Azure SQL Database egyetlen adatbázishoz, és tesztelje a feladatátvételt az Azure Portalon, a PowerShellen vagy az Azure CLI-n keresztül.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database egyetlen adatbázist.
> - Feladatátvételi csoport létrehozása két logikai SQL-kiszolgáló közötti egyetlen adatbázishoz.
> - Feladatátvétel tesztelése.

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Az oktatóanyag befejezéséhez győződjön meg arról, hogy a következő elemekkel rendelkezik:

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az oktatóanyag befejezéséhez győződjön meg arról, hogy a következő elemekkel rendelkezik:

- Azure-előfizetés. [Hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) ha még nem rendelkezik ilyen.
- Az Azure [CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)legújabb verziója. 

---

## <a name="1---create-a-single-database"></a>1 - Egyetlen adatbázis létrehozása 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - A feladatátvételi csoport létrehozása 
Ebben a lépésben egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) hoz létre egy meglévő Azure SQL-kiszolgáló és egy másik régióban lévő új Azure SQL-kiszolgáló között. Ezután adja hozzá a mintaadatbázist a feladatátvételi csoporthoz. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist az Azure Portalhasználatával. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza ki az `mySampleDatabase`1. 
1. A feladatátvételi csoportok kiszolgálószinten konfigurálhatók. A **kiszolgáló beállításainak** megnyitásához a Kiszolgáló neve csoportban válassza ki a kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Feladatátvételi csoportok** lehetőséget a **Beállítások** ablaktáblán, majd a **Csoport hozzáadása lehetőséget** új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **Feladatátvevő csoport** lapon adja meg vagy jelölje ki a következő értékeket, majd válassza a **Létrehozás**lehetőséget:
    - **Feladatátvételi csoport neve**: Írjon be `failovergrouptutorial`egy egyedi feladatátvételi csoport nevét, például . 
    - **Másodlagos kiszolgáló**: Válassza ki a *kívánt beállítások konfigurálásának* lehetőségét, majd válassza **az Új kiszolgáló létrehozása**lehetőséget. Másik lehetőségként másodlagos kiszolgálóként választhat egy már meglévő kiszolgálót. A következő értékek megadása után válassza a **Kijelölés lehetőséget.** 
        - **Kiszolgáló neve**: Írja be a másodlagos kiszolgáló `mysqlsecondary`egyedi nevét, például . 
        - **Kiszolgálórendszergazdai bejelentkezés:** Írja be a típust`azureuser`
        - **Jelszó**: Írjon be egy összetett jelszót, amely megfelel a jelszókövetelményeknek.
        - **Hely**: Válasszon egy helyet a `East US`legördülő menüből, például . Ez a hely nem lehet ugyanaz a hely, mint az elsődleges kiszolgáló.

    > [!NOTE]
    > A kiszolgáló bejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 
    
      ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoportszámára](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **A csoporton belüli adatbázisok**: Ha egy másodlagos kiszolgáló tikket, ez a beállítás feloldva lesz. Jelölje ki **a Hozzáadni kívánt adatbázisok kiválasztása** lehetőséget, majd válassza ki az 1. Az adatbázis hozzáadása a feladatátvételi csoporthoz automatikusan elindítja a georeplikációs folyamatot. 
        
    ![SQL DB hozzáadása feladatátvételi csoporthoz](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist a PowerShell használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabályt hoz létre egy logikai kiszolgálóhoz. | 
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Új Azure SQL Database egyetlen adatbázist hoz létre. | 
| [Új-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre. |
| [Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase) | Beggyelelt egy vagy több SQL-adatbázist. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis hozzáadása egy feladatátvételi csoporthoz. |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist az AZ CLI használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Az oktatóanyag ezen része a következő Az CLI-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [az sql server tűzfal-szabály létrehozása](/cli/azure/sql/server/firewall-rule) | Létrehozza a kiszolgáló tűzfalszabályait. | 
| [az sql feladatátvételi csoport létrehozása](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Feladatátvételi csoportot hoz létre. | 

---

## <a name="3---test-failover"></a>3 - Feladatátvétel tesztje 
Ebben a lépésben sikertelen lesz a feladatátvételi csoport a másodlagos kiszolgálóra, majd az Azure Portal használatával. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Feladatátvétel tesztelése az Azure Portalhasználatával. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Válassza ki a `mySampleDatbase`2. 
1. A **kiszolgáló beállításainak** megnyitásához a Kiszolgáló neve csoportban válassza ki a kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Feladatátvételi csoportok lehetőséget** a **Beállítások** ablaktáblán, majd válassza ki a 2. 
  
   ![Válassza ki a feladatátvételi csoportot a portálról](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges és melyik másodlagos kiszolgáló. 
1. Válassza **a feladatátvétel** lehetőséget a munkaablakból, ha feladatátvételi a minta egyetlen adatbázist tartalmazó feladatátvételi csoport. 
1. Válassza az **Igen** lehetőséget a figyelmeztetésen, amely értesíti, hogy a TDS-munkamenetek megszakadnak. 

   ![Feladatátvétel az SQL-adatbázist tartalmazó feladatátvételi csoport felett](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, és melyik másodlagos kiszolgáló. Ha a feladatátvétel sikeres volt, a két kiszolgálónak szerepkört kellett cserélnie. 
1. Válassza **a Feladatátvétel** lehetőséget, ha a kiszolgálók visszaállnak az eredeti szerepkörükbe. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Feladatátvétel tesztelése a PowerShell használatával. 


Ellenőrizze a másodlagos replika szerepét: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Feladatátvétel a másodlagos kiszolgálónak: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Leésezése vagy listázása Az Azure SQL Database feladatátvételi csoportok. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Egy Azure SQL Database feladatátvételi csoport feladatátvételt hajt végre. |



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az AZ CLI használatával a feladatátvétel tesztelése. 

Ellenőrizze, hogy melyik kiszolgáló a másodlagos:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Feladatátvétel a másodlagos kiszolgálónak: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Az oktatóanyag ezen része a következő Az CLI-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az sql feladatátvételi csoport listája](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | A kiszolgáló feladatátvételi csoportjainak listája. |
| [az sql feladatátvételi csoport set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődleges beállítását az aktuális elsődleges kiszolgáló összes adatbázisának feladatátvételével. | 

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Az erőforráscsoport törlésével törölje az erőforrásokat. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Törölje az erőforráscsoportot az Azure Portal használatával. 

1. Nyissa meg az erőforráscsoportot az [Azure Portalon.](https://portal.azure.com)
1. Válassza **az Erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint magának az erőforráscsoportnak a törléséhez. 
1. Írja be az erőforráscsoport `myResourceGroup`nevét , a mezőbe, majd válassza a **Törlés** gombot az erőforráscsoport törléséhez.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Törölje az erőforráscsoportot a PowerShell használatával. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Az oktatóanyag ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Törölje az erőforráscsoportot az AZ CLI használatával. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Az oktatóanyag ezen része a következő Az CLI-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

---


> [!IMPORTANT]
> Ha meg szeretné tartani az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, törlés előtt távolítsa el azt a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, az kiszámíthatatlan viselkedést okozhat. 


## <a name="full-scripts"></a>Teljes szkriptek

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabályt hoz létre egy logikai kiszolgálóhoz. | 
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Új Azure SQL Database egyetlen adatbázist hoz létre. | 
| [Új-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Új feladatátvételi csoportot hoz létre. |
| [Get-AzSqlDatabase adatbázis](/powershell/module/az.sql/get-azsqldatabase) | Beggyelelt egy vagy több SQL-adatbázist. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis hozzáadása egy feladatátvételi csoporthoz. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Leésezése vagy listázása Az Azure SQL Database feladatátvételi csoportok. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Egy Azure SQL Database feladatátvételi csoport feladatátvételt hajt végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az számlakészlet](/cli/azure/account?view=azure-cli-latest#az-account-set) | Beállítja az előfizetést az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [az sql server tűzfal-szabály létrehozása](/cli/azure/sql/server/firewall-rule) | Létrehozza a kiszolgáló tűzfalszabályait. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Létrehoz egy adatbázist. | 
| [az sql feladatátvételi csoport létrehozása](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Feladatátvételi csoportot hoz létre. | 
| [az sql feladatátvételi csoport listája](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | A kiszolgáló feladatátvételi csoportjainak listája. |
| [az sql feladatátvételi csoport set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődleges beállítását az aktuális elsődleges kiszolgáló összes adatbázisának feladatátvételével. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

# <a name="portal"></a>[Portál](#tab/azure-portal)
Nincsenek elérhető parancsfájlok az Azure Portalon. 
 
---

Az Azure SQL Database-parancsfájlokat itt találja: [Az Azure PowerShell](sql-database-powershell-samples.md) és az [Azure CLI.](sql-database-cli-samples.md) 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure SQL Database egyetlen adatbázist adott hozzá egy feladatátvételi csoporthoz, és teszteltfeladat-átvételt. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database egyetlen adatbázist. 
> - [Feladatátvételi csoport](sql-database-auto-failover-group.md) létrehozása két logikai SQL-kiszolgáló közötti egyetlen adatbázishoz.
> - Feladatátvétel tesztelése.

Ugrás a következő oktatóanyag, hogyan adja hozzá a rugalmas készlet egy feladatátvételi csoporthoz. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
