---
title: Feladatátvételi csoport konfigurálása Azure SQL Databasehoz
description: Megtudhatja, hogyan konfigurálhat automatikus feladatátvételi csoportot egy Azure SQL Database önálló adatbázishoz, rugalmas készlethez és felügyelt példányhoz a Azure Portal, az az CLI és a PowerShell használatával.
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
ms.openlocfilehash: 9206fd264854cd9e5d8e46473dd60b05a3362fdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329356"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Feladatátvételi csoport konfigurálása Azure SQL Databasehoz

Ez a témakör bemutatja, hogyan konfigurálhat egy [automatikus feladatátvételi csoportot](sql-database-auto-failover-group.md) egy Azure SQL Database önálló adatbázishoz, rugalmas készlethez és felügyelt példányhoz a Azure Portal vagy a PowerShell használatával. 

## <a name="single-database"></a>Önálló adatbázis
Hozza létre a feladatátvételi csoportot, és vegyen fel egyetlen adatbázist a Azure Portal vagy a PowerShell használatával.

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A kiszolgáló bejelentkezési és tűzfalbeállítások a másodlagos kiszolgálónak meg kell egyezniük az elsődleges kiszolgálóval. 

### <a name="create-failover-group"></a>Feladatátvételi csoport létrehozása

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá az önálló adatbázisát a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki azt az önálló adatbázist, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. 
1. A kiszolgáló beállításainak megnyitásához válassza a **kiszolgáló neve alatt lévő** kiszolgáló nevét.

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás**lehetőséget.

   - **A csoporton belüli adatbázisok**: Válassza ki a feladatátvételi csoportba felvenni kívánt adatbázist. Ha hozzáadja az adatbázist a feladatátvételi csoporthoz, a automatikusan elindítja a Geo-replikálási folyamatot. 
        
    ![SQL-adatbázis hozzáadása a feladatátvételi csoporthoz](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Hozza létre a feladatátvételi csoportot, és adja hozzá egyetlen adatbázisát a PowerShell használatával. 

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

### <a name="test-failover"></a>Feladatátvételi teszt 

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki azt az önálló adatbázist, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. 

   ![Kiszolgáló megnyitása egyetlen adatbázishoz](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki az imént létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálon](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos. 
1. Válassza ki a **feladatátvétel** elemet a feladat ablaktáblán az önálló adatbázist tartalmazó feladatátvételi csoport feladatátvételéhez. 
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva. 

   ![Az SQL-adatbázist tartalmazó feladatátvételi csoport feladatátvétele](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló legyen az elsődleges, és melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie. 
1. Válassza újra a **feladatátvételt** , hogy a kiszolgálókat visszaállítsa az eredeti szerepköreire. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.  

Vizsgálja meg a másodlagos replika szerepkörét: 

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
Feladatátvétel a másodlagos kiszolgálóra: 

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

A feladatátvételi csoport visszaállítása az elsődleges kiszolgálóra:

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

## <a name="elastic-pool"></a>Rugalmas készlet
Hozza létre a feladatátvételi csoportot, és adjon hozzá egy rugalmas készletet a Azure Portal vagy a PowerShell használatával.  

### <a name="prerequisites"></a>Előfeltételek

Vegye figyelembe a következő előfeltételeket:

- A kiszolgáló bejelentkezési és tűzfalbeállítások a másodlagos kiszolgálónak meg kell egyezniük az elsődleges kiszolgálóval. 

### <a name="create-the-failover-group"></a>A feladatátvételi csoport létrehozása 

Hozza létre a rugalmas készlet feladatátvételi csoportját a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Hozza létre a feladatátvételi csoportot, és adja hozzá rugalmas készletét a Azure Portal használatával.

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki a feladatátvevő csoportba felvenni kívánt rugalmas készletet. 
1. Az **Áttekintés** ablaktáblán válassza ki a kiszolgáló nevét a kiszolgáló **neve** alatt a kiszolgáló beállításainak megnyitásához.
  
    ![Rugalmas készlet kiszolgálójának megnyitása](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza a **Csoport hozzáadása** lehetőséget egy új feladatátvételi csoport létrehozásához. 

    ![Új feladatátvételi csoport hozzáadása](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. A **feladatátvételi csoport** lapon adja meg vagy válassza ki a szükséges értékeket, majd válassza a **Létrehozás**lehetőséget. Hozzon létre egy új másodlagos kiszolgálót, vagy válasszon ki egy meglévő másodlagos kiszolgálót. 

1. Válassza ki az **adatbázisokat a csoporton belül** , majd válassza ki azt a rugalmas készletet, amelyet hozzá szeretne adni a feladatátvételi csoporthoz. Ha egy rugalmas készlet még nem létezik a másodlagos kiszolgálón, egy figyelmeztetés jelenik meg, amely arra kéri, hogy hozzon létre egy rugalmas készletet a másodlagos kiszolgálón. Válassza ki a figyelmeztetést, majd kattintson az **OK** gombra a rugalmas készlet létrehozásához a másodlagos kiszolgálón. 
        
    ![Rugalmas készlet hozzáadása a feladatátvételi csoporthoz](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Válassza a **kiválasztás** elemet a rugalmas készlet beállításainak a feladatátvételi csoportra való alkalmazásához, majd válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. A rugalmas készlet a feladatátvételi csoportba való felvétele automatikusan elindítja a Geo-replikálási folyamatot. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Hozza létre a feladatátvételi csoportot, és adja hozzá rugalmas készletét a PowerShell használatával. 

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

### <a name="test-failover"></a>Feladatátvételi teszt

A rugalmas készlet feladatátvételének tesztelése a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvétele a másodlagos kiszolgálóra történik, majd a Azure Portal használatával visszatérhet. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki a feladatátvevő csoportba felvenni kívánt rugalmas készletet. 
1. Az **Áttekintés** ablaktáblán válassza ki a kiszolgáló nevét a kiszolgáló **neve** alatt a kiszolgáló beállításainak megnyitásához.
  
    ![Rugalmas készlet kiszolgálójának megnyitása](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Válassza a **Beállítások** ablaktábla **feladatátvételi csoportok** elemét, majd válassza ki a 2. szakaszban létrehozott feladatátvételi csoportot. 
  
   ![Válassza ki a feladatátvételi csoportot a portálon](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Ellenőrizze, hogy melyik kiszolgáló elsődleges, és melyik kiszolgáló a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget a feladat ablaktáblán a rugalmas készletet tartalmazó feladatátvételi csoport feladatátvételéhez. 
1. Válassza az **Igen** lehetőséget arra a figyelmeztetésre, amely értesíti, hogy a TDS-munkamenetek le lesznek választva. 

   ![Az SQL-adatbázist tartalmazó feladatátvételi csoport feladatátvétele](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Tekintse át, hogy melyik kiszolgáló elsődleges, melyik kiszolgáló a másodlagos. Ha a feladatátvétel sikeres volt, a két kiszolgálónak felcserélt szerepkörrel kell rendelkeznie. 
1. Válassza újra a **feladatátvételt** , hogy a feladatátvételi csoportot vissza lehessen térni az eredeti beállításokhoz. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával.

Vizsgálja meg a másodlagos replika szerepkörét: 

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

Feladatátvétel a másodlagos kiszolgálóra: 

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

## <a name="managed-instance"></a>Felügyelt példány

Hozzon létre egy feladatátvételi csoportot két felügyelt példány között a Azure Portal vagy a PowerShell használatával. 

Létre kell hoznia egy átjárót az egyes felügyelt példányok virtuális hálózatához, össze kell kapcsolnia a két átjárót, majd létre kell hoznia a feladatátvételi csoportot.

### <a name="prerequisites"></a>Előfeltételek
Vegye figyelembe a következő előfeltételeket:

- A másodlagos felügyelt példánynak üresnek kell lennie.
- A másodlagos virtuális hálózat alhálózati tartománya nem fedi át az elsődleges virtuális hálózat alhálózatának tartományát. 
- A másodlagos példány rendezésének és időzónájának meg kell egyeznie az elsődleges példánnyal. 
- A két átjáró csatlakoztatásakor a **megosztott kulcsnak** mindkét kapcsolat esetében azonosnak kell lennie. 

### <a name="create-primary-virtual-network-gateway"></a>Elsődleges virtuális hálózati átjáró létrehozása 

Hozza létre az elsődleges virtuális hálózati átjárót a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Hozza létre az elsődleges virtuális hálózati átjárót a Azure Portal használatával. 

1. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, és válassza ki a **virtuális hálózati** erőforrást az elsődleges felügyelt példányhoz. 
1. Válassza ki az **alhálózatok** lehetőséget a **Beállítások** területen, majd válassza a lehetőséget egy új **átjáró-alhálózat**hozzáadásához. Hagyja meg az alapértelmezett értékeket. 

   ![Átjáró hozzáadása az elsődleges felügyelt példányhoz](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Az alhálózat-átjáró létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali navigációs ablaktáblán, majd `Virtual network gateway` írja be a keresőmezőbe a kifejezést. Válassza ki a **Microsoft**által közzétett **Virtual Network Gateway** -erőforrást. 

   ![Új virtuális hálózati átjáró létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Töltse ki a kötelező mezőket az elsődleges felügyelt példány átjárójának konfigurálásához. 

   Az alábbi táblázat az elsődleges felügyelt példány átjárója számára szükséges értékeket tartalmazza:
 
    | **Mező** | Value |
    | --- | --- |
    | **Előfizetés** |  Az az előfizetés, amelyben az elsődleges felügyelt példánya. |
    | **Name** | A virtuális hálózati átjáró neve. | 
    | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
    | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
    | **VPN-típus** | **Útvonal-alapú** |
    | **Termékváltozat**| Hagyja meg az `VpnGw1`alapértelmezett értéket. |
    | **Location**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
    | **Virtuális hálózat**| Válassza ki a virtuális hálózatot a másodlagos felügyelt példányhoz. |
    | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
    | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét. |
    | &nbsp; | &nbsp; |

1. Hagyja meg a többi értéket alapértelmezettként, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a virtuális hálózati átjáró beállításainak áttekintéséhez.

   ![Elsődleges átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Válassza a **Létrehozás** lehetőséget az új virtuális hálózati átjáró létrehozásához. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

Hozza létre a másodlagos virtuális hálózati átjárót a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Az előző szakaszban leírt lépések megismétlésével hozza létre a virtuális hálózati alhálózatot és az átjárót a másodlagos felügyelt példányhoz. Adja meg a szükséges mezőket a másodlagos felügyelt példány átjárójának konfigurálásához. 

   A következő táblázat a másodlagos felügyelt példány átjárója számára szükséges értékeket tartalmazza:

   | **Mező** | Value |
   | --- | --- |
   | **Előfizetés** |  Az előfizetés, amelyben a másodlagos felügyelt példánya van. |
   | **Name** | A virtuális hálózati átjáró neve, például `secondary-mi-gateway`:. | 
   | **Régió** | Az a régió, amelyben a másodlagos felügyelt példány található. |
   | **Átjáró típusa** | Válassza a **VPN**lehetőséget. |
   | **VPN-típus** | **Útvonal-alapú** |
   | **Termékváltozat**| Hagyja meg az `VpnGw1`alapértelmezett értéket. |
   | **Location**| A másodlagos felügyelt példány és a másodlagos virtuális hálózat helye.   |
   | **Virtuális hálózat**| Válassza ki a 2 `vnet-sql-mi-secondary`. szakaszban létrehozott virtuális hálózatot, például:. |
   | **Nyilvános IP-cím**| Válassza az **Új létrehozása** lehetőséget. |
   | **Nyilvános IP-cím neve**| Adja meg az IP-cím nevét, például `secondary-gateway-IP`:. |
   | &nbsp; | &nbsp; |

   ![Másodlagos átjáró beállításai](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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


### <a name="connect-the-gateways"></a>Az átjárók összekötése 
Hozzon létre kapcsolatokat a két átjáró között a Azure Portal vagy a PowerShell használatával. 

Két kapcsolatot kell létrehoznia: az elsődleges átjáró és a másodlagos átjáró közötti kapcsolat, majd a másodlagos átjáróról az elsődleges átjáró felé irányuló kapcsolat. 

Mindkét kapcsolathoz ugyanazt a megosztott kulcsot kell használni, mint az egyes kapcsolatok esetében. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
Hozzon létre kapcsolatokat a két átjáró között a Azure Portal használatával. 

1. Navigáljon az erőforráscsoporthoz a [Azure Portalban](https://portal.azure.com) , és válassza ki a 4. lépésben létrehozott elsődleges átjárót. 
1. A **Beállítások** területen válassza a **kapcsolatok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget egy új kapcsolat létrehozásához. 

   ![Elsődleges átjáróhoz való kapcsolódás hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Adja meg a kapcsolatok nevét, és írjon be egy értéket a **megosztott kulcshoz**. 
1. Válassza ki a **második virtuális hálózati átjárót** , majd válassza ki a másodlagos felügyelt példány átjáróját. 

   ![Elsődlegesről másodlagosra történő kapcsolódás létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Az új elsődleges – másodlagos átjárói kapcsolatok hozzáadásához kattintson **az OK gombra** .
1. Ismételje meg ezeket a lépéseket a másodlagos felügyelt példány átjárója és az elsődleges felügyelt példány átjárója közötti kapcsolatok létrehozásához. 

   ![Másodlagos – elsődleges kapcsolatok létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Hozza létre a felügyelt példányok feladatátvételi csoportját a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

Hozzon létre egy feladatátvételi csoportot a felügyelt példányok számára Azure Portal használatával. 

1. Válassza az **Azure SQL** lehetőséget a [Azure Portal](https://portal.azure.com)bal oldali menüjében. Ha az **Azure SQL** nem szerepel a listában, válassza a **minden szolgáltatás**lehetőséget, majd írja be az Azure SQL kifejezést a keresőmezőbe. Választható Válassza ki az **Azure SQL** melletti csillagot a kedvencekhez, és adja hozzá elemként a bal oldali navigációs sávon. 
1. Válassza ki azt az elsődleges felügyelt példányt, amelyet hozzá szeretne adni a feladatátvételi csoporthoz.  
1. A **Beállítások**területen navigáljon a **példány feladatátvételi csoportok** elemre, majd válassza a **Csoport hozzáadása** lehetőséget a **példány feladatátvételi csoport** lap megnyitásához. 

   ![Feladatátvételi csoport hozzáadása](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. A **példány feladatátvételi csoport** lapján írja be a feladatátvételi csoport nevét, majd válassza ki a másodlagos felügyelt példányt a legördülő menüből. Válassza a **Létrehozás** lehetőséget a feladatátvételi csoport létrehozásához. 

   ![Feladatátvételi csoport létrehozása](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. A feladatátvételi csoport központi telepítésének befejezése után vissza fog térni a **feladatátvételi csoport** lapra. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Hozzon létre egy feladatátvételi csoportot a felügyelt példányok számára a PowerShell használatával. 

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

### <a name="test-failover"></a>Feladatátvételi teszt

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal vagy a PowerShell használatával. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A feladatátvételi csoport feladatátvételi tesztje a Azure Portal használatával. 

1. Navigáljon a felügyelt példányhoz a [Azure Portalon](https://portal.azure.com) belül, és válassza a **példányok feladatátvételi csoportok** lehetőséget a beállítások területen. 
1. Tekintse át, hogy melyik felügyelt példány az elsődleges, és melyik felügyelt példány a másodlagos. 
1. Válassza a **feladatátvétel** lehetőséget, majd válassza az **Igen** lehetőséget a TDS-munkamenetek leválasztására vonatkozó figyelmeztetésben. 

   ![Feladatátvételi csoport feladatátvétele](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Tekintse át az elsődleges és a másodlagos példányt. Ha a feladatátvétel sikeres volt, a két példánynak átváltott szerepkörökkel kell rendelkeznie. 

   ![A felügyelt példányok a feladatátvételt követően váltanak át szerepköröket](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Ismét válassza a **feladatátvétel** lehetőséget, ha az elsődleges példányt vissza szeretné adni az elsődleges szerepkörnek. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A feladatátvételi csoport feladatátvételi tesztje a PowerShell használatával. 

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

## <a name="locate-listener-endpoint"></a>Figyelő végpontjának keresése

Miután konfigurálta a feladatátvételi csoportot, frissítse az alkalmazáshoz tartozó kapcsolódási karakterláncot a figyelő végpontján. Így az alkalmazás az elsődleges adatbázis, a rugalmas készlet vagy a felügyelt példány helyett a feladatátvételi csoport figyelőjét fogja csatlakoztatni. Így nem kell manuálisan frissítenie a kapcsolati karakterláncot, amikor az Azure SQL Database-entitás feladatátvételt hajt végre, és a rendszer átirányítja a forgalmat, hogy melyik entitás jelenleg elsődleges. 

A figyelő végpontja `fog-name.database.windows.net` formátumú, és látható a Azure Portalban a feladatátvételi csoport megtekintésekor:

![Feladatátvételi csoport kapcsolatainak karakterlánca](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Megjegyzések

- Egyetlen vagy készletezett adatbázis feladatátvételi csoportjának eltávolítása nem állítja le a replikálást, és nem törli a replikált adatbázist. Manuálisan le kell állítania a Geo-replikálást, és törölnie kell az adatbázist a másodlagos kiszolgálóról, ha egyetlen vagy készletezett adatbázist szeretne visszaadni egy feladatátvételi csoportba annak eltávolítása után. Ha a művelet végrehajtása sikertelen, akkor a `The operation cannot be performed due to multiple errors` hibához hasonló hibát eredményezhet, amikor az adatbázist a feladatátvételi csoportba kívánja adni. 


## <a name="next-steps"></a>További lépések

A feladatátvételi csoportok konfigurálásának részletes lépéseiért tekintse meg a következő oktatóanyagokat:
- [Egyetlen adatbázis hozzáadása egy feladatátvételi csoporthoz](sql-database-single-database-failover-group-tutorial.md)
- [Rugalmas készlet hozzáadása feladatátvételi csoporthoz](sql-database-elastic-pool-failover-group-tutorial.md)
- [Felügyelt példányok hozzáadása feladatátvételi csoporthoz](sql-database-managed-instance-failover-group-tutorial.md)
 
Az Azure SQL Database magas rendelkezésre állási lehetőségeinek áttekintését lásd: [geo-replikáció](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md). 
