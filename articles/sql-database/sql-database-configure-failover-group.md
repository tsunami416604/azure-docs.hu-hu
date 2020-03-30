---
title: Feladatátvevő csoport konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy automatikus feladatátvételi csoportot egy Azure SQL Database egyetlen adatbázishoz, rugalmas készlethez és felügyelt példányhoz az Azure Portalon, az Az CLI-n és a PowerShellen keresztül.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461806"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Feladatátvételi csoport konfigurálása az Azure SQL Database számára

Ez a témakör bemutatja, hogyan konfigurálhat egy [automatikus feladatátvételi csoportot](sql-database-auto-failover-group.md) egy Azure SQL Database egyetlen adatbázishoz, rugalmas készlethez és felügyelt példányhoz az Azure Portalon vagy a PowerShell használatával. 

## <a name="single-database"></a>Önálló adatbázis
Hozza létre a feladatátvételi csoportot, és adjon hozzá egyetlen adatbázist az Azure Portalon vagy a PowerShell használatával.

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos kiszolgáló kiszolgálóbejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

# <a name="portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist az Azure Portalhasználatával.


1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki azt az adatbázist, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. 
1. A **kiszolgáló beállításainak** megnyitásához a Kiszolgáló neve csoportban válassza ki a kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Feladatátvételi csoportok** lehetőséget a **Beállítások** ablaktáblán, majd a **Csoport hozzáadása lehetőséget** új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **Feladatátvételi csoport** lapon adja meg vagy jelölje ki a szükséges értékeket, majd válassza a **Létrehozás lehetőséget.**

   - **A csoporton belüli adatbázisok**: Válassza ki azt az adatbázist, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. Az adatbázis hozzáadása a feladatátvételi csoporthoz automatikusan elindítja a georeplikációs folyamatot. 
        
    ![SQL DB hozzáadása feladatátvételi csoporthoz](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot, és adja hozzá az egyetlen adatbázist a PowerShell használatával. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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

---

### <a name="test-failover"></a>Feladatátvétel tesztelése 

Az Azure Portal vagy a PowerShell használatával a feladatátvételi csoport feladatátvételi tesztje. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Tesztelje a feladatátvételi csoport feladatátvételi az Azure Portalon keresztül. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki azt az adatbázist, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. 

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Feladatátvételi csoportok lehetőséget** a **Beállítások** ablaktáblán, majd válassza ki az imént létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálról](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges és melyik másodlagos kiszolgáló. 
1. Válassza a **feladatátvétel** lehetőséget a munkaablakból, ha át szeretné adni az egyetlen adatbázist tartalmazó feladatátvételi csoportot. 
1. Válassza az **Igen** lehetőséget a figyelmeztetésen, amely értesíti, hogy a TDS-munkamenetek megszakadnak. 

   ![Feladatátvétel az SQL-adatbázist tartalmazó feladatátvételi csoport felett](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, és melyik másodlagos kiszolgáló. Ha a feladatátvétel sikeres volt, a két kiszolgálónak szerepköröket kellett cserélnie. 
1. Válassza **a Feladatátvétel** lehetőséget, ha a kiszolgálók visszaállnak az eredeti szerepkörükbe. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Tesztelje a feladatátvételi csoport feladatátvételi csoport powershell használatával.  

Ellenőrizze a másodlagos replika szerepét: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Ha törölnie kell a másodlagos adatbázist, törlés előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, az kiszámíthatatlan viselkedést okozhat. 

## <a name="elastic-pool"></a>Rugalmas készlet
Hozza létre a feladatátvételi csoportot, és adjon hozzá egy rugalmas készletet az Azure Portalon vagy a PowerShell használatával.  

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A másodlagos kiszolgáló kiszolgálóbejelentkezési és tűzfalbeállításainak meg kell egyezniük az elsődleges kiszolgáló beállításaival. 

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása 

Hozza létre a feladatátvételi csoportot a rugalmas készlethez az Azure Portalon vagy a PowerShell használatával. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá a rugalmas készletet az Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki a feladatátvételi csoporthoz hozzáadni kívánt rugalmas készletet. 
1. Az **Áttekintő** ablaktáblán válassza ki a kiszolgáló nevét a **Kiszolgáló neve** csoportban a kiszolgáló beállításainak megnyitásához.
  
    ![Kiszolgáló megnyitása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Válassza a **Feladatátvételi csoportok** lehetőséget a **Beállítások** ablaktáblán, majd a **Csoport hozzáadása lehetőséget** új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **Feladatátvételi csoport** lapon adja meg vagy jelölje ki a szükséges értékeket, majd válassza a **Létrehozás lehetőséget.** Hozzon létre egy új másodlagos kiszolgálót, vagy válasszon ki egy meglévő másodlagos kiszolgálót. 

1. Válassza **az adatbázisok a csoporton belül,** majd válassza ki a kívánt rugalmas készlet a feladatátvételi csoporthoz hozzáadni. Ha egy rugalmas készlet még nem létezik a másodlagos kiszolgálón, egy figyelmeztetés jelenik meg, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Jelölje ki a figyelmeztetést, majd az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 
        
    ![Rugalmas készlet hozzáadása feladatátvételi csoporthoz](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Válassza **a Kijelölés lehetőséget,** ha a rugalmas készlet beállításait a feladatátvételi csoportra szeretné alkalmazni, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet hozzáadása a feladatátvételi csoporthoz automatikusan elindítja a georeplikációs folyamatot. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá a rugalmas készletet a PowerShell használatával. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Feladatátvétel tesztelése

Tesztelje a rugalmas készlet feladatátvételt az Azure Portalon vagy a PowerShell használatával. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport átad a másodlagos kiszolgálóra, majd az Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki a feladatátvételi csoporthoz hozzáadni kívánt rugalmas készletet. 
1. Az **Áttekintő** ablaktáblán válassza ki a kiszolgáló nevét a **Kiszolgáló neve** csoportban a kiszolgáló beállításainak megnyitásához.
  
    ![Kiszolgáló megnyitása a rugalmas készlethez](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Válassza a **Feladatátvételi csoportok lehetőséget** a **Beállítások** ablaktáblán, majd válassza ki a 2. 
  
   ![Válassza ki a feladatátvételi csoportot a portálról](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, és melyik kiszolgáló másodlagos. 
1. Válassza **a feladatátvétel** lehetőséget a munkaablakból a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételi csoportát. 
1. Válassza az **Igen** lehetőséget a figyelmeztetésen, amely értesíti, hogy a TDS-munkamenetek megszakadnak. 

   ![Feladatátvétel az SQL-adatbázist tartalmazó feladatátvételi csoport felett](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló az elsődleges, melyik kiszolgáló másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak szerepköröket kellett cserélnie. 
1. Válassza **a Feladatátvétel** lehetőséget, ha a feladatátvételi csoportot vissza szeretné adni az eredeti beállításokhoz. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Tesztelje a feladatátvételi csoport feladatátvételi csoport powershell használatával.

Ellenőrizze a másodlagos replika szerepét: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> Ha törölnie kell a másodlagos adatbázist, törlés előtt távolítsa el a feladatátvételi csoportból. Ha egy másodlagos adatbázist töröl a feladatátvételi csoportból való eltávolítása előtt, az kiszámíthatatlan viselkedést okozhat. 

## <a name="managed-instance"></a>Felügyelt példány

Hozzon létre egy feladatátvételi csoportot két felügyelt példány között az Azure Portalon vagy a PowerShell használatával. 

Vagy konfigurálnia kell az [ExpressRoute-ot,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) vagy létre kell hoznia egy átjárót az egyes felügyelt példányok virtuális hálózatához, csatlakoztatnia kell a két átjárót, majd létre kell hoznia a feladatátvételi csoportot. 

### <a name="prerequisites"></a>Előfeltételek
Vegye figyelembe a következő előfeltételeket:

- A másodlagos felügyelt példánynak üresnek kell lennie.
- A másodlagos virtuális hálózat alhálózati tartománya nem fedheti át az elsődleges virtuális hálózat alhálózati tartományát. 
- A másodlagos példány rendezési és időzónájának meg kell egyeznie az elsődleges példány időzónájával. 
- A két átjáró csatlakoztatásakor a **megosztott kulcsnak** mindkét kapcsolatesetében azonosnak kell lennie. 

### <a name="create-primary-virtual-network-gateway"></a>Elsődleges virtuális hálózati átjáró létrehozása 

Ha még nem konfigurálta [az ExpressRoute-ot,](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)létrehozhatja az elsődleges virtuális hálózati átjárót az Azure Portalon vagy a PowerShellben. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre az elsődleges virtuális hálózati átjárót az Azure Portal használatával. 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki az elsődleges felügyelt példány **virtuális hálózati** erőforrását. 
1. Válassza **az Alhálózatok lehetőséget a** Beállítások **csoportban,** majd új **átjáró-alhálózat**hozzáadásához válassza az alhálózatok at. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózati átjáró létrehozása után válassza az **Erőforrás létrehozása** lehetőséget `Virtual network gateway` a bal oldali navigációs ablakban, majd írja be a keresőmezőbe. Válassza ki a **Microsoft**által közzétett **virtuális hálózati átjáró** erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Töltse ki a szükséges mezőket az elsődleges felügyelt példány átjárókonfigurálásához. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárójához szükséges értékeket mutatja be:
 
    | **Mező** | Érték |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, ahol az elsődleges felügyelt példány van. |
    | **Név** | A virtuális hálózati átjáró neve. | 
    | **Régió** | Az a régió, ahol a másodlagos felügyelt példány van. |
    | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
    | **VPN-típus** | **Útvonalalapú kiválasztása** |
    | **Sku**| Hagyja békén `VpnGw1`a. |
    | **Helyen**| Az a hely, ahol a másodlagos felügyelt példány és a másodlagos virtuális hálózat található.   |
    | **Virtuális hálózat**| Válassza ki a másodlagos felügyelt példány virtuális hálózatát. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím**| Adja meg az IP-cím nevét. |
    | &nbsp; | &nbsp; |

1. Hagyja a többi értéket alapértelmezettként, majd válassza a **Véleményezés + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Az új virtuális hálózati átjáró létrehozásához válassza a **Létrehozás** lehetőséget. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre az elsődleges virtuális hálózati átjárót a PowerShell használatával. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Másodlagos virtuális hálózati átjáró létrehozása

Hozza létre a másodlagos virtuális hálózati átjárót az Azure Portalon vagy a PowerShellen keresztül. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ismételje meg az előző szakaszban leírt lépéseket a virtuális hálózati alhálózat és átjáró létrehozásához a másodlagos felügyelt példányhoz. Töltse ki a másodlagos felügyelt példány átjárójának konfigurálásához szükséges mezőket. 

   Az alábbi táblázat a másodlagos felügyelt példány átjárójához szükséges értékeket mutatja be:

   | **Mező** | Érték |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, ahol a másodlagos felügyelt példány van. |
   | **Név** | A virtuális hálózati átjáró neve, `secondary-mi-gateway`például . | 
   | **Régió** | Az a régió, ahol a másodlagos felügyelt példány van. |
   | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
   | **VPN-típus** | **Útvonalalapú kiválasztása** |
   | **Sku**| Hagyja békén `VpnGw1`a. |
   | **Helyen**| Az a hely, ahol a másodlagos felügyelt példány és a másodlagos virtuális hálózat található.   |
   | **Virtuális hálózat**| Válassza ki a `vnet-sql-mi-secondary`2. |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím**| Adja meg az IP-cím `secondary-gateway-IP`nevét, például . |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáróbeállítások](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre a másodlagos virtuális hálózati átjárót a PowerShell használatával. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Az átjárók csatlakoztatása 
Az Azure Portal vagy a PowerShell használatával kapcsolatokat hozhat létre a két átjáró között. 

Két kapcsolatot kell létrehozni - a kapcsolatot az elsődleges átjáróból a másodlagos átjáróhoz, majd a kapcsolatot a másodlagos átjárótól az elsődleges átjáróhoz. 

A két kapcsolathoz használt megosztott kulcsnak minden kapcsolathoz azonosnak kell lennie. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Az Azure Portal használatával kapcsolatokat hozhat létre a két átjáró között. 

1. Válassza **az Erőforrás létrehozása** az Azure [Portalon](https://portal.azure.com)lehetőséget.
1. Írja `connection` be a keresőmezőbe, majd nyomja meg az Enter billentyűt a kereséshez, amely a Microsoft által közzétett **Connection** erőforráshoz vezet.
1. A kapcsolat létrehozásához válassza a **Létrehozás** gombot. 
1. Az **Alapok** lapon jelölje ki az alábbi értékeket, majd kattintson az **OK gombra.** 
    1. Válassza `VNet-to-VNet` ki a **Kapcsolat típushoz.** 
    1. Válassza ki előfizetését a legördülő listából. 
    1. Válassza ki a felügyelt példány erőforráscsoportját a legördülő menüben. 
    1. Válassza ki az elsődleges felügyelt példány helyét a legördülő menüből 
1. A **Beállítások** lapon jelölje ki vagy írja be a következő értékeket, majd kattintson az **OK gombra:**
    1. Válassza ki az **első virtuális hálózati átjáró** `Primary-Gateway`elsődleges hálózati átjáróját, például a.  
    1. Válassza ki a második virtuális hálózati átjáró `Secondary-Gateway`másodlagos hálózati **átjáróját,** például a. 
    1. Jelölje be a **Kétirányú kapcsolat létrehozása jelölőnégyzetet.** 
    1. Hagyja meg az alapértelmezett elsődleges kapcsolat nevet, vagy nevezze át az Ön által választott értékre. 
    1. Megosztott **kulcs (PSK)** biztosítása a kapcsolathoz, például `mi1m2psk`. 

   ![Átjárókapcsolat létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. Az **Összegzés** lapon tekintse át a kétirányú kapcsolat beállításait, majd a kapcsolat létrehozásához kattintson az **OK gombra.** 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozzon létre kapcsolatokat a két átjáró között a PowerShell használatával. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása 
Hozza létre a feladatátvételi csoportot a felügyelt példányok az Azure Portalon vagy a PowerShell használatával. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Hozza létre a feladatátvételi csoportot a felügyelt példányok az Azure Portalon. 

1. Válassza az **Azure SQL** lehetőséget az [Azure](https://portal.azure.com)Portal bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **Minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. (Nem kötelező) Válassza ki az **Azure SQL** melletti csillagot a kedvencként, és adja hozzá elemként a bal oldali navigációs sávban. 
1. Jelölje ki azt az elsődleges felügyelt példányt, amelyet hozzá szeretne adni a feladatátvételi csoporthoz.  
1. A **Beállítások csoportban**keresse meg **a Példány feladatátvételi csoportjait,** és válassza a **Csoport hozzáadása lehetőséget** a **Példány feladatátvételi csoport** lapjának megnyitásához. 

   ![Feladatátvevő csoport hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. A **Példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, majd válassza ki a másodlagos felügyelt példányt a legördülő menüből. A Feladatátvételi csoport létrehozásához válassza a **Létrehozás** lehetőséget. 

   ![Feladatátvételi csoport létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Miután a feladatátvételi csoport telepítése befejeződött, a **feladatátvételi csoport** lapra kerül vissza. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot a felügyelt példányok powershell használatával. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Feladatátvétel tesztelése

Az Azure Portal vagy a PowerShell használatával a feladatátvételi csoport feladatátvételi tesztje. 

# <a name="portal"></a>[Portál](#tab/azure-portal)

Tesztelje a feladatátvételi csoport feladatátvételi az Azure Portalon keresztül. 

1. Keresse meg a _másodlagos_ felügyelt példányaz [Azure Portalon,](https://portal.azure.com) és válassza **a példány feladatátvételi csoportok** a beállítások alatt. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza **a Feladatátvétel** lehetőséget, majd a leválasztott TDS-munkamenetekkel kapcsolatos figyelmeztetésen válassza az **Igen** lehetőséget. 

   ![Feladatátvétel a feladatátvételi csoport felett](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Tekintse át, hogy melyik manged példány az elsődleges, és melyik példány a másodlagos. Ha a feladatátvétel sikeres volt, a két példánynak szerepkört kellett váltania. 

   ![A felügyelt példányok szerepkört váltottak a feladatátvétel után](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Lépjen az új _másodlagos_ felügyelt példányra, és válassza a **Feladatátvétel** ismét az elsődleges példány elsődleges szerepkörbe való visszakerüléséhez. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Tesztelje a feladatátvételi csoport feladatátvételi csoport powershell használatával. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Figyelővégpont megkeresése

Miután a feladatátvételi csoport konfigurálva van, frissítse az alkalmazás kapcsolati karakterláncát a figyelő végpontjára. Ez az alkalmazás az elsődleges adatbázis, rugalmas készlet vagy felügyelt példány helyett a feladatátvételi csoport figyelőjéhez csatlakozik. Így nem kell manuálisan frissítenia a kapcsolati karakterláncot minden alkalommal, amikor az Azure SQL-adatbázis entitás átadja a feladatát, és a forgalom a ttól függően, hogy melyik entitás jelenleg elsődleges. 

A figyelő végpontja a , `fog-name.database.windows.net`és látható az Azure Portalon, a feladatátvételi csoport megtekintésekor:

![Feladatátvevő csoport kapcsolati karakterlánca](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Megjegyzések

- Az egy vagy készletbe adott adatbázis feladatátvételi csoportjának eltávolítása nem állítja le a replikációt, és nem törli a replikált adatbázist. Manuálisan le kell állítania a georeplikációt, és törölnie kell az adatbázist a másodlagos kiszolgálóról, ha egy vagy készletbe adott adatbázist szeretne hozzáadni egy feladatátvételi csoporthoz az eltávolítás után. Ha egyik dolgot sem teszi meg, `The operation cannot be performed due to multiple errors` az adatbázis feladatátvételi csoporthoz való hozzáadásakor hasonló hibát okozhat. 


## <a name="next-steps"></a>További lépések

A feladatátvételi csoport konfigurálásának részletes lépéseit az alábbi oktatóanyagokban tetszés szerint.
- [Egyetlen adatbázis hozzáadása feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md)
- [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
- [Felügyelt példányok hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md)
 
Az Azure SQL Database magas rendelkezésre állási lehetőségeinek áttekintése a [georeplikációs](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok ban](sql-database-auto-failover-group.md)található. 
