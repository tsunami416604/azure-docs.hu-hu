---
title: Telepítse és konfigurálja az Azure PowerShell-lel az Azure-tűzfal
description: Ebből a cikkből megismerheti, hogyan telepítheti és konfigurálhatja az Azure-tűzfal az Azure PowerShell használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.openlocfilehash: c2d49defa2e0fbbd12c5403ccca74e91cf4ec981
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502113"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Telepítse és konfigurálja az Azure PowerShell-lel az Azure-tűzfal

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Például előfordulhat, hogy szeretné korlátozni a webhelyekhez való hozzáférést. Vagy előfordulhat, hogy szeretné korlátozni a kimenő IP-címek és portok elérhető.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben a cikkben létrehoz egy egyszerűsített egyetlen virtuális hálózaton az egyszerű telepítés három alhálózatot. Éles környezetekben üzemelő példányok egy [küllős modell](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata javasolt, ha a tűzfal van-e a saját virtuális hálózatában. A számítási feladatok kiszolgálók társviszonyban lévő virtuális hálózatok egy vagy több alhálózattal rendelkező ugyanabban a régióban találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.
* **Jump-SN** – ezen az alhálózaton található a helyettesítő kiszolgáló. A helyettesítő kiszolgáló egy nyilvános IP-címmel rendelkezik, amelyhez csatlakozni lehet a távoli asztal használatával. Innen pedig egy másik távoli asztallal lehet csatlakozni a számítási feladat kiszolgálójához.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazás www.google.com való hozzáférés engedélyezése a szabály konfigurálása
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * A tűzfal tesztelése

Ha szeretné, ez az eljárás használatával elvégezhető a [az Azure portal](tutorial-firewall-deploy-portal.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ezt az eljárást a PowerShell helyi futtatása szükséges. Az Azure PowerShell-modul telepítve kell rendelkeznie. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-Az-ps) ismertető cikket. A PowerShell-verzió ellenőrzése után futtassa az `Connect-AzAccount` parancsot az Azure-hoz való kapcsolódáshoz.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy virtuális hálózatot, alhálózatokat és tesztkiszolgálókat.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport tartalmazza a központi telepítés összes erőforrást.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Ez a virtuális hálózat rendelkezik három alhálózatot:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/24
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```

> [!NOTE]
> A AzureFirewallSubnet alhálózat minimális mérete/26-os.

Hozza létre a virtuális hálózat:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Most hozza létre a helyettesítő és a számítási feladatokat futtató virtuális gépeket, és helyezze el őket a megfelelő alhálózatokon.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

Az Srv-Jump virtuális gép létrehozása.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Hozzon létre a munkaterhelési virtuális gép nyilvános IP-cím.
Amikor a rendszer kéri, adjon meg egy felhasználónevet és jelszót a virtuális gép számára.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Már üzembe helyezheti a tűzfal a virtuális hálózatban.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Hozzon létre egy tábla BGP-útvonalpropagálás le van tiltva

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Az alkalmazás a szabály lehetővé teszi, hogy a kimenő hozzáférést www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

A hálózati szabály lehetővé teszi, hogy a két IP-cím (DNS) 53-as porton a kimenő hozzáférést.

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Tesztelési célokra ezzel az eljárással konfigurálhatja a kiszolgáló az elsődleges és másodlagos DNS-címek. Ez nem egy általános Azure tűzfallal kapcsolatos követelmény.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje le, a tűzfal, ellenőrizze, hogy az elvárt módon működik.

1. Vegye figyelembe a magánhálózati IP-címét a **Srv-munkahelyi** virtuális gépet:

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Csatlakozzon a távoli asztali **Srv-Jump** virtuális gépet, és jelentkezzen be. Itt, nyissa meg a távoli asztali kapcsolatot a **Srv-munkahelyi** magánhálózati IP-címet, és jelentkezzen be.

3. A **SRV-munkahelyi**, nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsokat:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Mindkét parancsot kell visszaadnia a válaszokat, megjelenítése, hogy a DNS-lekérdezések első vannak-e a tűzfalon keresztül.

1. Futtassa az alábbi parancsot:

   ```
   Invoke-WebRequest -Uri http://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri http://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   A www.google.com kérelmek sikeres legyen, és a www.microsoft.com kérelmek sikertelenek. Ez azt mutatja be, hogy a tűzfalszabályok vannak a várt módon működik.

Így most már ellenőrizte, hogy a tűzfalszabályok működnek:

* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.
* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfal erőforrások megtarthatja a következő oktatóanyagra, vagy ha már nincs rá szükség, törölje a **Test-Keretrendszer-RG** tűzfal kapcsolódó összes erőforrást törölheti az erőforráscsoportot:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: A figyelő Azure tűzfal-naplókon](./tutorial-diagnostics.md)
