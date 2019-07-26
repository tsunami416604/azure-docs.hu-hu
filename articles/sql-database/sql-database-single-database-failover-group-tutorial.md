---
title: 'Oktatóanyag: Azure SQL Database önálló adatbázis hozzáadása egy feladatátvételi csoporthoz | Microsoft Docs'
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
manager: jroth
ms.date: 06/19/2019
ms.openlocfilehash: 6125fd087d40f9ac6ae24d9200fc0cd1e7843f82
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444980"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Oktatóanyag: Azure SQL Database önálló adatbázis hozzáadása egy feladatátvételi csoporthoz

A feladatátvételi csoportot konfigurálhatja egy Azure SQL Database önálló adatbázishoz, és feladatátvételi tesztet hajthat végre a Azure Portal, a PowerShell vagy az Azure CLI használatával.  Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist.
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egyetlen adatbázishoz két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

## <a name="prerequisites"></a>Előfeltételek

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel: 

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .
- Az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)legújabb verziója. 

---

## <a name="1---create-a-single-database"></a>1 – önálló adatbázis létrehozása 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – a feladatátvételi csoport létrehozása 
Ebben a lépésben létre fog hozni egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egy meglévő Azure SQL Server-kiszolgáló és egy másik régióban lévő új Azure SQL Server között. Ezután adja hozzá a mintaadatbázis a feladatátvételi csoporthoz. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá az önálló adatbázisát a Azure Portal használatával. 


1. Válassza ki az **összes szolgáltatást** a [Azure Portal](https://portal.azure.com)bal felső sarkában. 
1. Írja `sql servers` be a keresőmezőbe a kifejezést. 
1. Választható Válassza ki a csillag ikont az SQL-kiszolgálók mellett kedvenc **SQL-kiszolgálókhoz** , és adja hozzá a bal oldali navigációs ablaktáblához. 
    
    ![SQL-kiszolgálók keresése](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Válassza az **SQL-kiszolgálók** lehetőséget, és válassza ki az 1 `mysqlserver`. szakaszban létrehozott kiszolgálót, például:.
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a következő értékeket, majd válassza a **Létrehozás**lehetőséget:
    - **Feladatátvételi csoport neve**: Írjon be egy egyedi feladatátvételi csoport nevét, például `failovergrouptutorial`:. 
    - **Másodlagos kiszolgáló**: Válassza a *szükséges beállítások konfigurálását* , majd válassza az **új kiszolgáló létrehozása**lehetőséget. Másik lehetőségként már meglévő kiszolgálót is választhat másodlagos kiszolgálóként. A következő értékek beírása után válassza a **kiválasztás**lehetőséget. 
        - **Kiszolgáló neve**: Írjon be egy egyedi nevet a másodlagos kiszolgálónak, például `mysqlsecondary`:. 
        - **Kiszolgáló-rendszergazdai bejelentkezés**: Típusa`azureuser`
        - **Jelszó**: Írjon be egy olyan összetett jelszót, amely megfelel a jelszó követelményeinek.
        - **Hely**: Válasszon egy helyet a legördülő menüből, például az USA 2. keleti régiójában. Ez a hely nem lehet ugyanazon a helyen, mint az elsődleges kiszolgáló.

    > [!NOTE]
    > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 
    
      ![Másodlagos kiszolgáló létrehozása a feladatátvételi csoport számára](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **A csoporton belüli adatbázisok**: Ha a másodlagos kiszolgáló van kiválasztva, ez a beállítás lesz feloldva. Válassza ki a **hozzáadni** kívánt adatbázisokat, majd válassza ki az 1. szakaszban létrehozott adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a automatikusan elindítja a Geo-replikálási folyamatot. 
        
    ![SQL-adatbázis hozzáadása a feladatátvételi csoporthoz](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Hozza létre a feladatátvételi csoportot, és adja hozzá egyetlen adatbázisát a PowerShell használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

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

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
Hozza létre a feladatátvételi csoportot, és adja hozzá egyetlen adatbázisát az AZ CLI használatával. 

   > [!NOTE]
   > A kiszolgáló bejelentkezési és tűzfalbeállítások meg kell egyeznie az elsődleges kiszolgálóval. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3 – feladatátvételi teszt 
Ebben a lépésben a feladatátvételi csoportot a másodlagos kiszolgálóra fogja felvenni, majd a Azure Portal használatával hajtja végre a feladatokat. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Feladatátvételi teszt a Azure Portal használatával. 

1. Navigáljon az **SQL Server** -kiszolgálóhoz a [Azure Portalon](https://portal.azure.com)belül. 
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálon](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a minta önálló adatbázist tartalmazó feladatátvételi csoport feladatátvételéhez. 
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva. 

   ![Az SQL-adatbázist tartalmazó feladatátvételi csoport feladatátvétele](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló legyen az elsődleges, és melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie. 
1. Válassza  újra a feladatátvételt, hogy a kiszolgálókat visszaállítsa az eredeti szerepköreire. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
Feladatátvételi teszt az az CLI használatával. 


Ellenőrizze, hogy melyik kiszolgáló a másodlagos:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Feladatátvétel a másodlagos kiszolgálóra: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Törölje az erőforrásokat az erőforráscsoport törlésével. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Törölje az erőforráscsoportot a Azure Portal használatával. 


1. Navigáljon az erőforráscsoporthoz a [Azure Portal](https://portal.azure.com).
1. Válassza az **erőforráscsoport törlése** lehetőséget a csoport összes erőforrásának, valamint maga az erőforráscsoport törléséhez. 
1. Írja be az erőforráscsoport `myResourceGroup`nevét, a szövegmezőbe, majd kattintson a **Törlés** elemre az erőforráscsoport törléséhez.  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Törölje az erőforráscsoportot a PowerShell használatával. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)
Törölje az erőforráscsoportot az AZ CLI használatával. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>Teljes parancsfájlok

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Nincsenek elérhető parancsfájlok a Azure Portal számára.
 
---

További Azure SQL Database szkriptek itt találhatók: [Azure PowerShell](sql-database-powershell-samples.md) és az [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure SQL Database önálló adatbázist adott hozzá egy feladatátvételi csoporthoz, és tesztelte a feladatátvételt. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Hozzon létre egy Azure SQL Database önálló adatbázist. 
> - Hozzon létre egy [feladatátvételi csoportot](sql-database-auto-failover-group.md) egyetlen adatbázishoz két logikai SQL-kiszolgáló között.
> - Feladatátvételi teszt.

Folytassa a következő oktatóanyaggal, amely bemutatja, hogyan adhat hozzá rugalmas készletet egy feladatátvételi csoporthoz. 

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure SQL Database rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
