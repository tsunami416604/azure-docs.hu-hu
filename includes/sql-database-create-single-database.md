---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444681"
---
Ebben a lépésben az erőforráscsoportot és egy Azure SQL Database önálló adatbázist fog létrehozni. 

> [!IMPORTANT]
> Ügyeljen arra, hogy a tűzfalszabályok azon számítógép nyilvános IP-címének használatára legyenek beállítva, amelyen a jelen cikkben szereplő lépéseket végrehajtja. Az adatbázis-szintű tűzfalszabályok automatikusan replikálva lesznek a másodlagos kiszolgálóra.
>
> További információért lásd: [adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) vagy a kiszolgáló szintű tűzfalszabály által használt IP-cím meghatározása a számítógépen: [kiszolgáló szintű tűzfal létrehozása](../articles/sql-database/sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Hozza létre az erőforráscsoportot és az önálló adatbázist a Azure Portal használatával. 

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza az **adatbázisok** lehetőséget, majd válassza a **SQL Database** lehetőséget a **SQL Database létrehozása** lap megnyitásához.

   ![Önálló adatbázis létrehozása](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. Az **alapvető beállítások** lap **projekt részletei** területén írja be vagy válassza ki a következő értékeket:

   - **Előfizetés**: Legördülő menüből válassza ki a megfelelő előfizetést, ha az nem jelenik meg.
   - **Erőforráscsoport**: Válassza az **új létrehozása**, `myResourceGroup`majd a típus lehetőséget, majd kattintson **az OK gombra**.

     ![Új SQL Database-adatbázis – alapszintű lap](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. Az **adatbázis részletei** szakaszban írja be vagy válassza ki a következő értékeket:

   - **Adatbázis neve**: Írja be a `mySampleDatabase` (igen) kifejezést.
   - **Kiszolgáló**: Válassza az **új létrehozása** elemet, és adja meg a következő értékeket, majd válassza a **kiválasztás**lehetőséget.
       - **Kiszolgáló neve**: Írja `mysqlserver`be a típust, és adjon meg néhány számot az egyediséghez.
       - **Kiszolgáló-rendszergazdai bejelentkezés**: Gépelje be: `azureuser`.
       - **Jelszó**: Írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
       - **Hely**: Válasszon egy helyet a legördülő menüből, például `West US 2`:.

         ![Új kiszolgáló](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Ne felejtse el rögzíteni a kiszolgáló-rendszergazdai bejelentkezési azonosítót és a jelszót, hogy bejelentkezzen a kiszolgálóra és az adatbázisokra ehhez és más gyors útmutatóhoz. Ha elfelejti a felhasználónevét vagy jelszavát, beolvashatja a bejelentkezési nevet, vagy visszaállíthatja a jelszót az **SQL Server** -lapon. Az **SQL Server** oldal megnyitásához válassza ki a kiszolgáló nevét az adatbázis- **Áttekintés** oldalon az adatbázis létrehozása után.

        ![SQL Database részletei](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Rugalmas SQL-készletet szeretne használni**: Válassza a **nincs** lehetőséget.
   - **Számítás + tárolás**: Válassza az **adatbázis konfigurálása** és ehhez a rövid útmutatóhoz lehetőséget, majd válassza a **virtuális mag-alapú vásárlási lehetőségek lehetőséget** .

     ![Virtuálismag-alapú vásárlási beállítások](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - Válassza  a kiépített és a **Gen4**lehetőséget.

     ![kiszolgáló nélküli számítási szintek](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - Tekintse át a **maximális virtuális mag**, a **minimális virtuális mag**, az **automatikus szüneteltetés késleltetését**és az adatmaximális **méretet**. Módosítsa ezeket a kívánt módon.
   - Fogadja el az előnézet feltételeit, majd kattintson **az OK**gombra.
   - Kattintson az **Alkalmaz** gombra.

5. Válassza a **További beállítások** lapot. 
6. Az **adatforrás** szakasz **meglévő adat használata**területén válassza a elemet `Sample`. 

   ![További SQL-adatbázis-beállítások](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a **minta-(AdventureWorksLT-)** adatértékek könnyen követhető legyenek, és más, az ezeket az adatAzure SQL Databaset használó rövid útmutatók.

7. Hagyja meg a többi értéket alapértelmezettként, és válassza a **felülvizsgálat + létrehozás** elemet az űrlap alján.
8. Tekintse át a végső beállításokat, és válassza a **Létrehozás**lehetőséget.

9. A **SQL Database** űrlapon válassza a **Létrehozás** lehetőséget az erőforráscsoport, a kiszolgáló és az adatbázis üzembe helyezéséhez és kiépítéséhez.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

Hozzon létre egy erőforráscsoportot és egy önálló adatbázist a PowerShell használatával. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
Hozza létre az erőforráscsoportot és az önálló adatbázist AZ AZ parancssori felület használatával. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---