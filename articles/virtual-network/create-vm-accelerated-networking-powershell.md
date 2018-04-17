---
title: Hozzon létre egy Azure virtuális gép a hálózat elérését gyorsítja fel |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Linux virtuális gép a hálózat elérését gyorsítja fel.
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: f3c8853331121fc1e267f6c569279f7d8df907b5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>A hálózat elérését gyorsítja fel Windows virtuális gép létrehozása

> [!IMPORTANT] 
> Virtuális gépek léteznie kell a hálózat elérését gyorsítja fel engedélyezve van. Ez a funkció nem engedélyezhető a meglévő virtuális gépeken. Az alábbi lépésekkel gyorsított hálózatkezelés engedélyezése:
>   1. A virtuális gép törlése
>   2. Hozza létre újra a virtuális gép gyorsított hálózattal engedélyezve
>

Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy Windows rendszerű virtuális gép (VM) a hálózat elérését gyorsítja fel. Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, a hálózati teljesítmény nagy mértékben javítva. A nagy teljesítményű elérési út nincs hatással a gazdagép a datapath, így csökkentve a késést, a jitter és a CPU felhasználását, a legnagyobb igénybevételt jelentő munkaterheléseit támogatott Virtuálisgép-típusokon való használatra. Az alábbi képen látható rendelkező és anélküli gyorsított hálózatkezelés két virtuális gépek közötti kommunikáció:

![Összehasonlítása](./media/create-vm-accelerated-networking/accelerated-networking.png)

Gyorsított hálózatkezelés nélkül, az összes hálózati forgalom mindkét a virtuális Gépet a virtuális kapcsoló és a gazdagép be kell járnia. A virtuális kapcsoló betartatja az összes, például a hálózati biztonsági csoportok, hozzáférés-vezérlési listák, elkülönítési és egyéb hálózati szempontból virtualizált szolgáltatások a hálózati forgalmat. Virtuális kapcsolók kapcsolatos további tudnivalókért olvassa el a [Hyper-V hálózatvirtualizálás és a virtuális kapcsoló](https://technet.microsoft.com/library/jj945275.aspx) cikk.

Gyorsított hálózattal, a hálózati forgalom érkezik a virtuális hálózati adapter (NIC), és a virtuális gép ezután kerül. A virtuális kapcsolót alkalmazó hálózati házirendben most kiszervezve, és a hardver alkalmazza. A hardver-házirend alkalmazását lehetővé teszi, hogy a hálózati adapter a hálózati forgalom erre a virtuális Gépet, a gazdagép és a virtuális kapcsolót, akkor alkalmazza, a fogadó minden házirend fenntartásával kihagyásával.

A gyorsított hálózat előnyeit, hogy engedélyezve van a virtuális gép csak vonatkoznak. A legjobb eredmény elérése érdekében az ideális legalább két virtuális gépeken, az azonos Azure Virtual Network (VNet) csatlakozik a funkció engedélyezése érdekében. Ha kommunikál a Vnetek vagy az összekötő a helyszíni, ez a funkció csak minimális befolyással van általános késleltetésű.

## <a name="benefits"></a>Előnyök
* **Késés csökkentése / magasabb csomag / másodperc (pps):** a virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok kiosztására szánt idejét a gazdagép a házirend-feldolgozás, a virtuális Gépen belül feldolgozható csomagok száma.
* **Alacsonyabb jitter:** virtuális kapcsoló feldolgozása függ a házirendet, szükség van, és a CPU-t, amely a feldolgozási terhelését. A házirendek betartatását, hogy a hardver kiszervezésével megszüntetéséhez, hogy a variancia közvetlenül a virtuális gép eltávolítása a gazdagép VM kommunikáció és az összes szoftver megszakítások és a környezet kapcsolók kézbesíti a csomagokat.
* **Csökkent a CPU-felhasználás:** kevesebb hálózati forgalom feldolgozása a CPU-használatot a virtuális kapcsoló a gazdagép megkerülésével vezet.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
Microsoft Windows Server 2012 R2 Datacenter és a Windows Server 2016.

## <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és számítási optimalizált példány mérete 4 vagy több Vcpu a támogatott. -Példányokon például D/DSv3 vagy E/ESv3, amely támogatja a Hyper-Threading technológia az elérését gyorsítja fel a hálózat támogatott 8 vagy több Vcpu a Virtuálisgép-példányok. Támogatott adatsorokat: D/DSv2, D/DSv3, E/ESv3, F/Fs/Fsv2 és Ms/Mms.

További információ a Virtuálisgép-példányok: [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Régiók
Elérhető az összes Azure-régiók és Azure Government felhő. 

## <a name="limitations"></a>Korlátozások
A következő korlátozások vonatkoznak az e funkció használata esetén:

* **A hálózati illesztő létrehozása:** gyorsított hálózat csak akkor engedélyezhető, az új hálózati Nem engedélyezhető az egy meglévő hálózati adaptert.
* **Virtuális gép létrehozása:** A hálózati adapter engedélyezve gyorsított hálózattal csak akkor csatolható a virtuális géphez a virtuális gép létrehozásakor. A hálózati adapter nem lehet csatolni, egy meglévő virtuális gépre. Ha a virtuális gép hozzáadása egy meglévő rendelkezésre állási, a rendelkezésre állási csoport virtuális gépeinek kell is rendelkezik az elérését gyorsítja fel engedélyezett hálózati.
* **Telepítés csak Azure Resource Manageren keresztül:** virtuális gépek (klasszikus) nem állítható rendszerbe, a hálózat elérését gyorsítja fel.

Bár ez a cikk ismerteti a virtuális gép létrehozása az Azure PowerShell gyorsított hálózattal, is [virtuális gép létrehozása az Azure portál használatával gyorsított hálózattal](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Létrehozásakor egy virtuális gépet a portálon, a **beállítások**, jelölje be **engedélyezve**a **elérését gyorsítja fel a hálózat**. Gyorsított hálózatkezelés lehetővé teszi nem szerepel a portálon, kivéve, ha a kijelölt egy [támogatott operációs rendszer](#supported-operating-systems) és [Virtuálisgép-méretet](#supported-vm-instances). A virtuális gép létrehozása után végre kell hajtania a utasításait [ellenőrizze az illesztőprogram telepítve van az operációs rendszer](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Telepítés [Azure PowerShell](/powershell/azure/install-azurerm-ps) verzió 5.1.1-es vagy újabb. A jelenleg telepített verzió megkereséséhez futtassa `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni kell, telepítse a legújabb verziót a AzureRM modul a [PowerShell-galériában](https://www.powershellgallery.com/packages/AzureRM). A PowerShell-munkamenetben jelentkezzen be egy Azure-fiók használatával [Add-AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount).

A következő példákban cserélje le a saját értékeit példa paraméterek nevei. Példa paraméter nevekre *myResourceGroup*, *myNic*, és *myVM*.

Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *centralus* helye:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Először hozzon létre egy alhálózat-konfiguráció [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). Az alábbi példakód létrehozza nevű alhálózat *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

A virtuális hálózat létrehozása [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), az a *mySubnet* alhálózat.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Először hozzon létre egy hálózati biztonsági csoport szabály [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
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

Hozzon létre egy hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) , és rendelje hozzá a *engedélyezése – RDP-minden* biztonsági szabályt. Kívül a *engedélyezése – RDP-minden* szabály, a hálózati biztonsági csoport több alapértelmezett szabályokat tartalmazza. Egy alapértelmezett szabály letiltja az összes bejövő hozzáférést az internetről, amely ennek okát a *engedélyezése – RDP-minden* szabály tartozik a hálózati biztonsági csoportot, hogy távolról csatlakozhat a virtuális gép létrehozása után.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

A hálózati biztonsági csoportra, és társítsa a *mySubnet* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). A szabály a hálózati biztonsági csoport minden alhálózaton üzembe helyezett erőforrás a érvényben.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Hozzon létre egy adott hálózati csatoló gyorsított hálózatkezelés
Hozzon létre egy nyilvános IP-címet a [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress) paranccsal. A nyilvános IP-cím nem szükséges, ha nem tervezi a virtuális gép az internetről való eléréséhez, de ez a cikk a végrehajtásához, szükség rá.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Hozzon létre egy hálózati illesztő – [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) az elérését gyorsítja fel a hálózat engedélyezve van, és a nyilvános IP-címet rendel a hálózati illesztőt. Az alábbi példa létrehoz egy adott hálózati csatoló nevű *myNic* a a *mySubnet* alhálózata a *myVnet* virtuális hálózat és rendel a *myPublicIp*  nyilvános IP-cím hozzá:

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

A virtuális gép hitelesítő adatok beállítása a `$cred` változó segítségével [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Először adja meg a virtuális Gépet a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Az alábbi példa meghatározza egy nevű virtuális gép *myVM* a Virtuálisgép-méretet, amely támogatja a hálózat elérését gyorsítja fel (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Az összes Virtuálisgép-méretek és jellemzők listájáért lásd: [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Hozzon létre a Virtuálisgép-konfiguráció, a többi [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) és [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). Az alábbi példa létrehoz egy Windows Server 2016 virtuális Gépet:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Csatlakoztassa a hálózati adapter, amely a korábban létrehozott [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Végezetül hozza létre a virtuális Gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Ellenőrizze az illesztőprogram telepítve van az operációs rendszer

Miután létrehozta a virtuális Gépet az Azure-ban, csatlakoztassa a virtuális Gépet, és győződjön meg arról, hogy az illesztőprogram telepítve van-e a Windows rendszerben. 

1. Egy webböngészőben nyissa meg az Azure [portal](https://portal.azure.com) és jelentkezzen be az Azure-fiókjával.
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *myVm*. Ha **myVm** jelenik meg a keresési eredmények között kattintson rá. Ha **létrehozása** alatt látható a **Connect** gomb, Azure még nem fejeződött be a virtuális gép létrehozása. Kattintson a **Connect** a áttekintése után csak bal felső sarkában többé nem láthatja **létrehozása** alatt a **Connect** gombra.
3. Adja meg a felhasználónevet és jelszót adott meg [a virtuális gép létrehozása](#create-the-virtual-machine). Ha soha nem csatlakozott egy Windows Azure-ban, tekintse meg [kapcsolódás a virtuális gép](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Kattintson a jobb gombbal a Start gombra, és kattintson a **Eszközkezelő**. Bontsa ki a **hálózati adapterek** csomópont. Ellenőrizze, hogy a **Mellanox ConnectX-3 virtuális függvény Ethernet-Adapter** az alábbi ábrán látható módon jelenik meg:
   
    ![Eszközkezelő](./media/create-vm-accelerated-networking/device-manager.png)

Gyorsított hálózatkezelés engedélyezve van a virtuális gép számára.
