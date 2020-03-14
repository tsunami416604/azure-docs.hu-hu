---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: c1ca87b6e7b8afb50522e73107707e15782a0a91
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79199931"
---
Ebben a lépésben egy Azure SQL Database-kiszolgálót és egy AdventureWorksLT-mintát használó önálló adatbázist hoz létre. Az adatbázist Azure Portal menük és képernyők használatával, vagy az Azure Cloud Shell Azure CLI-vagy PowerShell-parancsfájllal is létrehozhatja.

Az összes módszer magában foglalja a kiszolgálói szintű tűzfalszabály beállítását, hogy engedélyezze a kiszolgálóhoz való hozzáféréshez használt számítógép nyilvános IP-címét. A kiszolgálói Tűzfalszabályok létrehozásával kapcsolatos további információkért tekintse meg [a kiszolgálói szintű tűzfal létrehozása](../sql-database-server-level-firewall-rule.md)című témakört. Az adatbázis szintű tűzfalszabályok is megadhatók. Lásd: [adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Erőforráscsoport, SQL Server és önálló adatbázis létrehozása a Azure Portalban:

1. Jelentkezzen be a [portálra](https://portal.azure.com).
1. A keresősáv alatt keresse meg és válassza ki az **Azure SQL**elemet.
1. Az **Azure SQL** lapon válassza a **Hozzáadás**lehetőséget. 
   
   ![Hozzáadás az Azure SQL-hez](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Az **SQL központi telepítési lehetőség kiválasztása** lapon válassza az **SQL-adatbázisok** csempét az **Erőforrás típusa**területen található **önálló adatbázis** elemre. A különböző adatbázisokkal kapcsolatos további információkat a **Részletek megjelenítése**lehetőség kiválasztásával tekintheti meg.
1. Kattintson a **Létrehozás** gombra.
   
   ![Önálló adatbázis létrehozása](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Az **SQL-adatbázis létrehozása** űrlap **alapismeretek** lapján a **projekt részletei**területen válassza ki a megfelelő Azure- **előfizetést** , ha még nincs kiválasztva.
1. Az **erőforráscsoport**területen válassza az **új létrehozása**elemet, írja be a *MyResourceGroup*, majd kattintson **az OK gombra**.
1. Az **adatbázis adatai**alatt az **adatbázis neve** mezőbe írja be a következőt: *mySampleDatabase*.
1. A **kiszolgáló**területen válassza az **új létrehozása**lehetőséget, és töltse ki az **új kiszolgáló** űrlapot a következőképpen:
   - **Kiszolgáló neve**: adja meg a *portra beállított mysqlserver*, és néhány karaktert az egyediség érdekében.
   - **Kiszolgáló-rendszergazdai bejelentkezés**: írja be az *Azureus*nevet.
   - **Password (jelszó**): adjon meg egy jelszót, amely megfelel a követelményeknek, majd írja be újra a **Jelszó megerősítése** mezőbe.
   - **Hely**: legördülő lista, válasszon egy helyet, például **(USA) az USA keleti**régióját.
   
   Kattintson az **OK** gombra.
   
   ![Új kiszolgáló](../media/sql-database-single-database-get-started/new-server.png)
   
   Jegyezze fel a kiszolgáló rendszergazdai felhasználónevét és jelszavát, hogy bejelentkezzen a kiszolgálóra és az adatbázisokra. Ha elfelejti a felhasználónevét vagy jelszavát, beolvashatja a bejelentkezési nevet, vagy visszaállíthatja a jelszót az **SQL Server** -lapon az adatbázis létrehozása után. Az **SQL Server** oldal megnyitásához válassza ki a kiszolgáló nevét az adatbázis **Áttekintés** lapján.
   
1. Ha újra szeretné konfigurálni az alapértelmezett értékeket a **számítás + tárolás**területen, válassza az **adatbázis konfigurálása**lehetőséget.
   
   A **configure (Konfigurálás** ) lapon igény szerint:
   - Módosítsa a **számítási szintet** a **kiépített** **kiszolgáló**nélküli értékre.
   - Tekintse át és módosítsa a **virtuális mag** és az **adatmaximális méret**beállításait.
   - A hardver generálásának módosításához válassza a **konfiguráció módosítása** lehetőséget.
   
   A módosítások végrehajtása után válassza az **alkalmaz**lehetőséget.
   
1. Válassza a **Tovább: hálózatkezelés** elemet az oldal alján.
   
   ![Új SQL Database-adatbázis – alapszintű lap](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. A **hálózatkezelés** lap **kapcsolati módszer**területén válassza a **nyilvános végpont**elemet. 
1. A **Tűzfalszabályok**területen állítsa az **aktuális ügyfél IP-címének hozzáadása** **Igen értéket**.
1. Válassza a **Tovább: további beállítások** elemet az oldal alján.
   
   ![Hálózatkezelés lap](../media/sql-database-single-database-get-started/networking.png)
   
   További információ a tűzfal beállításairól: az [Azure-szolgáltatások és-erőforrások engedélyezése a kiszolgálóhoz való hozzáféréshez](../sql-database-networkaccess-overview.md) és [privát végpont hozzáadása](../../private-link/private-endpoint-overview.md).
   
1. A **További beállítások** lap **adatforrás** szakaszában, a meglévő adatforrások **használatához**válassza a **minta**lehetőséget.
1. Kattintson a lap alján található **felülvizsgálat + létrehozás** lehetőségre.
   
   ![További beállítások lap](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. A beállítások áttekintése után válassza a **Létrehozás**lehetőséget.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure parancssori felületének (Azure CLI) használatával létrehozhat egy Azure-erőforráscsoportot, egy SQL Servert és egy önálló adatbázist. Ha nem szeretné használni a Azure Cloud Shell, telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t a számítógépre.

Ha a következő kódot szeretné futtatni a Azure Cloud Shellban, válassza a **kipróbálás** elemet a kód minta címsorában. Amikor megnyílik a Cloud Shell, válassza a **Másolás** elemet a kód minta címsorában, és illessze be a kód mintát a Cloud Shell ablakába. A kódban cserélje le a `<Subscription ID>`t az Azure-előfizetési azonosítójával, és a `$startip` és a `$endip`esetében cserélje le a `0.0.0.0`t a használt számítógép nyilvános IP-címére.

Kövesse a képernyőn megjelenő utasításokat az Azure-ba való bejelentkezéshez és a kód futtatásához. 

A Azure Portal Azure Cloud Shell is használhatja, ha a felső sávon a Cloud Shell ikonra kattint. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Amikor első alkalommal használja Cloud Shell a portálon, válassza a **bash** lehetőséget az **üdvözlő** párbeszédpanelen. A következő munkamenetek az Azure CLI-t egy bash-környezetben fogják használni, vagy kiválaszthatja a **bash** elemet a Cloud Shell vezérlőelem sávjában. 

A következő Azure CLI-kód egy Azure-erőforráscsoportot, egy SQL Servert, egy önálló adatbázist és egy tűzfalszabály-szabályt hoz létre a kiszolgálóhoz való hozzáféréshez. Ügyeljen arra, hogy rögzítse a létrehozott erőforráscsoportot és a kiszolgáló nevét, így később is kezelheti ezeket az erőforrásokat.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a logical server name that is unique in the system
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Az előző kód az alábbi Azure CLI-parancsokat használja:

| Parancs | Leírás |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Egy előfizetést állít be az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Létrehoz egy kiszolgáló tűzfalszabály-szabályait. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Létrehoz egy adatbázist. | 

További Azure SQL Database Azure CLI-mintákhoz lásd: [Azure CLI-minták](../sql-database-cli-samples.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Létrehozhat egy Azure-erőforráscsoportot, az SQL Servert és az önálló adatbázist a Windows PowerShell használatával. Ha nem szeretné használni a Azure Cloud Shell, [telepítse a Azure PowerShell modult](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha az alábbi kódrészletet szeretné futtatni a Azure Cloud Shellban, válassza a **kipróbálás** lehetőséget a kód címsorában. Amikor megnyílik a Cloud Shell, válassza a **Másolás** elemet a kód minta címsorában, és illessze be a kód mintát a Cloud Shell ablakába. A kódban cserélje le a `<Subscription ID>`t az Azure-előfizetési azonosítójával, és a `$startIp` és a `$endIp`esetében cserélje le a `0.0.0.0`t a használt számítógép nyilvános IP-címére. 

Kövesse a képernyőn megjelenő utasításokat az Azure-ba való bejelentkezéshez és a kód futtatásához. 

A Azure Portal Azure Cloud Shell is használhatja, ha a felső sávon a Cloud Shell ikonra kattint. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Amikor első alkalommal használja Cloud Shell a portálon, válassza a **PowerShell** lehetőséget az **üdvözlő** párbeszédpanelen. A következő munkamenetek a PowerShellt fogják használni, vagy kiválaszthatják a Cloud Shell vezérlőelem sávján. 

A következő PowerShell-kód egy Azure-erőforráscsoportot, egy SQL Servert, egy önálló adatbázist és egy tűzfalszabály-szabályt hoz létre a kiszolgálóhoz való hozzáféréshez. Ügyeljen arra, hogy rögzítse a létrehozott erőforráscsoportot és a kiszolgáló nevét, így később is kezelheti ezeket az erőforrásokat.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
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

Az előző kód ezeket a PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új – AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database kiszolgálót, amely önálló adatbázist és rugalmas készleteket üzemeltet. |
| [Új – AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabály létrehozása logikai kiszolgálóhoz. | 
| [Új – AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy Azure SQL Database önálló adatbázist. | 

További Azure SQL Database PowerShell-példákat a következő témakörben talál: [Azure PowerShell minták](../sql-database-powershell-samples.md).

---
