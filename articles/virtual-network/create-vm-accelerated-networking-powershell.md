---
title: Gyorsított hálózatkezelést biztosító Azure-beli virtuális gép létrehozása – Azure PowerShell
description: Megtudhatja, hogyan hozhat létre egy gyorsított hálózatkezeléssel rendelkező linuxos virtuális gépet.
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
ms.openlocfilehash: 3807c1434e3758eafe299da7b30769b41d3ede87
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106304"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking-using-azure-powershell"></a>Gyorsított hálózatkezeléssel rendelkező Windows rendszerű virtuális gép létrehozása Azure PowerShell használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy gyorsított hálózatkezeléssel rendelkező Windows rendszerű virtuális gépet (VM). A gyorsított hálózatkezelést biztosító linuxos virtuális gép létrehozásával kapcsolatban lásd: [Linux rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](create-vm-accelerated-networking-cli.md). A gyorsított hálózatkezelés lehetővé teszi az egyszintű I/O-virtualizálás (SR-IOV) használatát egy virtuális gépre, nagy mértékben javítja hálózati teljesítményét. Ez a nagy teljesítményű elérési út megkerüli a gazdagépet a DataPath, csökkenti a késést, a vibrálás és a CPU-kihasználtságot, és a legszigorúbb hálózati számítási feladatokhoz használja a támogatott virtuálisgép-típusoknál. Az alábbi képen a két virtuális gép közötti kommunikáció gyorsított hálózatkezeléssel és anélkül látható:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

A gyorsított hálózatkezelés nélkül a virtuális GÉPEN belüli és kívüli összes hálózati forgalomnak át kell haladnia a gazdagépen és a virtuális kapcsolón. A virtuális kapcsoló biztosítja az összes házirend-kényszerítést, például a hálózati biztonsági csoportokat, a hozzáférés-vezérlési listát, az elkülönítést és az egyéb, a hálózati forgalomhoz tartozó virtualizált szolgáltatásokat. További információ a virtuális kapcsolókról: [Hyper-V hálózati virtualizálás és virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx).

A gyorsított hálózatkezeléssel a hálózati forgalom a virtuális gép hálózati adapterén (NIC) érkezik, majd továbbítva lesz a virtuális géphez. A virtuális kapcsoló által érintett összes hálózati házirend már ki van kiszervezve és alkalmazva a hardveren. A szabályzat a hardveren való alkalmazása lehetővé teszi, hogy a hálózati adapter közvetlenül a virtuális gépre továbbítsa a hálózati forgalmat, megkerülve a gazdagépet és a virtuális kapcsolót, miközben a gazdagépen alkalmazott összes házirendet megőrzi.

A gyorsított hálózatkezelés előnyei csak azokra a virtuális gépekre érvényesek, amelyeken engedélyezve van a szolgáltatás. A legjobb eredmények elérése érdekében ideális, ha legalább két, ugyanahhoz az Azure-Virtual Networkhoz (VNet) csatlakoztatott virtuális gépen engedélyezi ezt a szolgáltatást. A virtuális hálózatok keresztüli kommunikáció vagy a helyszíni csatlakozás esetén ez a funkció minimális hatással van a teljes késésre.

## <a name="benefits"></a>Előnyök
* **Kisebb késés/nagyobb csomagok másodpercenként (PPS):** Ha eltávolítja a virtuális kapcsolót a DataPath, azzal eltávolítja a gazdagépen a házirendek feldolgozására fordított időt, és növeli a virtuális gépen belül feldolgozható csomagok számát.
* **Csökkentett Jitter:** A virtuális kapcsolók feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző CPU munkaterheléstől függ. A szabályzat kényszerítésének a hardverre való kiszervezése eltávolítja a változékonyságot a csomagok közvetlenül a virtuális géphez való továbbításával, a gazdagép és a virtuális gépek közötti kommunikáció, valamint az összes szoftveres megszakítás és környezeti kapcsoló eltávolításával.
* **Csökkent CPU-kihasználtság:** A gazdagépen lévő virtuális kapcsoló megkerülése kevesebb CPU-kihasználtságot eredményez a hálózati forgalom feldolgozásakor.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Az Azure-katalógusból az alábbi disztribúciók támogatottak:
* **Windows Server 2016 Datacenter** 
* **Windows Server 2012 R2 Datacenter**
* **Windows Server 2019 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott VM-példányok
A gyorsított hálózatkezelést a legtöbb általános célú és a számítási optimalizált példány mérete támogatja 2 vagy több vCPU.  A támogatott adatsorozatok a következők: D/DSv2 és F/FS

A feleznie támogató példányokon a gyorsított hálózatkezelést a 4 vagy több vCPU rendelkező virtuálisgép-példányok támogatják. Támogatott adatsorozatok: D/Dsv3, D/Dsv4, E/Esv3, EA/Easv4, Fsv2, Lsv2, MS/MMS és MS/Mmsv2.

A virtuálisgép-példányokkal kapcsolatos további információkért lásd: [Windows rendszerű virtuális gépek méretei](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Régiók
Elérhető az összes nyilvános Azure-régióban és Azure Government felhőben.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Gyorsított hálózatkezelés engedélyezése egy futó virtuális gépen
A gyorsított hálózatkezelést nem támogató, támogatott virtuálisgép-méret csak akkor engedélyezhető, ha a funkció le van állítva és fel van foglalva.

### <a name="deployment-through-azure-resource-manager"></a>Üzembe helyezés Azure Resource Manageron keresztül
A virtuális gépek (klasszikus) a gyorsított hálózatkezeléssel nem helyezhetők üzembe.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Windows rendszerű virtuális gép létrehozása az Azure gyorsított hálózatkezeléssel
## <a name="portal-creation"></a>Portál létrehozása
Bár ez a cikk a gyorsított hálózatkezelést használó virtuális gépek Azure PowerShell használatával történő létrehozásának lépéseit ismerteti, [a Azure Portal használatával létrehozhat egy gyorsított hálózatkezeléssel rendelkező virtuális gépet](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)is. Amikor virtuális gépet hoz létre a portálon, a **virtuális gép létrehozása** panelen válassza a **hálózatkezelés** lapot.  Ezen a lapon van lehetőség a **gyorsított hálózatkezelésre**.  Ha a [támogatott operációs rendszert](#supported-operating-systems) és virtuálisgép- [méretet](#supported-vm-instances)választotta, akkor ez a beállítás automatikusan betöltődik a "be" értékre.  Ha nem, akkor feltölti a felgyorsított hálózatkezelés "off" beállítását, és megadja a felhasználónak, hogy miért nem engedélyezhető.   
* *Megjegyzés:* Csak a támogatott operációs rendszerek engedélyezhetők a portálon keresztül.  Ha egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelést, akkor a parancssori felület vagy a PowerShell használatával hozza létre a virtuális gépet. 

A virtuális gép létrehozása után megerősítheti a gyorsított hálózatkezelést, ha azt szeretné, hogy a gyorsított hálózatkezelés engedélyezve legyen.

## <a name="powershell-creation"></a>PowerShell létrehozása
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Telepítse [Azure PowerShell](/powershell/azure/install-az-ps) a 1.0.0 vagy újabb verziót. A jelenleg telepített verziójának megkereséséhez `Get-Module -ListAvailable Az`futtassa a parancsot. Ha telepíteni vagy frissíteni szeretne, telepítse az az modul legújabb verzióját a [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az). Egy PowerShell-munkamenetben jelentkezzen be egy Azure-fiókba a Connection [-AzAccount](/powershell/module/az.accounts/connect-azaccount)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa a paraméterek neveire: *myResourceGroup*, *myNic*és *myVM*.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *CentralUS* helyen:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Először hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). A következő példa egy *mySubnet*nevű alhálózatot hoz létre:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Hozzon létre egy [új AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork)rendelkező virtuális hálózatot a *mySubnet* alhálózattal.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Először hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Hozzon létre egy hálózati biztonsági csoportot [új-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) , és rendelje hozzá az *Allow-RDP-minden* biztonsági szabályt. Az *Allow-RDP-all* szabály mellett a hálózati biztonsági csoport több alapértelmezett szabályt is tartalmaz. Egy alapértelmezett szabály letiltja az internetről érkező összes bejövő hozzáférést, ezért az *Allow-RDP – all* szabály hozzá van rendelve a hálózati biztonsági csoporthoz, hogy távolról csatlakozhasson a virtuális géphez a létrehozásuk után.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Társítsa a hálózati biztonsági csoportot a *mySubnet* alhálózathoz a [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A hálózati biztonsági csoport szabálya az alhálózaton üzembe helyezett összes erőforrásra érvényes.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hálózati adapter létrehozása gyorsított hálózatkezeléssel
Hozzon létre egy nyilvános IP [-címet a New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Nincs szükség nyilvános IP-cím megadására, ha nem tervezi a virtuális gép internetről való elérését, de a cikk lépéseinek elvégzéséhez szükség van rá.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Hozzon létre egy olyan hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) , amelyen engedélyezve van a gyorsított hálózat, és rendelje hozzá a nyilvános IP-címet a hálózati adapterhez. A következő példa létrehoz egy *myNic* nevű hálózati adaptert a *MyVnet* virtuális hálózat *mySubnet* alhálózatán, és hozzárendeli a *myPublicIp* nyilvános IP-címét:

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

Állítsa be a virtuális gép hitelesítő `$cred` adatait a változóba a [Get-hitelesítőadat](/powershell/module/microsoft.powershell.security/get-credential)használatával:

```powershell
$cred = Get-Credential
```

Először határozza meg a virtuális gépet a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A következő példa egy *myVM* nevű virtuális gépet definiál egy virtuálisgép-mérettel, amely támogatja a gyorsított hálózatkezelést (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

A virtuális gépek méretének és jellemzőinek listáját lásd: [Windows rendszerű virtuális gépek mérete](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Hozza létre a virtuális gép többi konfigurációját a [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) és a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). A következő példa egy Windows Server 2016 rendszerű virtuális gépet hoz létre:

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

Csatlakoztassa a korábban az [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)által létrehozott hálózati adaptert:

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Végül hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Ellenőrizze, hogy az illesztőprogram telepítve van-e az operációs rendszeren.

Miután létrehozta a virtuális gépet az Azure-ban, kapcsolódjon a virtuális géphez, és ellenőrizze, hogy az illesztőprogram telepítve van-e a Windows rendszerben.

1. Egy böngészőben nyissa meg az Azure [Portalt](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával.
2. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a következőt: *myVm*. Ha a **myVm** megjelenik a keresési eredmények között, kattintson rá. Ha a **Létrehozás** a **kapcsolat** gomb alatt látható, az Azure még nem fejezte be a virtuális gép létrehozását. Kattintson a **kapcsolat** gombra az Áttekintés bal felső sarkában, miután már nem látja a **Létrehozás** gombot a **kapcsolat** gomb alatt.
3. Adja meg a [virtuális gép létrehozásakor](#create-the-virtual-machine)megadott felhasználónevet és jelszót. Ha még soha nem kapcsolódott az Azure-beli Windows rendszerű virtuális géphez, tekintse meg a [Csatlakozás virtuális géphez](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine)című témakört.
4. Kattintson a jobb gombbal a Windows Start gombra, majd kattintson a **Eszközkezelő**elemre. Bontsa ki a **hálózati adapterek** csomópontot. Győződjön meg arról, hogy a **Mellanox ConnectX-3 virtuális funkció Ethernet-adaptere** megjelenik, ahogy az az alábbi képen is látható:

    ![Eszközkezelő](./media/create-vm-accelerated-networking/device-manager.png)

A gyorsított hálózatkezelés mostantól engedélyezve van a virtuális gépen.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Gyorsított hálózatkezelés engedélyezése meglévő virtuális gépeken
Ha felgyorsított hálózatkezelés nélküli virtuális gépet hozott létre, akkor lehetséges, hogy egy meglévő virtuális gépen engedélyezte ezt a funkciót.  A virtuális gépnek támogatnia kell a gyorsított hálózatkezelést az alábbi, a fent ismertetett előfeltételek teljesítésével:

* A virtuális gépnek a gyorsított hálózatkezeléshez támogatott méretnek kell lennie
* A virtuális gépnek támogatott Azure Gallery-rendszerképnek (és Linux kernel-verziónak) kell lennie
* A rendelkezésre állási csoportokban vagy VMSS lévő virtuális gépeket le kell állítani/fel kell osztani a gyorsított hálózatkezelés bármely hálózati adapteren való engedélyezése előtt.

### <a name="individual-vms--vms-in-an-availability-set"></a>A rendelkezésre állási csoportba tartozó egyes virtuális gépek & virtuális gépek
Először állítsa le/szabadítsa fel a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Fontos megjegyezni, hogy ha a virtuális gépet különállóan hozták létre, rendelkezésre állási csoport nélkül, a gyorsított hálózatkezelés engedélyezéséhez csak le kell állítania/fel kell szabadítania az egyes virtuális gépeket.  Ha a virtuális gép rendelkezésre állási csoporttal lett létrehozva, a rendelkezésre állási csoportba tartozó összes virtuális gépet le kell állítani/fel kell szüntetni, mielőtt engedélyezné a gyorsított hálózatkezelést bármelyik hálózati adapteren. 

Ha leállt, engedélyezze a gyorsított hálózatkezelést a virtuális gép hálózati adapterén:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Indítsa újra a virtuális gépet, vagy ha rendelkezésre állási csoport, a készletben lévő összes virtuális gép, és ellenőrizze, hogy engedélyezve van-e a gyorsított hálózat:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
A VMSS némileg eltérő, de ugyanazt a munkafolyamatot követi.  Először állítsa le a virtuális gépeket:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

A virtuális gépek leállítása után frissítse a gyorsított hálózatkezelési tulajdonságot a hálózati adapter alatt:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Vegye figyelembe, hogy a VMSS olyan virtuálisgép-frissítésekkel rendelkezik, amelyek három különböző beállítással, automatikus, működés közbeni és manuális beállításokkal alkalmazzák a frissítéseket.  Ebben az útmutatóban a szabályzat automatikusra van beállítva, hogy a VMSS az újraindítást követően azonnal vegye fel a módosításokat.  Ha automatikusra szeretné állítani, hogy a módosítások azonnal fellegyenek, válassza a következőt:

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Végezetül indítsa újra a VMSS:

```azurepowershell
Start-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Ha újraindul, várjon, amíg a frissítés befejeződik, de ha elkészült, a VF a virtuális gépen fog megjelenni.  (Győződjön meg róla, hogy támogatott operációs rendszert és virtuálisgép-méretet használ)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Meglévő virtuális gépek átméretezése gyorsított hálózatkezeléssel

A gyorsított hálózatkezelést használó virtuális gépeket csak a gyorsított hálózatkezelést támogató virtuális gépekre lehet átméretezni.  

A gyorsított hálózatkezelést használó virtuális gépek nem méretezhetők át olyan virtuálisgép-példányra, amely nem támogatja a gyorsított hálózatkezelést az átméretezési művelettel.  Ehelyett a következő virtuális gépek egyikének átméretezéséhez:

* Állítsa le/szabadítsa fel a virtuális gépet, vagy ha rendelkezésre állási csoport/VMSS, állítsa le/szabadítsa fel a set/VMSS összes virtuális gépét.
* A gyorsított hálózatkezelést le kell tiltani a virtuális gép hálózati adapterén, vagy ha rendelkezésre állási csoport/VMSS, a set/VMSS összes virtuális gépe.
* Ha a gyorsított hálózatkezelés le van tiltva, a virtuális gép/rendelkezésre állási csoport/VMSS olyan új méretre helyezhető át, amely nem támogatja a gyorsított hálózatkezelést és az újraindítást.
