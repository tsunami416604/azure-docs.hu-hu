---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: ff5505c2cb35d088565773e8d0ba01e8abb4b8c3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933226"
---
Ebben a lépésben egy Azure SQL Database önálló adatbázist fog létrehozni. 

> [!IMPORTANT]
> Ügyeljen arra, hogy a jelen cikk elvégzéséhez a használni kívánt számítógép nyilvános IP-címét használja a tűzfalszabályok beállításához.
>
> További információért lásd: [adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) vagy a számítógép kiszolgáló szintű tűzfalszabály által használt IP-cím meghatározása: [kiszolgálói szintű tűzfal létrehozása](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Hozza létre az erőforráscsoportot és az önálló adatbázist a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
2. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az **adatbázisok** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
3. Válassza a **Létrehozás**lehetőséget:

   ![Önálló adatbázis létrehozása](../media/sql-database-get-started-portal/create-single-database.png)

3. Az **alapvető beállítások** lap **projekt részletei** területén írja be vagy válassza ki a következő értékeket:

   - **Előfizetés**: legördülő menüből válassza ki a megfelelő előfizetést, ha az nem jelenik meg.
   - **Erőforráscsoport**: válassza az **új létrehozása**elemet, írja be a `myResourceGroup`lehetőséget, majd kattintson **az OK gombra**.

     ![Új SQL Database-adatbázis – alapszintű lap](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Az **adatbázis részletei** szakaszban írja be vagy válassza ki a következő értékeket:

   - **Adatbázis neve**: írja be a `mySampleDatabase`.
   - **Kiszolgáló**: válassza az **új létrehozása**elemet, adja meg a következő értékeket, majd válassza a **kiválasztás**lehetőséget.
       - **Kiszolgáló neve**: Type `mysqlserver`; az egyediséggel együtt néhány számot.
       - **Kiszolgáló-rendszergazdai bejelentkezés**: írja be a `azureuser`.
       - **Password (jelszó**): írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
       - **Hely**: válasszon ki egy helyet a legördülő menüből, például `West US`.

         ![Új kiszolgáló](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Ne felejtse el rögzíteni a kiszolgáló-rendszergazdai bejelentkezési azonosítót és a jelszót, hogy bejelentkezzen a kiszolgálóra és az adatbázisokra ehhez és más gyors útmutatóhoz. Ha elfelejti a felhasználónevét vagy jelszavát, beolvashatja a bejelentkezési nevet, vagy visszaállíthatja a jelszót az **SQL Server** -lapon. Az **SQL Server** oldal megnyitásához válassza ki a kiszolgáló nevét az adatbázis- **Áttekintés** oldalon az adatbázis létrehozása után.

   - **Rugalmas SQL-készletet szeretne használni**: válassza a **nincs** lehetőséget.
   - **Számítás + tárolás**: válassza az **adatbázis konfigurálása**lehetőséget. 

     ![SQL Database részletei](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Válassza a **kiépített** és a **Gen5**lehetőséget.

     ![Kiépített Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Tekintse át a **maximális virtuális mag**, a **min virtuális mag**, az automatikus **szüneteltetési késleltetés**és az **adatmaximális méret**beállításait. Módosítsa ezeket a kívánt módon.
   - Fogadja el az előnézet feltételeit, majd kattintson **az OK**gombra.
   - Kattintson az **Alkalmaz** gombra.

5. Válassza a **További beállítások** lapot. 
6. Az **adatforrás** szakasz **meglévő adat használata**területén válassza a `Sample`lehetőséget.

   ![További SQL-adatbázis-beállítások](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a **minta-(AdventureWorksLT-)** adatértékek könnyen követhető legyenek, és más, az ezeket az adatAzure SQL Databaset használó rövid útmutatók.

7. Hagyja meg a többi értéket alapértelmezettként, és válassza a **felülvizsgálat + létrehozás** elemet az űrlap alján.
8. Tekintse át a végső beállításokat, és válassza a **Létrehozás**lehetőséget.

9. A **SQL Database** űrlapon válassza a **Létrehozás** lehetőséget az erőforráscsoport, a kiszolgáló és az adatbázis üzembe helyezéséhez és kiépítéséhez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Hozzon létre egy erőforráscsoportot és egy önálló adatbázist a PowerShell használatával.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

A cikk ezen része a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása logikai kiszolgálóhoz. | 
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy új Azure SQL Database önálló adatbázist. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Hozza létre az erőforráscsoportot és az önálló adatbázist AZ AZ parancssori felület használatával.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Egy előfizetést állít be az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Létrehoz egy adatbázist. | 


---
