---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 07/31/2019
ms.author: mathoma
ms.openlocfilehash: d19e21edd31be461ba30e8985e80c4651bbbb02d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894127"
---
Ebben a lépésben egy Azure SQL Database önálló adatbázist fog létrehozni. 

> [!IMPORTANT]
> Ügyeljen arra, hogy a jelen cikk elvégzéséhez a használni kívánt számítógép nyilvános IP-címét használja a tűzfalszabályok beállításához.
>
> További információért lásd: [adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) vagy a számítógép kiszolgáló szintű tűzfalszabály által használt IP-cím meghatározása: [kiszolgálói szintű tűzfal létrehozása](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Hozza létre az erőforráscsoportot és az önálló adatbázist a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a Azure Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az *Azure SQL* kifejezést a keresőmezőbe.
2. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A különböző adatbázisokkal kapcsolatos további információkat az **adatbázisok** csempén a **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
3. Válassza a **Létrehozás**lehetőséget:

   ![Önálló adatbázis létrehozása](../media/sql-database-get-started-portal/create-single-database.png)

3. Az **alapvető beállítások** lap **projekt részletei** területén írja be vagy válassza ki a következő értékeket:

   - **Előfizetés**: Legördülő menüből válassza ki a megfelelő előfizetést, ha az nem jelenik meg.
   - **Erőforráscsoport**: Válassza az **új létrehozása**, `myResourceGroup`majd a típus lehetőséget, majd kattintson **az OK gombra**.

     ![Új SQL Database-adatbázis – alapszintű lap](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Az **adatbázis részletei** szakaszban írja be vagy válassza ki a következő értékeket:

   - **Adatbázis neve**: Írja be a `mySampleDatabase` (igen) kifejezést.
   - **Kiszolgáló**: Válassza az **új létrehozása**elemet, adja meg a következő értékeket, majd válassza a **kiválasztás**lehetőséget.
       - **Kiszolgáló neve**: Írja `mysqlserver`be a típust, és adjon meg néhány számot az egyediséghez.
       - **Kiszolgáló-rendszergazdai bejelentkezés**: Gépelje be: `azureuser`.
       - **Jelszó**: Írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
       - **Hely**: Válasszon egy helyet a legördülő menüből, például `West US 2`:.

         ![Új kiszolgáló](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Ne felejtse el rögzíteni a kiszolgáló-rendszergazdai bejelentkezési azonosítót és a jelszót, hogy bejelentkezzen a kiszolgálóra és az adatbázisokra ehhez és más gyors útmutatóhoz. Ha elfelejti a felhasználónevét vagy jelszavát, beolvashatja a bejelentkezési nevet, vagy visszaállíthatja a jelszót az **SQL Server** -lapon. Az **SQL Server** oldal megnyitásához válassza ki a kiszolgáló nevét az adatbázis- **Áttekintés** oldalon az adatbázis létrehozása után.

   - **Rugalmas SQL-készletet szeretne használni**: Válassza a **nincs** lehetőséget.
   - **Számítás + tárolás**: Válassza az **adatbázis konfigurálása**lehetőséget. 

     ![SQL Database részletei](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Válassza a kiépített és a **Gen5**lehetőséget.

     ![Kiépített Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Tekintse át a **maximális virtuális mag**, a **min virtuális mag**, az automatikus **szüneteltetési késleltetés**és az **adatmaximális méret**beállításait. Módosítsa ezeket a kívánt módon.
   - Fogadja el az előnézet feltételeit, majd kattintson **az OK**gombra.
   - Kattintson az **Alkalmaz** gombra.

5. Válassza a **További beállítások** lapot. 
6. Az **adatforrás** szakasz **meglévő adat használata**területén válassza a elemet `Sample`.

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
   $location = "West US 2"
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

---
