---
title: 'Oktatóanyag: egyetlen adatbázis hozzáadása egy feladatátvételi csoporthoz'
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával vegyen fel egy Azure SQL Database önálló adatbázist egy feladatátvételi csoportba.
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
ms.openlocfilehash: b88557468c386bc07c2432e154a82fd1f4fcb438
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380011"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database önálló adatbázis hozzáadása feladatátvételi csoporthoz

A feladatátvételi csoportot konfigurálhatja egy Azure SQL Database önálló adatbázishoz, és feladatátvételi tesztet hajthat végre a Azure Portal, a PowerShell vagy az Azure CLI használatával.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist.
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egyetlen adatbázishoz két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

# <a name="portal"></a>[Portal](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- Az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)legújabb verziója. 

---

## <a name="1---create-a-single-database"></a>1 – önálló adatbázis létrehozása 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – a feladatátvételi csoport létrehozása 
Ebben a lépésben létre fog hozni egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egy meglévő Azure SQL Server-kiszolgáló és egy másik régióban lévő új Azure SQL Server között. Ezután adja hozzá a mintaadatbázis a feladatátvételi csoporthoz. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá az önálló adatbázisát a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki az 1. szakaszban létrehozott önálló adatbázist, például `mySampleDatabase`. 
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás**lehetőséget:
    - **Feladatátvételi csoport neve**: adjon meg egy egyedi feladatátvételi csoport nevét, például `failovergrouptutorial`. 
    - **Másodlagos kiszolgáló**: válassza a *szükséges beállítások konfigurálását* , majd válassza az **új kiszolgáló létrehozása**lehetőséget. Másik lehetőségként már meglévő kiszolgálót is választhat másodlagos kiszolgálóként. A következő értékek beírása után válassza a **kiválasztás**lehetőséget. 
        - **Kiszolgáló neve**: írjon be egy egyedi nevet a másodlagos kiszolgálónak, például `mysqlsecondary`. 
        - **Kiszolgáló-rendszergazdai bejelentkezés**: típus `azureuser`
        - **Password (jelszó**): írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
        - **Hely**: válasszon ki egy helyet a legördülő menüből, például `East US`. Ez a hely nem lehet ugyanazon a helyen, mint az elsődleges kiszolgáló.

    > [!NOTE]
    > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 
    
      ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoport számára](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Adatbázisok a csoporton belül**: Ha egy másodlagos kiszolgáló van kiválasztva, akkor ez a beállítás fel lesz oldva. Válassza ki a **hozzáadni kívánt adatbázisokat** , majd válassza ki az 1. szakaszban létrehozott adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a automatikusan elindítja a Geo-replikálási folyamatot. 
        
    ![SQL-adatbázis hozzáadása a feladatátvételi csoporthoz](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot, és adja hozzá egyetlen adatbázisát a PowerShell használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 

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
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása logikai kiszolgálóhoz. | 
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy új Azure SQL Database önálló adatbázist. | 
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több SQL-adatbázis beolvasása. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Hozza létre a feladatátvételi csoportot, és adja hozzá egyetlen adatbázisát az AZ CLI használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 

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

Az oktatóanyag ezen része a következő az CLI parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. | 
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Létrehoz egy feladatátvételi csoportot. | 

---

## <a name="3---test-failover"></a>3 – feladatátvételi teszt 
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Feladatátvételi teszt a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki a 2. szakaszban létrehozott önálló adatbázist, például `mySampleDatbase`. 
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálon](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a minta önálló adatbázist tartalmazó feladatátvételi csoport feladatátvételéhez. 
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva. 

   ![Az SQL-adatbázist tartalmazó feladatátvételi csoport feladatátvétele](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló legyen az elsődleges, és melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie. 
1. Válassza újra a **feladatátvételt** , hogy a kiszolgálókat visszaállítsa az eredeti szerepköreire. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Feladatátvételi teszt a PowerShell használatával. 


Vizsgálja meg a másodlagos replika szerepkörét: 

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

Feladatátvétel a másodlagos kiszolgálóra: 

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

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

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
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Feladatátvételi teszt az az CLI használatával. 

Ellenőrizze, hogy melyik kiszolgáló a másodlagos:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Feladatátvétel a másodlagos kiszolgálóra: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Az oktatóanyag ezen része a következő az CLI parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az SQL feladatátvételi csoport listája](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Felsorolja a kiszolgálók feladatátvételi csoportjait. |
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődlegesét úgy, hogy az a jelenlegi elsődleges kiszolgáló összes adatbázisát elvégzi. | 

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Törölje az erőforrásokat az erőforráscsoport törlésével. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Törölje az erőforráscsoportot a Azure Portal használatával. 

1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza az **erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint maga az erőforráscsoport törléséhez. 
1. Írja be az erőforráscsoport nevét, `myResourceGroup`, a szövegmezőbe, majd válassza a **Törlés** lehetőséget az erőforráscsoport törléséhez.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

Az oktatóanyag ezen része a következő az CLI parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

---


> [!IMPORTANT]
> Ha meg kívánja őrizni az erőforráscsoportot, de törölni szeretné a másodlagos adatbázist, akkor a törlés előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, akkor kiszámíthatatlan viselkedést okozhat. 


## <a name="full-scripts"></a>Teljes parancsfájlok

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása logikai kiszolgálóhoz. | 
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy új Azure SQL Database önálló adatbázist. | 
| [Új – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Létrehoz egy új feladatátvételi csoportot. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Egy vagy több SQL-adatbázis beolvasása. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Egy vagy több Azure SQL-adatbázis egy feladatátvételi csoportba való hozzáadására szolgál. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Lekérdezi vagy felsorolja Azure SQL Database feladatátvételi csoportokat. |
| [Kapcsoló – AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database feladatátvételi csoport feladatátvételét hajtja végre. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Erőforráscsoport eltávolítása | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Egy előfizetést állít be az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Létrehoz egy adatbázist. | 
| [az SQL feladatátvétel-csoport létrehozása](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Létrehoz egy feladatátvételi csoportot. | 
| [az SQL feladatátvételi csoport listája](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Felsorolja a kiszolgálók feladatátvételi csoportjait. |
| [az SQL feladatátvétel-csoport beállítása – elsődleges](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Állítsa be a feladatátvételi csoport elsődlegesét úgy, hogy az a jelenlegi elsődleges kiszolgáló összes adatbázisát elvégzi. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Nincsenek elérhető parancsfájlok a Azure Portal számára. 
 
---

További Azure SQL Database szkriptek itt találhatók: [Azure PowerShell](sql-database-powershell-samples.md) és [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure SQL Database önálló adatbázist adott hozzá egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist. 
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egyetlen adatbázishoz két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

Folytassa a következő oktatóanyaggal, amely bemutatja, hogyan adhat hozzá rugalmas készletet egy feladatátvételi csoporthoz. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása egy feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
