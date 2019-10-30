---
title: Azure Private-végpont létrehozása a Azure PowerShell használatával | Microsoft Docs
description: További információ az Azure Private linkről
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 91670d51328b3adb67ba8b2ed05d3b86f9bc0010
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053929"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Privát végpont létrehozása Azure PowerShell használatával
A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. 

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépet Azure-Virtual Network, egy Azure Private-végponttal rendelkező SQL Database-kiszolgálót Azure PowerShell használatával. Ezután biztonságosan hozzáférhet a SQL Database-kiszolgálóhoz a virtuális gépről.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforrások létrehozása előtt létre kell hoznia egy erőforráscsoportot, amely a Virtual Network és a privát végpontot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)tárolja. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *WestUS* helyen:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Virtual Network létrehozása
Ebben a szakaszban egy virtuális hálózatot és egy alhálózatot hoz létre. Ezután rendelje hozzá az alhálózatot a Virtual Networkhoz.

### <a name="create-a-virtual-network"></a>Virtual Network létrehozása

Hozzon létre egy virtuális hálózatot a privát végponthoz a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Az alábbi példa egy *MyVirtualNetwork*nevű Virtual Network hoz létre:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

Az Azure egy Virtual Networkon belüli alhálózatra helyezi az erőforrásokat, ezért létre kell hoznia egy alhálózatot. Hozzon létre egy *mySubnet* nevű alhálózati konfigurációt az [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)paranccsal. A következő példa létrehoz egy *mySubnet* nevű alhálózatot a privát végpont hálózati házirendjének jelzővel, amely **le van tiltva**.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Az alhálózat hozzárendelése a Virtual Networkhoz

Az alhálózat konfigurációját a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)használatával írhatja be a Virtual Network. Ez a parancs az alhálózatot hozza létre:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a Virtual Network a [New-AzVM](/powershell/module/az.compute/new-azvm). A következő parancs futtatásakor a rendszer kéri a hitelesítő adatokat. Adja meg a virtuális gép felhasználónevét és jelszavát:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

A `-AsJob` lehetőség a virtuális gépet a háttérben hozza létre. Folytassa a következő lépéssel.

Amikor az Azure a háttérben elkezdi létrehozni a virtuális gépet, a következőhöz hasonló lesz:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>SQL Database-kiszolgáló létrehozása 

Hozzon létre egy SQL Database-kiszolgálót a New-AzSqlServer parancs használatával. Ne feledje, hogy a SQL Database-kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása

A Virtual Network SQL Database-kiszolgáló magánhálózati végpontja a [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection): 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása 
Hozzon létre egy magánhálózati DNS-zónát SQL Database Server-tartományhoz, és hozzon létre egy társítási hivatkozást a virtuális hálózattal: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

A [Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress) használatával visszaállíthatja egy virtuális gép nyilvános IP-címét. Ez a példa a *myVM* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Nyisson meg egy parancssort a helyi számítógépen. Futtassa az mstsc parancsot. Cserélje le a <publicIpAddress>t az utolsó lépésből visszaadott nyilvános IP-címmel: 


> [!NOTE]
> Ha ezeket a parancsokat egy PowerShell-parancssorból futtatta a helyi számítógépen, és az az PowerShell-modul 1,0-es vagy újabb verzióját használja, akkor folytathatja a felületet.
```
mstsc /v:<publicIpAddress>
```

1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. 
2. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.
  > [!NOTE]
  > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell kiválasztania > eltérő fiókot használjon. 
  
3. Kattintson az **OK** gombra. 
4. A tanúsítványra vonatkozó figyelmeztetés jelenhet meg. Ha így tesz, válassza az **Igen** vagy a **Folytatás**lehetőséget. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database kiszolgáló magánhálózati elérése a virtuális gépről

1. A myVM Távoli asztal nyissa meg a PowerShellt.
2. Írja be a `nslookup myserver.database.windows.net` (igen) kifejezést. 

    Ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. SQL Server Management Studio telepítése
4. A Kapcsolódás a kiszolgálóhoz lapon adja meg vagy válassza ki ezt az információt: érték-kiszolgáló típusának beállítása adatbázismotor.
      Kiszolgáló neve válassza a myserver.database.windows.net felhasználónevet, adja meg a létrehozás során megadott felhasználónevet.
      A jelszó mezőbe írja be a létrehozás során megadott jelszót.
      Jelszó megjegyzése: válassza az Igen lehetőséget.
5. Válassza a kapcsolat lehetőséget.
6. A bal oldali menüben lévő adatbázisok tallózása. 
7. Opcionálisan Információk létrehozása vagy lekérdezése a mydatabase
8. A távoli asztali kapcsolat bezárásával *myVM*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végponttal, SQL Database-kiszolgálóval és a virtuális géppel, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával távolítsa el az erőforráscsoportot és az összes erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private linkről](private-link-overview.md)
