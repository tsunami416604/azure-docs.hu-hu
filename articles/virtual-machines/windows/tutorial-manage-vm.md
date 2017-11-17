---
title: "Hozzon létre és kezelheti a Windows virtuális gépek az Azure PowerShell modulra |} Microsoft Docs"
description: "Az oktatóanyag - létrehozása és kezelése a Windows virtuális gépek az Azure PowerShell modul"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c612a251105197ab2b46bf448ae39253e5a65f36
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Hozzon létre és a Windows virtuális gépek kezelése az Azure PowerShell modul

Az Azure virtuális gépek adjon meg egy teljes mértékben konfigurálhatók és rugalmas számítási környezet. Ez az oktatóanyag ismerteti alapszintű Azure-beli virtuális gép telepítési elemek, például Virtuálisgép-méret kiválasztása, Virtuálisgép-lemezkép kiválasztása és a virtuális gépek telepítése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre, és csatlakoztassa a virtuális Gépet
> * Válassza ki és használja a Virtuálisgép-rendszerképek
> * Megtekintése és használata a megadott Virtuálisgép-méretek
> * Virtuális gép átméretezése
> * Megtekinteni és megérteni a virtuális gép állapota


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. 

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Egy erőforráscsoportot a virtuális gépek előtt létre kell hozni. Ebben a példában az erőforráscsoport neve *myResourceGroupVM* jön létre a *EastUS* régióban. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

Az erőforráscsoport létrehozása vagy módosítása egy virtuális Gépet, amelynek ez az oktatóanyag teljes látható során van megadva.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gépek kapcsolódnia kell egy virtuális hálózatot. A virtuális gépet egy nyilvános IP-cím használatával a hálózati kártya keresztül kommunikálnak.

### <a name="create-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy alhálózat [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

A virtuális hálózat létrehozása [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```azurepowershell-interactive
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása

Hozzon létre egy nyilvános IP-cím [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```azurepowershell-interactive
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Hálózati kártya létrehozása

Hozzon létre egy hálózati kártya [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```azurepowershell-interactive
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Egy Azure [hálózati biztonsági csoport](../../virtual-network/virtual-networks-nsg.md) (NSG) szabályozza a bejövő és kimenő forgalmat egy vagy több virtuális géphez. Hálózati biztonsági csoportszabályok engedélyezheti vagy letilthatja a hálózati forgalmat egy adott portot vagy porttartományt. Ezek a szabályok is hozzáadhat egy forráscímelőtag, hogy csak egy előre meghatározott forrás származó forgalmat a virtuális gépek kommunikálhatnak. A telepített IIS-webkiszolgáló szeretne használni, hozzá kell adnia a NSG bejövő szabály.

NSG bejövő szabály létrehozásához használható [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). Az alábbi példa létrehoz egy NSG-szabály nevű *myNSGRule* portot nyit meg, amely *3389-es* a virtuális géphez:

```azurepowershell-interactive
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Az NSG-t használó létrehozása *myNSGRule* rendelkező [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Az NSG hozzáadása a virtuális hálózatban az alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

A virtuális hálózat frissítése [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```azurepowershell-interactive
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

A virtuális gép létrehozásakor több lehetőség is elérhető, például az operációs rendszer lemezképét, a lemez méretezés és a felügyeleti hitelesítő adatokat. Ebben a példában egy virtuális gép létrehozása nevű *myVM* a Windows Server 2016 Datacenter legújabb verzióját.

Állítsa be a felhasználónevet és jelszót a rendszergazdai fiók, a virtuális gépen a szükséges [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Hozzon létre a virtuális gép kezdeti konfigurálásához [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Az operációs rendszer-adatokat hozzáadni a virtuálisgép-konfigurációnak [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```azurepowershell-interactive
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

A lemezkép-adatokat hozzáadni a virtuálisgép-konfigurációnak [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Az operációs rendszer lemezbeállításokat hozzáadása a virtuálisgép-konfigurációnak [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Hozzáadás a hálózati kártyát a virtuális gép konfigurációját a korábban létrehozott [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```azurepowershell-interactive
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Csatlakozás virtuális géphez

Az üzembe helyezés befejeztével hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

Futtassa a következő parancsokat a virtuális gép nyilvános IP-címének visszaadásához. Jegyezze fel ezt az IP-címet, hogy egy későbbi lépésben csatlakozhasson majd hozzá a böngészőjében a webes kapcsolat tesztelése érdekében.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géppel. Cserélje le az IP-címet a virtuális gépe *publicIPAddress* címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Virtuálisgép-rendszerképek ismertetése

Az Azure piactéren sok virtuálisgép-lemezképeket, amelyek segítségével hozzon létre egy új virtuális gépet tartalmaz. Az előző lépésben a virtuális gép létrehozása a Windows Server 2016-Datacenter lemezképpel. Ebben a lépésben a PowerShell-modul segítségével keresése a piactéren más Windows-lemezképek, amely is alapjaként új virtuális gépek esetén. Ezt a folyamatot, hogy a rendszer a közzétevő, az ajánlat és a lemezkép neve (Sku) áll. 

Használja a [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) parancs sikeresen lefut a kép közzétevők listájának.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Használja a [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) kép ajánlatok listájához való visszatéréshez. Ezzel a paranccsal a visszaadott lista a megadott közzétevő szűrve van. 

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

A [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) parancs majd szűrheti a közzétevő és ajánlat nevére képek nevének listájához való visszatéréshez.

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Ezek az információk segítségével olyan virtuális gép adott képének telepítéséhez. Ebben a példában a lemezkép neve beállítása a Virtuálisgép-objektum. Tekintse meg az előző példákban ebben az oktatóanyagban a teljes telepítési lépéseket.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Virtuálisgép-méretek ismertetése

A virtuális gép méretét határozza meg a számítási erőforrásokat, a virtuális gép számára elérhető például a CPU, grafikus Processzor és memória mennyisége. Virtuális gépek kell létrehozni a várt munkaterhelés megfelelő méretű. Ha növeli a munkaterhelés, egy meglévő virtuális gépet is átméretezhetők.

### <a name="vm-sizes"></a>Virtuálisgép-méretek

A következő táblázat azokat a használati esetek méretek kategorizálja.  

| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Általános célú         |DSv2, Dv2, DS, D, Av2, A0-7| Elosztott terhelésű CPU-memória. Épp ezért tökéletes választás a fejlesztői / tesztelői és kis, közepes méretű alkalmazásokhoz és adatokhoz megoldásokhoz.  |
| Számításra optimalizált      | FS, F             | Magas CPU-memória. Jó közepes forgalom alkalmazásokat, hálózati berendezéseket és kötegelt folyamatok.        |
| Memóriaoptimalizált       | GS, G, DSv2, DS, Dv2, D   | Nagy memória-CPU. A relációs adatbázisok, közepes vagy nagyméretű gyorsítótárak és memórián belüli analytics nagy.                 |
| Tárolásra optimalizált       | Ls                | Magas lemez-adatátviteli és I/O-műveleti jellemzők. Ideális Big Data-, SQL- és NoSQL-adatbázisok esetén.                                                         |
| GPU           | PORTOK HV, NC            | Nagy mennyiségű grafikus megjelenítési és videó szerkesztése szánt speciális virtuális gépeket.       |
| Kiemelkedő teljesítmény | H, A8-11          | A leghatékonyabb CPU rendelkező virtuális gépek nagy átviteli további hálózati adapterek (RDMA). 


### <a name="find-available-vm-sizes"></a>Elérhető Virtuálisgép-méretek keresése

Virtuálisgép-méretek érhető el az adott listájának megtekintéséhez használja a [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancsot.

```azurepowershell-interactive
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Virtuális gép átméretezése

A virtuális gépek telepítése után azt növeléséhez vagy csökkentéséhez az erőforrás-elosztás is átméretezhetők.

A virtuális gépek átméretezésével előtt ellenőrizze, hogy a kívánt méretet cím az aktuális virtuális gép fürtön. A [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) parancs méretek listáját adja vissza. 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Ha érhető el a kívánt méretet, a virtuális gép átméretezhető az alkalmazás bekapcsolja a állapotból, azonban a művelet során újraindítása után.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Ha a kívánt méretet nincs a jelenlegi fürthöz, a virtuális gép kell felszabadítása előtt az átméretezés akkor fordulhat elő. Vegye figyelembe, ha a virtuális gép be van kapcsolva vissza, a rendszer eltávolítja az ideiglenes lemezen lévő adatokhoz és a nyilvános IP-cím módosítása, kivéve, ha a statikus IP-cím használatban van. 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Virtuális gép energiaállapotokat

Egy Azure virtuális gép sok energiaállapotát egyike lehet. Ebben az állapotban lévő virtuális gép aktuális állapotát jeleníti meg a hipervizor szempontjából. 

### <a name="power-states"></a>Energiaállapotokat

| Energiaállapot | Leírás
|----|----|
| Indulás alatt | Azt jelzi, hogy a virtuális gép elindul. |
| Fut | Azt jelzi, hogy fut-e a virtuális gép. |
| Leállítás | Azt jelzi, hogy a virtuális gép leáll. | 
| Leállítva | Azt jelzi, hogy a virtuális gép leáll. Vegye figyelembe, hogy a virtuális gép leállított állapotban továbbra is fel Önnek költségeket.  |
| Felszabadítás | Azt jelzi, hogy a virtuális gép felszabadítása alatt. |
| Felszabadítva | Jelzi, hogy a virtuális gép teljesen eltávolították a hipervizor, de a vezérlő vezérlősík továbbra is elérhető. Virtuális gépek Deallocated állapotban nem számítunk költségeket. |
| - | Azt jelzi, hogy a teljesítmény a virtuális gép állapota ismeretlen. |

### <a name="find-power-state"></a>Energiaállapot keresése

Egy adott virtuális gép állapotának lekéréséhez használja a [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) parancsot. Ne adjon meg egy érvényes nevet a virtuális gép és erőforráscsoportban. 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName myResourceGroupVM `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Kimenet:

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Felügyeleti feladatok

A virtuális gépek életciklusa folyamán érdemes lehet indítása, leállítása vagy törlése a virtuális gépek például a felügyeleti feladatok futtatásához. Emellett érdemes lehet ismétlődő vagy összetett feladatok automatizálása parancsfájlok létrehozására. Az Azure PowerShell használatával számos gyakori felügyeleti feladatokat is futtatható a parancssorból vagy parancsfájlokban.

### <a name="stop-virtual-machine"></a>Virtuális gép leállítása

Állítsa le és a virtuális gép felszabadítása [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Ha meg szeretné tartani a virtuális gép kiosztott állapotú, akkor használja a - StayProvisioned paramétert.

### <a name="start-virtual-machine"></a>Virtuális gép elindítása

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Erőforráscsoport törlése

Erőforráscsoport törlésekor a belül található összes erőforrást is törlődnek.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóprogramban megismerte alapvető virtuális gép létrehozása és kezelése például hogyan:

> [!div class="checklist"]
> * Hozzon létre, és csatlakoztassa a virtuális Gépet
> * Válassza ki és használja a Virtuálisgép-rendszerképek
> * Megtekintése és használata a megadott Virtuálisgép-méretek
> * Virtuális gép átméretezése
> * Megtekinteni és megérteni a virtuális gép állapota

A következő oktatóanyag további információt a virtuális gépek lemezei továbblépés.  

> [!div class="nextstepaction"]
> [Hozzon létre és kezelheti a virtuális gépek lemezei](./tutorial-manage-data-disk.md)
