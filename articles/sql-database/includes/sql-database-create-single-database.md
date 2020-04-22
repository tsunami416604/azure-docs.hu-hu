---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: 563ac8748e9d4f8a254a151814491113b9c816e6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685135"
---
Ebben a lépésben hozzon létre egy Azure SQL Database-kiszolgálót és egyetlen adatbázist, amely az AdventureWorksLT mintaadatait használja. Az azure portalmenük és -képernyők használatával, vagy egy Azure CLI vagy PowerShell-parancsfájl használatával hozhat létre az Azure Cloud Shell használatával.

A módszerek közé tartozik egy kiszolgálószintű tűzfalszabály beállítása, amely lehetővé teszi a kiszolgáló eléréséhez használt számítógép nyilvános IP-címét. A kiszolgálótűzfal-szabályok létrehozásáról további információt a [Kiszolgálószintű tűzfal létrehozása](../sql-database-server-level-firewall-rule.md)című témakörben talál. Adatbázisszintű tűzfalszabályokat is beállíthat. Lásd: [Adatbázisszintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Erőforráscsoport, SQL-kiszolgáló és egyetlen adatbázis létrehozása az Azure Portalon:

1. Jelentkezzen be a [portálra.](https://portal.azure.com)
1. A keresősávon keresse meg és válassza az **Azure SQL**lehetőséget.
1. Az **Azure SQL-lapon** válassza a **Hozzáadás lehetőséget.** 
   
   ![Hozzáadás az Azure SQL-hez](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. Az **SQL-telepítés kiválasztása lapon** válassza ki az **SQL-adatbázisok csempét,** **amelynek egyetlen adatbázisa** az **Erőforrás típusa csoportban**található. A különböző adatbázisokról a Részletek megjelenítése lehetőséget választva tekintheti meg a **különböző adatbázisokról.**
1. Kattintson a **Létrehozás** gombra.
   
   ![Önálló adatbázis létrehozása](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Az **SQL-adatbázis létrehozása** képernyő **Alapjai** lapján a **Project részletei**csoportban válassza ki a megfelelő **Azure-előfizetést,** ha még nincs kiválasztva.
1. Az **Erőforráscsoport**csoportban válassza **az Új létrehozása**lehetőséget, írja be a *myResourceGroup*parancsot, és válassza **az OK**gombot.
1. Az **Adatbázis adatai csoportban**az **Adatbázis neve mezőbe** írja be a *mySampleDatabase értéket.*
1. **Kiszolgáló esetén**válassza az **Új létrehozása**lehetőséget, és töltse ki az Új **kiszolgáló** űrlapot az alábbiak szerint:
   - **Kiszolgáló neve**: Adja meg *a mysqlserver*, és néhány karaktert az egyediség.
   - **Kiszolgálófelügyeleti bejelentkezés**: Adja meg *az azureuser t.*
   - **Jelszó**: Adjon meg egy olyan jelszót, amely megfelel a követelményeknek, majd írja be újra a **Jelszó megerősítése** mezőbe.
   - **Hely**: Legördülő menü, és válasszon egy helyet, például **(USA) USA keleti része**.
   
   Válassza **az OK gombot.**
   
   ![Új kiszolgáló](../media/sql-database-single-database-get-started/new-server.png)
   
   Jegyezze fel a kiszolgáló rendszergazdai bejelentkezési adatait és jelszavát, hogy bejelentkezhessen a kiszolgálóra és az adatbázisokba. Ha elfelejti a bejelentkezési nevet vagy a jelszót, az adatbázis létrehozása után beszerezheti a bejelentkezési nevet, vagy alaphelyzetbe állíthatja a jelszót az **SQL szerver** oldalon. Az **SQL-kiszolgáló** lap megnyitásához jelölje ki a kiszolgáló nevét az adatbázis **áttekintése** lapon.
   
1. A **Számítás + tárolás**csoportban, ha újra szeretné konfigurálni az alapértelmezett beállításokat, válassza az Adatbázis **konfigurálása**lehetőséget.
   
   A **Konfigurálás** lapon tetszés szerint:
   - Módosítsa a **számítási szintet** **kiszolgáló** **nélküliről kiszolgáló nélkülire.**
   - Tekintse át és módosítsa a virtuális magok és az **adatok maximális méretének beállításait.** **vCores**
   - A hardverlétrehozás módosításához válassza a **Konfiguráció módosítása** lehetőséget.
   
   A módosítások elvégzése után válassza az **Alkalmaz**lehetőséget.
   
1. Válassza a **Tovább: Hálózatozás** lehetőséget a lap alján.
   
   ![Új SQL-adatbázis – Alaplap](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. A **Hálózat** lap **Kapcsolat metódus csoportjában**válassza a **Nyilvános végpont lehetőséget.** 
1. A **Tűzfalszabályok csoportban**állítsa **az Aktuális ügyfél IP-címének hozzáadása** értéket **Igen**beállításra.
1. Válassza a **Tovább lehetőséget: További beállítások** a lap alján.
   
   ![Hálózat lap](../media/sql-database-single-database-get-started/networking.png)
   
   A tűzfalbeállításokról további információt az [Azure-szolgáltatások és -erőforrások hozzáférése a kiszolgálóhoz](../sql-database-networkaccess-overview.md) és a Saját végpont hozzáadása című [témakörben](../../private-link/private-endpoint-overview.md)talál.
   
1. A **További beállítások** lap **Adatforrás** csoportjában a **Meglévő adatok használata**csoportban válassza a **Minta**lehetőséget.
1. Válassza a Lap alján válassza a **Véleményezés + létrehozás** lehetőséget.
   
   ![További beállítások lap](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. A beállítások áttekintése után válassza a **Létrehozás gombot.**

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Létrehozhat egy Azure-erőforráscsoportot, az SQL-kiszolgálót és az egyetlen adatbázist az Azure parancssori felületével (Azure CLI). Ha nem szeretné használni az Azure Cloud Shellt, telepítse az [Azure CLI-t](/cli/azure/install-azure-cli) a számítógépre.

A következő kódminta futtatásához az Azure Cloud Shellben válassza a **Próbálja ki** a kódminta címsorában lehetőséget. Amikor a Cloud Shell megnyílik, válassza a **Másolás** lehetőséget a kódminta címsorában, és illessze be a kódmintát a Cloud Shell ablakba. A kódban `<Subscription ID>` cserélje le az Azure-előfizetés-azonosítóját, valamint a és `$startip` `$endip`a helyére `0.0.0.0` a használt számítógép nyilvános IP-címét.

Kövesse a képernyőn megjelenő utasításokat az Azure-ba való bejelentkezéshez és a kód futtatásához. 

Az Azure Cloud Shell az Azure Portalon is használhatja, ha a felső sávon kiválasztja a Cloud Shell ikont. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Amikor először használja a Cloud Shellt a portálon, válassza a **Bash** lehetőséget az **Üdvözlő** párbeszédpanelen. A következő munkamenetek az Azure CLI-t bash környezetben fogják használni, vagy kiválaszthatja a **Bash-t** a Cloud Shell vezérlősávjáról. 

A következő Azure CLI-kód létrehoz egy Azure-erőforráscsoportot, az SQL-kiszolgálót, az egyetlen adatbázist és a tűzfalszabályt a kiszolgáló eléréséhez. Győződjön meg arról, hogy rögzíti a létrehozott erőforráscsoport és a kiszolgáló nevét, így ezeket az erőforrásokat később kezelheti.

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

# Set a logical server name that is unique to Azure DNS (<server_name>.database.windows.net)
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

Az előző kód a következő Azure CLI-parancsokat használja:

| Parancs | Leírás |
|---|---|
| [az számlakészlet](/cli/azure/account?view=azure-cli-latest#az-account-set) | Beállítja az előfizetést az aktuális aktív előfizetésre. | 
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [az sql server tűzfal-szabály létrehozása](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Létrehozza a kiszolgáló tűzfalszabályait. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Létrehoz egy adatbázist. | 

További Azure Database Azure CLI-mintákért tekintse meg az [Azure CLI-mintákat.](../sql-database-cli-samples.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Létrehozhat egy Azure-erőforráscsoportot, SQL-kiszolgálót és egyetlen adatbázist a Windows PowerShell használatával. Ha nem szeretné használni az Azure Cloud Shellt, [telepítse az Azure PowerShell-modult.](/powershell/azure/install-az-ps)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő kódminta futtatásához az Azure Cloud Shell, válassza **próbálja ki** a kód címsorában. Amikor a Cloud Shell megnyílik, válassza a **Másolás** lehetőséget a kódminta címsorában, és illessze be a kódmintát a Cloud Shell ablakba. A kódban `<Subscription ID>` cserélje le az Azure-előfizetés-azonosítóját, valamint a és `$startIp` `$endIp`a helyére `0.0.0.0` a használt számítógép nyilvános IP-címét. 

Kövesse a képernyőn megjelenő utasításokat az Azure-ba való bejelentkezéshez és a kód futtatásához. 

Az Azure Cloud Shell az Azure Portalon is használható, ha a felső sávon a Cloud Shell ikonra kattint. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Amikor először használja a Cloud Shellt a portálról, válassza a **PowerShell** lehetőséget az **üdvözlő** párbeszédpanelen. A következő munkamenetek a PowerShellt fogják használni, vagy kiválaszthatja a Cloud Shell vezérlősávjáról. 

A következő PowerShell-kód létrehoz egy Azure-erőforráscsoportot, az SQL-kiszolgálót, az egyetlen adatbázist és a tűzfalszabályt a kiszolgáló eléréséhez. Győződjön meg arról, hogy rögzíti a létrehozott erőforráscsoport és a kiszolgáló nevét, így ezeket az erőforrásokat később kezelheti.

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

Az előző kód a következő PowerShell-parancsmagokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Új-AzsqlServer](/powershell/module/az.sql/new-azsqlserver) | Létrehoz egy SQL Database-kiszolgálót, amely egyetlen adatbázisokat és rugalmas készleteket üzemeltet. |
| [Új-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Tűzfalszabályt hoz létre egy logikai kiszolgálóhoz. | 
| [Új-AzsqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Létrehoz egy Azure SQL Database egyetlen adatbázist. | 

További Azure SQL Database PowerShell-mintákért tekintse meg az [Azure PowerShell-mintákat.](../sql-database-powershell-samples.md)

---
