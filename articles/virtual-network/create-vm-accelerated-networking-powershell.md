---
title: Hozzon létre egy Azure virtuális gép gyorsított hálózatkezelés - Azure PowerShell
description: Ismerje meg, hogyan hozhat létre egy Linux virtuális gépet gyorsított hálózatkezeléssel.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: 16837782af2f08e27363091dc21587a100194cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245056"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Windows-alapú virtuális gép létrehozása gyorsított hálózatkezeléssel az Azure PowerShell használatával

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy Windows virtuális gépet (VM) gyorsított hálózattal. Ha gyorsított hálózati linuxos virtuális gépről szeretne beszélni, olvassa el a Linux virtuális gép létrehozása gyorsított hálózattal című [témakört.](create-vm-accelerated-networking-cli.md) A gyorsított hálózatkezelés lehetővé teszi az egygyökérű I/O-virtualizációt (SR-IOV) a virtuális géphez, jelentősen javítva a hálózati teljesítményt. Ez a nagy teljesítményű elérési út megkerüli a gazdagép a datapath, csökkenti a késést, a vibráló és a CPU-kihasználtság, a támogatott virtuálisgép-típusok legigényesebb hálózati munkaterhelések használható. Az alábbi képen két virtuális gép közötti kommunikáció látható gyorsított hálózattal és anélkül:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül a virtuális gépbe be- és kifutó összes hálózati forgalomnak át kell haladnia a gazdagépen és a virtuális kapcsolón. A virtuális kapcsoló minden házirend-kényszerítést biztosít, például a hálózati biztonsági csoportokat, a hozzáférés-vezérlési listákat, az elkülönítést és a hálózati forgalomhoz való egyéb hálózati virtualizált szolgáltatásokat. A virtuális kapcsolókról a [Hyper-V hálózati virtualizáció és a virtuális kapcsoló további információja.](https://technet.microsoft.com/library/jj945275.aspx)

A gyorsított hálózati kapcsolat, a hálózati forgalom érkezik a virtuális gép hálózati adapter (NIC), majd továbbítja a virtuális gép. A virtuális kapcsolóáltal alkalmazott összes hálózati házirend kivan töltve, és a hardverben is érvénybe lép. A hardveres házirend alkalmazása lehetővé teszi, hogy a hálózati adapter közvetlenül a virtuális gépre továbbítsa a hálózati forgalmat, megkerülve az állomást és a virtuális kapcsolót, miközben az állomásban alkalmazott összes házirendet fenntartja.

A gyorsított hálózatkezelés előnyei csak a virtuális gép, amely engedélyezve van. A legjobb eredmény érdekében ideális, ha ezt a funkciót legalább két, ugyanahhoz az Azure virtuális hálózathoz (VNet) csatlakoztatott virtuális gépeken engedélyezi. Virtuális hálózatok közötti kommunikáció vagy a helyszíni csatlakozás, ez a szolgáltatás minimális hatással van az általános késésre.

## <a name="benefits"></a>Előnyök
* **Kisebb késleltetés / Magasabb csomagok másodpercenként (pps):** A virtuális kapcsoló eltávolítása a datapath eltávolítja az időt csomagok töltenek a gazdagép a házirend-feldolgozás, és növeli a csomagok számát, amelyek a virtuális gépen belül feldolgozható.
* **Csökkentett vibrátor:** A virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző processzor terhelésétől függ. A házirend-kényszerítés hardverre való kiszervezése eltávolítja ezt a változékonyságot azáltal, hogy a csomagokat közvetlenül a virtuális gépnek kézbesíti, eltávolítja a gazdagép ről a virtuális gépre, valamint az összes szoftvermegszakítást és környezetkapcsolót.
* **Csökkent CPU-kihasználtság:** A virtuális kapcsoló megkerülése a gazdagépben kevesebb PROCESSZOR-kihasználtságot eredményez a hálózati forgalom feldolgozásához.

## <a name="limitations-and-constraints"></a>Korlátozások és korlátozások

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure Gallery-ből származó dobozból támogatottak:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott virtuálisgép-példányok
Gyorsított hálózatkezelés támogatott a legtöbb általános célú és számítási optimalizált példány mérete2 vagy több vCPU-k.  Ezek a támogatott sorozatok a következők: D/DSv2 és F/Fs

A hyperthreading-et támogató példányokon a gyorsított hálózatkezelés 4 vagy több vCPU-val rendelkező virtuálisgép-példányokon támogatott. Támogatott sorozatok: D/Dsv3, E/Esv3, Fsv2, Lsv2, Ms/Mms és Ms/Mmsv2.

A virtuálisgép-példányokkal kapcsolatos további tudnivalókért olvassa el [a Windows virtuálisgépek méretei című témakört.](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="regions"></a>Régiók
Minden nyilvános Azure-régióban és az Azure Government Cloudban elérhető.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Gyorsított hálózatkezelés engedélyezése futó virtuális gépen
A támogatott virtuális gép mérete gyorsított hálózati engedélyezés nélkül csak akkor engedélyezve van a funkció, ha le van állítva, és felszabadított.

### <a name="deployment-through-azure-resource-manager"></a>Telepítés az Azure Resource Manageren keresztül
A (klasszikus) virtuális gépek nem telepíthetők gyorsított hálózatkezeléssel.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Windows virtuális gép létrehozása az Azure Accelerated Networking segítségével
## <a name="portal-creation"></a>Portál létrehozása
Bár ez a cikk lépéseket tartalmaz egy virtuális gép létrehozásához gyorsított hálózatkezelés az Azure Powershell használatával, az [Azure Portalon gyorsított hálózatkezeléssel rendelkező virtuális gépet](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)is létrehozhat. Amikor virtuális gépet hoz létre a portálon, a **Virtuálisgép létrehozása** panelen válassza a **Hálózat** lapot.  Ezen a lapon lehetőség van a **gyorsított hálózatkezelésre.**  Ha [támogatott operációs rendszert](#supported-operating-systems) és virtuális [gépméretet](#supported-vm-instances)választott, ez a beállítás automatikusan "Be" állapotba kerül.  Ha nem, akkor feltölti a "Off" opciót gyorsított hálózat, és adja meg a felhasználónak egy ok, hogy miért nincs engedélyezve.   
* *Megjegyzés:* Csak a támogatott operációs rendszerek engedélyezhetők a portálon keresztül.  Ha egyéni lemezképet használ, és a lemezkép támogatja az Accelerated Networking, kérjük, hozza létre a virtuális gép cli vagy Powershell használatával. 

A virtuális gép létrehozása után ellenőrizheti, hogy az Accelerated Networking engedélyezve van-e a Gyorsított hálózat kezelés engedélyezésének megerősítéséhez a Gyorsított hálózatengedélyezés engedélyezésének megerősítésére vonatkozó utasításokat követve.

## <a name="powershell-creation"></a>Powershell létrehozása
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Telepítse [az Azure PowerShell](/powershell/azure/install-az-ps) 1.0.0-s vagy újabb verzióját. A jelenleg telepített verzió `Get-Module -ListAvailable Az`megkereséséhez futtassa a futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell, telepítse az Az modul legújabb verzióját a [PowerShell-galériából.](https://www.powershellgallery.com/packages/Az) PowerShell-munkamenetben jelentkezzen be egy Azure-fiókba a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)használatával.

A következő példákban cserélje le a példaparaméterneveket a saját értékeire. Példa paraméter nevek közé *myResourceGroup*, *myNic*, és *myVM*.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)segítségével. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *centralus* helyen:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Először hozzon létre egy alhálózati konfigurációt a [New-AzVirtualSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig)segítségével. A következő példa létrehoz egy *sajka nevű alhálózatot:*

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), a *mySubnet* alhálózat.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Először hozzon létre egy hálózati biztonsági csoportszabályt a [New-AzNetworkSecurityRuleConfig segítségével.](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig)

```powershell
$rdp = New-AzNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup csoporttal,](/powershell/module/az.Network/New-azNetworkSecurityGroup) és rendelje hozzá az *Allow-RDP-All* biztonsági szabályt. Az *Allow-RDP-All* szabály mellett a hálózati biztonsági csoport több alapértelmezett szabályt is tartalmaz. Egy alapértelmezett szabály letiltja az összes bejövő hozzáférést az internetről, ezért az *Allow-RDP-All* szabály a hálózati biztonsági csoporthoz van rendelve, így a létrehozásután távolról csatlakozhat a virtuális géphez.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Társítsa a hálózati biztonsági csoportot a *mySubnet* alhálózathoz a [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig)segítségével. A hálózati biztonsági csoportban lévő szabály az alhálózatban üzembe helyezett összes erőforrásra érvényes.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hálózati adapter létrehozása gyorsított hálózatkezeléssel
Hozzon létre egy nyilvános [IP-címet a New-AzPublicIpAddress címmel.](/powershell/module/az.Network/New-azPublicIpAddress) Nyilvános IP-cím nem szükséges, ha nem tervezi a virtuális gép elérését az internetről, de a jelen cikkben leírt lépések végrehajtásához szükséges.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Hozzon létre egy hálózati adaptert [a New-AzNetworkInterface-lel,](/powershell/module/az.Network/New-azNetworkInterface) amelynek engedélyezett a hálózat, és rendelje hozzá a nyilvános IP-címet a hálózati adapterhez. A következő példa létrehoz egy *myNic* nevű hálózati illesztőt a *myVnet* virtuális hálózat *mySubnet* alhálózatában, és hozzárendeli a *myPublicIp* nyilvános IP-címet:

```powershell
$nic = New-AzNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

Állítsa be a virtuális `$cred` gép hitelesítő adatait a változóhoz a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)használatával:

```powershell
$cred = Get-Credential
```

Először definiálja a virtuális gép ét a [New-AzVMConfig segítségével.](/powershell/module/az.compute/new-azvmconfig) A következő példa egy *myVM* nevű virtuális gép, amelynek virtuális gépmérete támogatja a gyorsított hálózati *(Standard_DS4_v2)* nevet:

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

A virtuális gépek méretének és jellemzőinek listáját a [Windows virtuálisgép-méretek (Windows VM-méretek)](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)tartalmazza.

Hozza létre a vm-konfiguráció többi részét a [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) és [a Set-AzVMSourceImage segítségével.](/powershell/module/az.compute/set-azvmsourceimage) A következő példa létrehoz egy Windows Server 2016 virtuális gép:

```powershell
$vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Csatolja a korábban [az Add-AzVMNetworkInterface kapcsolatban](/powershell/module/az.compute/add-azvmnetworkinterface)létrehozott hálózati adaptert:

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Végül hozza létre a virtuális gép a [New-AzVM:](/powershell/module/az.compute/new-azvm)

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Az illesztőprogram operációs rendszerbe való telepítésének ellenőrzése

Miután létrehozta a virtuális gép az Azure-ban, csatlakozzon a virtuális géphez, és ellenőrizze, hogy az illesztőprogram telepítve van-e a Windows rendszerben.

1. Egy internetböngészőből nyissa meg az Azure [Portalt,](https://portal.azure.com) és jelentkezzen be Azure-fiókjával.
2. Az Azure Portal tetején található *Keresési erőforrások at* tartalmazó mezőbe írja be a *myVm*kifejezést. Amikor **a myVm** megjelenik a keresési eredmények között, kattintson rá. Ha a **Létrehozás** a **Csatlakozás** gomb alatt látható, az Azure még nem fejezte be a virtuális gép létrehozását. Kattintson a **Csatlakozás** gombra az áttekintés bal felső sarkában, miután már nem látja **a** **Létrehozás** gombot.
3. Adja meg a [virtuális gép létrehozása mezőbe](#create-the-virtual-machine)beírt felhasználónevet és jelszót. Ha még soha nem csatlakozott Windows virtuális géphez az Azure-ban, olvassa el [a Csatlakozás a virtuális géphez (Csatlakozás a virtuális géphez) (Csatlakozás a virtuális géphez) (Csatlakozás a virtuális géphez)](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)témakört.
4. Kattintson a jobb gombbal a Windows Start gombjára, és válassza az **Eszközkezelő**t. Bontsa ki a **Hálózati adapterek csomópontját.** Ellenőrizze, hogy megjelenik-e a **Mellanox ConnectX-3 virtuális funkciós Ethernet adapter,** ahogy az az alábbi képen látható:

    ![Eszközkezelő](./media/create-vm-accelerated-networking/device-manager.png)

Gyorsított hálózatkezelés most engedélyezve van a virtuális gép.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Gyorsított hálózatkezelés engedélyezése meglévő virtuális gépeken
Ha gyorsított hálózatkezelés nélkül hozott létre virtuális gép, ez a funkció egy meglévő virtuális gépen is engedélyezheti.  A virtuális gépnek támogatnia kell az accelerated networking-et a fent vázolt alábbi előfeltételek teljesítésével:

* A virtuális gépnek támogatott méretűnek kell lennie a gyorsított hálózatkezeléshez
* A virtuális gépnek egy támogatott Azure Gallery-lemezképnek (és Linux kernelverziónak) kell lennie.
* A rendelkezésre állási csoportban vagy a VMSS-ben lévő összes virtuális gépet le kell állítani/fel kell osztani, mielőtt bármilyen hálózati adapteren engedélyeznék a gyorsított hálózatkezelést.

### <a name="individual-vms--vms-in-an-availability-set"></a>Egyéni virtuális gépek & virtuális gépek egy rendelkezésre állási csoportban
A virtuális gép első leállítása/felszabadítása, vagy ha egy rendelkezésre állási csoport, a készlet összes virtuális gépe:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Fontos, vegye figyelembe, ha a virtuális gép külön-külön jött létre, rendelkezésre állási csoport nélkül, csak le kell állítania/felkell osztania az egyes virtuális gépek et a gyorsított hálózatkezelés engedélyezéséhez.  Ha a virtuális gép egy rendelkezésre állási csoport, a rendelkezésre állási csoportban található összes virtuális gépet le kell állítani/fel kell osztani, mielőtt a hálózati adapterek gyorsított hálózatba foglalását engedélyeznék. 

A leállított beállítás után engedélyezze a gyorsított hálózatkezelést a virtuális gép hálózati adapterén:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Indítsa újra a virtuális gépet, vagy ha egy rendelkezésre állási csoportban van, a készlet összes virtuális gépe, és győződjön meg arról, hogy a gyorsított hálózatkezelés engedélyezve van:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
A VMSS kissé eltérő, de ugyanazt a munkafolyamatot követi.  Először állítsa le a virtuális gépeket:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

A virtuális gépek leállítása után frissítse a gyorsított hálózati tulajdonságot a hálózati adapter alatt:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Kérjük, vegye figyelembe, hogy a VMSS virtuális gép frissítéseket, amelyek a frissítéseket három különböző beállítások, automatikus, gördülő és kézi használatával.  Ezekben az utasításokban a házirend automatikus, így a VMSS azonnal felveszi a módosításokat az újraindítás után.  Állítsa be automatikusra, hogy a módosítások azonnal felvesd:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Végül indítsa újra a VMSS-t:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Miután újraindítja, várja meg, amíg a frissítések befejeződnek, de ha befejeződött, a VF jelenik meg a virtuális gép belsejében.  (Győződjön meg arról, hogy támogatott operációs rendszert és virtuális gépméretet használ)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Meglévő virtuális gépek átméretezése gyorsított hálózatkezeléssel

A gyorsított hálózatkezeléssel rendelkező virtuális gépek csak olyan virtuális gépekre méretezhetők át, amelyek támogatják az accelerated networking szolgáltatást.  

A gyorsított hálózatkezelés engedélyezve lévő virtuális gép nem méretezhető át olyan virtuálisgép-példányra, amely nem támogatja az újraméretezési művelet használatával végzett gyorsított hálózatkezelést.  Ehelyett az alábbi virtuális gépek egyikének átméretezéséhez:

* Állítsa le/szabadítsa fel a virtuális gépet, vagy ha egy rendelkezésre állási csoportban/VMSS, állítsa le/szabadítsa fel az összes virtuális gép a készlet/VMSS.
* Gyorsított hálózatletiltást kell tiltani a hálózati adapteren a virtuális gép, vagy ha egy rendelkezésre állási készlet/VMSS, a készlet/VMSS összes virtuális gép.
* Miután a gyorsított hálózatkezelés le van tiltva, a virtuális gép/rendelkezésre állási készlet/VMSS áthelyezhető egy új méretre, amely nem támogatja az accelerated networking és újraindul.
