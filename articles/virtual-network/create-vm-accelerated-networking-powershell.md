---
title: Hozzon létre egy Azure virtuális gép gyorsított hálózatkezelésű |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Linux rendszerű virtuális gép gyorsított hálózatkezelésű.
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
ms.openlocfilehash: c4567919490c8bc9094dea3dddbe22550d9eebb2
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192905"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Gyorsított hálózatkezelésű Windows virtuális gép létrehozása

Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy Windows virtuális gép (VM) gyorsított hálózatkezelésű. Linux rendszerű virtuális gép gyorsított hálózatkezelésű létrehozásához lásd: [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű](create-vm-accelerated-networking-cli.md). Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, nagy mértékben javítva a hálózati teljesítmény. A nagy teljesítményű elérési út megkerüli a gazdagép a datapath csökkenti a késés, a jitter és a Processzor kihasználtsága a legnagyobb hálózati számítási a támogatott Virtuálisgép-típusok való használatra. Az alábbi képen látható a két virtuális gépet, és anélkül gyorsított hálózatkezelés közötti kommunikáció:

![Összehasonlítás](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül a-ból a virtuális gép minden hálózati forgalmat kell haladnak át, a gazdagép és a virtuális kapcsolóhoz. A virtuális kapcsoló az összes házirend betartatása, például a hálózati biztonsági csoportok, hozzáférés-vezérlési listák, elkülönítési és egyéb hálózati forgalom virtualizált hálózati szolgáltatások biztosít. Virtuális kapcsolók kapcsolatos további információkért lásd: [Hyper-V hálózatvirtualizálás és a virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx).

Gyorsított hálózatkezeléssel, a hálózati forgalom szolgáltatáshoz érkezik, a virtuális gép hálózati adapteréhez (NIC), és továbbítja a virtuális Gépet. Minden hálózati házirendek, amelyekre vonatkozik a virtuális kapcsoló most kiszervezett és hardver a alkalmazni. A hardver házirend alkalmazása lehetővé teszi, hogy a hálózati Adaptert a hálózati forgalom közvetlenül az a virtuális Gépet, a gazdagép és a virtuális kapcsolót, megkerülése, alkalmazza a gazdagép minden házirend fenntartásával.

A gyorsított hálózatkezelés előnyeit csak a virtuális gép, amely engedélyezve van a vonatkoznak. A legjobb eredmények érdekében ideális engedélyezze ezt a funkciót, legalább két virtuális gépet az azonos Azure Virtual Network (VNet). Való kommunikációhoz, virtuális hálózatok közötti vagy helyszíni összekötő, ez a funkció csak minimális befolyással van a teljes késést.

## <a name="benefits"></a>Előnyök
* **Rövidebb válaszidőt / magasabb szintű csomag / másodperc (pps):** A virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok töltött idő a gazdagép a csoportházirend feldolgozása, és növeli a dolgozhatók fel a virtuális gép csomagok száma.
* **Végrehajtását alacsonyabb jitter:** Virtuáliskapcsoló-feldolgozó a szabályzatot, amely a alkalmazni mennyiségét és a munkaterhelés, a CPU, amely a feldolgozási függ. A házirend betartatása hardverre tehermentesítést eltávolítja az adott változékonyságát azáltal, hogy a csomagok közvetlenül a virtuális gép eltávolítása a gazdagépet a virtuális gép kommunikációs és az összes szoftver megszakítások és környezeti kapcsolók a.
* **Csökkent a CPU-kihasználtság:** A virtuális kapcsoló a gazdagép megkerülésével kevesebb hálózati forgalom feldolgozása tekintetében CPU-kihasználtság vezet.

## <a name="limitations-and-constraints"></a>Korlátozások és megkötések

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure katalógusából beépített támogatottak:
* **A Windows Server 2016 Datacenter** 
* **A Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és a 2 vagy több vcpu-k méretű számításra optimalizált példányok esetén támogatott.  A támogatott sorozat a következők: D/DSv2 és az F/Fs

Hyper-Threading technológia támogató példányokon gyorsított hálózatkezelés támogatott 4 vagy több vcpu-k a Virtuálisgép-példányokon. Támogatott sorozat a következők: A Dsv3/D, E/Esv3, Fsv2, Lsv2, Ms és Mms és Ms/Mmsv2.

További információ a Virtuálisgép-példányokon: [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>Régiók
Elérhető minden nyilvános Azure-régiók és az Azure Government felhőben.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>A futó virtuális gép gyorsított hálózatkezelés engedélyezése
Gyorsított hálózatkezelés engedélyezett nélkül támogatott virtuális gép mérete legfeljebb a funkció engedélyezve van a leállítása és felszabadítása.

### <a name="deployment-through-azure-resource-manager"></a>Üzembe helyezés Azure Resource Manager használatával
Gyorsított hálózatkezelésű nem állítható rendszerbe a virtuális gépek (klasszikus).

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Egy Windows virtuális gép létrehozása az Azure gyorsított hálózatkezelés
## <a name="portal-creation"></a>Portál létrehozása
Bár ez a cikk ismerteti a virtuális gép létrehozása az Azure Powershell-lel gyorsított hálózatkezeléssel, is [virtuális gép létrehozása az Azure portal használatával gyorsított hálózatkezeléssel](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Az a portálon, a virtuális gép létrehozásakor a **hozzon létre egy virtuális gépet** panelen válassza ki a **hálózati** fülre.  Ezen a lapon lehetősége van a **gyorsított hálózatkezelés**.  Ha úgy döntött, egy [támogatott operációs rendszert](#supported-operating-systems) és [Virtuálisgép-méret](#supported-vm-instances), ezzel a beállítással automatikusan feltölti az "On".  Ha nem, akkor töltse fel a gyorsított hálózatkezelés "Kikapcsolva" lehetőséget, és a felhasználó okot miért nem lehet engedélyezve van.   
* *Megjegyzés:* Csak a támogatott operációs rendszerek a portálon keresztül engedélyezhető.  Ha egy egyéni rendszerképet használ, és a rendszerkép támogatja a gyorsított hálózatkezelés, hozzon létre a virtuális gép parancssori felület vagy Powershell használatával. 

A virtuális gép létrehozása után ellenőrizheti a gyorsított hálózatkezelés engedélyezve van a következő engedélyezve van a gyorsított hálózatkezelést megerősítő utasításait.

## <a name="powershell-creation"></a>PowerShell létrehozása
## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Telepítés [Azure PowerShell-lel](/powershell/azure/install-az-ps) 1.0.0-s verziójának vagy újabb. A jelenleg telepített verzió megkereséséhez futtassa `Get-Module -ListAvailable Az`. Ha telepíteni vagy frissíteni kell, telepítse a legújabb verziót a modul Az a [PowerShell-galériából](https://www.powershellgallery.com/packages/Az). A PowerShell-munkamenetben jelentkezzen be egy Azure-fiókba az [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

A következő példákban cserélje le a példa a paraméter nevét a saját értékeire. Példa paraméterneveket foglalt *myResourceGroup*, *myNic*, és *myVM*.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup* a a *centralus* helye:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Először hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). A következő példában létrehozunk egy nevű alhálózatot *mySubnet*:

```powershell
$subnet = New-AzVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

A virtuális hálózat létrehozása [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), az a *mySubnet* alhálózat.

```powershell
$vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Először hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

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

Hozzon létre egy hálózati biztonsági csoport [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) és rendelje hozzá a *Allow-RDP-All* biztonsági szabályt. Mellett a *Allow-RDP-All* szabály a hálózati biztonsági csoport több alapértelmezett szabályokat tartalmaz. Egy alapértelmezett szabály az internetről, amely ezért minden bejövő hozzáférés letiltása a *Allow-RDP-All* szabály van rendelve a hálózati biztonsági csoport, úgy, hogy távolról csatlakozhat a virtuális gép létrehozása után.

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

A hálózati biztonsági csoport társítása a *mySubnet* alhálózat [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). A szabály a hálózati biztonsági csoport érvényben, üzembe helyezheti az alhálózaton minden erőforráshoz.

```powershell
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hozzon létre egy hálózati adapter gyorsított hálózatkezelés
Hozzon létre egy nyilvános IP-címet [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). Nyilvános IP-cím nem szükséges, ha nem a virtuális gép elérhető az internetről, de a cikkben ismertetett lépések végrehajtásához, szükség rá.

```powershell
$publicIp = New-AzPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) a gyorsított hálózatkezelés engedélyezett, és rendelje hozzá a nyilvános IP-címet a hálózati adapterhez. A következő példában létrehozunk egy hálózati adapter nevű *myNic* a a *mySubnet* alhálózatának a *myVnet* virtuális hálózat és hozzárendelése a *myPublicIp*  azt a nyilvános IP-cím:

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

A virtuális gép hitelesítő adatainak beállítása a `$cred` változó használatával [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Először határozzon meg a virtuális gép [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Az alábbi példa meghatározza nevű virtuális gép *myVM* egy Virtuálisgép-méretet, amely támogatja a gyorsított hálózatkezelés az (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Az összes Virtuálisgép-méretek és jellemzői, lásd: [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Hozzon létre a Virtuálisgép-konfigurációt, a többi [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) és [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). A következő példában létrehozunk egy Windows Server 2016 virtuális gép:

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

A korábban létrehozott hálózati adapter csatolása [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

```powershell
$vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Végül hozza létre a virtuális gép [New-azvm parancsmag](/powershell/module/az.compute/new-azvm):

```powershell
New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Ellenőrizze az illesztőprogram telepítve van az operációs rendszer

Miután létrehozta a virtuális gép az Azure-ban, csatlakozzon a virtuális Géphez, és győződjön meg arról, hogy az illesztőprogram telepítve van-e a Windows.

1. Egy webböngészőben nyissa meg az Azure [portál](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával.
2. A szöveget tartalmazó mezőbe *erőforrások keresése* írja be az Azure portal tetején *myVm*. Amikor **myVm** megjelenik a keresési eredmények között, kattintson rá. Ha **létrehozása** alatt látható a **Connect** gombra, az Azure rendelkezik még nem fejeződött be a virtuális gép létrehozását. Kattintson a **Connect** , csak azután az Áttekintés bal felső sarkában többé nem látja **létrehozása** alatt a **Connect** gombra.
3. Adja meg a felhasználónevet és jelszót adott meg [hozza létre a virtuális gépet](#create-the-virtual-machine). Ha soha nem csatlakozott az Azure-beli Windows virtuális gépre, tekintse meg [csatlakozhat a virtuális gép](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kattintson a jobb gombbal a Windows Start gombra, és kattintson a **Eszközkezelő**. Bontsa ki a **hálózati adapterek** csomópont. Ellenőrizze, hogy a **Mellanox ConnectX-3 virtuális függvény Ethernet-adaptert** jelenik meg, az alábbi ábrán látható módon:

    ![Eszközkezelő](./media/create-vm-accelerated-networking/device-manager.png)

Gyorsított hálózatkezelés engedélyezve van a virtuális géphez.

## <a name="enable-accelerated-networking-on-existing-vms"></a>A meglévő virtuális gépek gyorsított hálózatkezelés engedélyezéséhez
Létrehozott egy virtuális gép gyorsított hálózatkezelés nélkül, célszerű lehet engedélyezni ezt a funkciót egy meglévő virtuális Gépet.  A virtuális gép támogatnia kell a gyorsított hálózatkezelés az alábbi előfeltételek is fent leírt feltételek:

* A virtuális Gépnek kell lennie egy támogatott méret gyorsított hálózatkezelés
* A virtuális Gépet egy támogatott Azure Gallery-image (és a Linux-Kernelverzió) kell lennie.
* Rendelkezésre állási csoportban lévő összes virtuális gép vagy VMSS kell leállítva és felszabadítva bármely NIC gyorsított hálózatkezelés engedélyezése előtt

### <a name="individual-vms--vms-in-an-availability-set"></a>Az egyes virtuális gépek és virtuális gépek a rendelkezésre állási beállítása
Először állítsa le vagy szabadítsa fel a virtuális gép vagy, ha a rendelkezésre állási csoport, a csoport összes virtuális gépen:

```azurepowershell
Stop-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Fontos, kérjük vegye figyelembe, hogy ha a virtuális gép külön-külön, egy rendelkezésre állási csoportban, akkor csak nélkül hozták létre kell az egyes virtuális gép gyorsított hálózatkezelés engedélyezéséhez állítsa le vagy szabadítsa fel.  Ha a gép egy rendelkezésre állási csoport létrehozásakor, a rendelkezésre állási csoportban található összes virtuális gép kell leállítva és felszabadítva lehet bármelyik hálózati adapter gyorsított hálózatkezelés engedélyezése előtt. 

Leállítását követően engedélyezze a hálózati adapteren a virtuális gép gyorsított Hálózatkezelés:

```azurepowershell
$nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzNetworkInterface
```

Indítsa újra a virtuális Gépet, vagy ha egy rendelkezésre állási készletbe, a csoportban lévő összes virtuális gép, és győződjön meg arról, hogy engedélyezve van-e a gyorsított Hálózatkezelés:

```azurepowershell
Start-AzVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>VMSS
VMSS kissé eltérő, de a következő ugyanabban a munkafolyamatban.  Először állítsa le a virtuális gépek:

```azurepowershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"
```

Miután a virtuális gépek leállnak, frissítse a gyorsított hálózatkezelés tulajdonság alatt a hálózati adapter:

```azurepowershell
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Vegye figyelembe, hogy egy VMSS a virtuális gép olyan frissítések, amelyek a alkalmazni a frissítéseket az három különböző beállításokkal, automatikus, működés közbeni és manuális rendelkezik.  Ebben az útmutatóban a szabályzat automatikusra van beállítva, hogy a VMSS kiesik a módosítások újraindítás után azonnal.  Nastavit nA automatikus úgy, hogy a módosítások azonnal mértékének:

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

Egyszer, indítsa újra, várjon, amíg befejeződik a frissítéseket, de ha befejeződött, a VF jelenik meg a virtuális Gépen belül.  (Ellenőrizze, hogy használja a támogatott operációs rendszer és a virtuális gép mérete)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Gyorsított hálózatkezelésű meglévő virtuális gépek átméretezése

Virtuális gépek gyorsított hálózatkezelésű engedélyezve van, amely támogatja a gyorsított hálózatkezelés virtuális gépek csak átméretezhetők.  

Egy Virtuálisgép-példányhoz, amely nem támogatja a gyorsított hálózatkezelés az átméretezés használatával nem méretezhetők át egy virtuális Gépet a gyorsított hálózatkezelés engedélyezett.  Ehelyett egy ilyen virtuális gépek átméretezése:

* A virtuális gép leállítása vagy felszabadítási vagy egy rendelkezésre állási csoport/VMSS, ha a Leállítás/szabadítsa fel a set/VMSS összes virtuális gépen.
* Gyorsított hálózatkezelés le kell tiltani a hálózati adapteren a virtuális gép vagy egy rendelkezésre állási készlet/VMSS, a set/VMSS minden virtuális gépen belül.
* Gyorsított hálózatkezelés le van tiltva, miután a virtuális gép vagy rendelkezésre állási csoport/VMSS áthelyezhetők az új méret, amely nem támogatja a gyorsított hálózatkezelés és újraindul.
