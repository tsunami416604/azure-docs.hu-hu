---
title: "Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el az Azure virtuális gépek |} Microsoft Docs"
description: "Útmutató a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépekről."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: 225dd64c46bf9af3e058bbe3cfacf8f8a693b565
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el a hálózati adapterek a virtuális gépek

Útmutató: virtuális gép létrehozásakor adja hozzá a meglévő hálózati illesztő, vagy adja hozzá, vagy távolítsa el a hálózati adapterek egy meglévő virtuális gép leállított (felszabadított) állapotában. Egy adott hálózati csatoló lehetővé teszi, hogy egy Azure virtuális gép (VM) Internet, Azure és a helyszíni erőforrások folytatott kommunikációhoz. A virtuális gépek is egy vagy több hálózati illesztőre van szükség. 

Ha szeretne hozzáadni, módosítása vagy eltávolítása IP-címek a hálózati illesztő, olvassa el a [hálózati illesztő IP-címeinek kezelése](virtual-network-network-interface-addresses.md) cikk. Ha meg kell létrehozni, módosítási vagy törlési hálózati adapterek, olvassa el a [kezelése a hálózati adapterek](virtual-network-network-interface.md) cikk.

## <a name="before"></a>Előkészületek

Esetlegesen szakasz ebben a cikkben szereplő lépésekkel befejezése előtt hajtsa végre a következő feladatokat:

- Jelentkezzen be a Azure [portal](https://portal.azure.com), az Azure parancssori felület (CLI), vagy az Azure PowerShell használata az Azure-fiók. Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha a feladat-ebben a cikkben a PowerShell-parancsokkal [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a legfrissebb telepítve az Azure PowerShell-parancsmagjaival. Ha segítséget szeretne kérni a PowerShell-parancsaihoz, valamint példákkal, írja be a `get-help <command> -full`. Ahelyett, hogy az Azure PowerShell telepítése az Azure-felhő rendszerhéj is használhatja. Az Azure-felhő rendszerhéj egy szabad PowerShell futtatható közvetlenül az Azure-portálon belül. Azure PowerShell előtelepített és konfigurált a fiókhoz rendelkezik. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj **> _** gomb tetején a [portal](https://portal.azure.com) PowerShell bal felső sarkában, válassza ki, amikor megjelenik a rendszerhéj ablak.
- Ha ebben a cikkben a feladatokat az Azure parancssori felület (CLI) parancsokkal [telepítése és konfigurálása az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Ellenőrizze, hogy a telepített Azure CLI legújabb verziója. Segítség kérése parancssori felület parancsait, írja be a következőt `az <command> --help`. Ahelyett, hogy a parancssori felület és a szükséges előfeltételek telepítése, az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj **> _** gomb tetején a [portal](https://portal.azure.com) Bash bal felső sarkában, válassza ki, amikor megjelenik a rendszerhéj ablak.

## <a name="vm-create"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális géphez

Amikor létrehoz egy virtuális Gépet a portálon keresztül, a portál létrehoz egy adott hálózati csatoló alapértelmezett beállításokkal, és csatolja a virtuális gép meg. Nem a meglévő hálózati adapterek hozzáadása egy új virtuális Gépet, vagy hozzon létre egy virtuális gép több hálózati adapterrel, az Azure portál használatával. Mindkét a parancssori felületen vagy a PowerShell használatával teheti meg. Mielőtt PowerShell vagy a parancssori felület segítségével azonban a meglévő hálózati illesztő hozzon létre egy virtuális Gépet, feltérképezése a [megkötések](#constraints). Ha több hálózati adapterrel egy virtuális gépet hoz létre, az operációs rendszer megfelelően használni azokat a virtuális gép létrehozása után is konfigurálnia kell. További információkért lásd: Configure [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) a több hálózati adapterrel.

**Parancsok** előtt a virtuális gép létrehozása, hozzon létre egy adott hálózati csatoló a lépések [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface).

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az virtuális gép létrehozása](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Egy adott hálózati csatoló hozzáadása egy meglévő virtuális Gépen

1. Jelentkezzen be az Azure Portalra.
2. A keresési mezőbe, a portál felső, keresse meg a virtuális gép hozzá szeretné adni a hálózati kapcsolat neve, vagy keresse meg a virtuális gép kattintva **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, kattintson rá. A virtuális gép hozzá szeretné adni a hálózati adaptert támogatnia kell hozzáadni kívánt hálózati adapterek száma. Megtudhatja, hogy hány hálózati adapterek minden virtuális gép mérete támogatja, olvassa el a [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretének cikkeket.  
3. Kattintson a **áttekintése**a **beállítások**. Kattintson a **leállítása**, és várjon, amíg a **állapot** változik a virtuális gép *leállítva (felszabadítva)*. 
4. Kattintson a **hálózati**a **beállítások**.
5. Kattintson a **Attach hálózati illesztő**. A meglévő, amely jelenleg nem csatlakozik másik virtuális gép hálózati adapterek listájának megtekintéséhez kattintson a hálózati illesztő csatolni szeretné. A kiválasztott hálózati adapter nem engedélyezett hálózati gyorsítást, nem rendelkezik hozzárendelt IPv6-cím és léteznie kell az azonos virtuális hálózatban, a virtuális hálózatot a hálózati adapter jelenleg a virtuális Géphez csatlakozik, az. Ha egy meglévő hálózati illesztő nem rendelkezik, akkor először létre kell hoznia egyet. A hálózati kapcsolat létrehozásához kattintson a **létrehozás hálózati illesztő**. Egy adott hálózati csatoló létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface). További korlátozások további hálózati adapterek virtuális gépek hozzáadásakor, [megkötések](#constraints).
6. Kattintson az **OK** gombra.
7. Kattintson a **áttekintése**a **beállítások**. Kattintson a **Start** a virtuális gép elindításához.
8. A virtuális gép operációs rendszer megfelelően használni az több hálózati adapter konfigurálásához. További információkért lásd: Configure [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) a több hálózati adapterrel.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm hálózati adapter hozzáadása](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Adja hozzá AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a>A virtuális gépek nézet hálózati illesztők

Megtekintheti a jelenleg a virtuális Géphez csatlakozik, mindegyik hálózati interfész konfigurációjával kapcsolatos további hálózati adapterek, és mindegyik hálózati interfész hozzárendelt IP-címeket. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózat közreműködő szerepkört az előfizetés. Szerepkörök hozzárendelése a fiókokhoz kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A mezőbe a szöveget tartalmazó *keresési erőforrások* az Azure portál felső részén írja be a *virtuális gépek*. Ha **virtuális gépek** jelenik meg a keresési eredmények között kattintson rá.
3. Kattintson a virtuális hálózati adaptert használjon a megtekinteni kívánt nevét.
4. Az a **beállítások** szakasz a kiválasztott virtuális gép **hálózati**. Hálózati illesztő beállításai és azok módosítására vonatkozó további tudnivalókért lásd: [kezelése a hálózati adapterek](virtual-network-network-interface.md). Hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő rendelt IP-címek című témakörben olvashat [kezelése IP-címek](virtual-network-network-interface-addresses.md).

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm megjelenítése](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a>Távolítsa el a hálózati adaptert egy virtuális gépből

1. Jelentkezzen be az Azure Portalra.
2. A keresési mezőbe, a portál felső, keresse meg az eltávolítani kívánt virtuális gép nevét (leválasztani) a hálózati illesztőt a, vagy a tallózással keresse meg a virtuális gép kattintva **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, kattintson rá.
3. Kattintson a **áttekintése**a **beállítások**. Kattintson a **leállítása**, és várjon, amíg a **állapot** változik a virtuális gép *leállítva (felszabadítva)*. 
4. Kattintson a **hálózati**a **beállítások**.
5. Kattintson a **leválasztási hálózati illesztő**. Jelenleg a virtuális géphez csatlakoztatott hálózati adapterek listájának megtekintéséhez kattintson a leválasztani kívánt hálózati adapter. Ha csak egy hálózati illesztővel szerepel, akkor nem választható le, mert a virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható.
6. Kattintson az **OK** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[Távolítsa el a virtuális gép hálózati az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Következő lépések
Hozzon létre egy virtuális gép több hálózati adapterrel vagy IP-címek, olvassa el a következő cikkeket:

**Parancsok**

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy hálózati adapter virtuális több IPv4-címekkel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy hálózati adapter virtuális magánhálózati IPv6-cím (mögött egy Azure Load Balancer)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Korlátozások

- A virtuális gépek rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható.
- A virtuális gépek csak van számos hálózati kapcsolat, a virtuális gép mérete által támogatott kapcsolódik. Hány hálózati adapterekkel kapcsolatos minden egyes Virtuálisgép-méretet támogatja, kapcsolatban további [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Virtuálisgép-méretek. Összes méretének legalább két hálózati adapterrel támogatja.
- A hálózati adapterek ad hozzá egy virtuális gép jelenleg egy másik virtuális géphez nem lehet csatolni. Hálózati illesztők létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface).
- A múltban hálózati illesztők csak nem kell hozzá, amely támogatja több hálózati adapterrel, és létre lettek hozva legalább két hálózati adapterrel rendelkező virtuális gépeket. Meg nem lehet hozzáadni a hálózati adaptert egy virtuális Gépet egy hálózati csatoló hoztak létre akkor is, ha a Virtuálisgép-méretet támogatja több hálózati adapterrel. Ezzel szemben csak eltávolítható hálózati adapterek legalább három hálózati felületek, a virtuális gép alapján, mert legalább két hálózati illesztők mindig kell hozniuk legalább két hálózati adapterrel létrehozott virtuális gépeket. Ezek a megkötések egyike sem már érvényesek. Most létrehozhat egy virtuális gép tetszőleges számú, a hálózati adapterek (pótolni a virtuális gép mérete által támogatott).
- Alapértelmezés szerint az első hálózati adapter egy virtuális Géphez csatlakozik, típusúként van definiálva a *elsődleges* hálózati illesztőt. A virtuális gép más hálózati felületek *másodlagos* hálózati illesztőt.
- Bár szabályozhatja, hogy melyik hálózati csatolón kimenő forgalmat, alapértelmezés szerint küldi, minden kimenő forgalom a virtuális gép által kiküldött a hozzárendelt elsődleges IP-konfigurációja az elsődleges hálózati adapter IP-címet.
- A múltban belül az azonos rendelkezésre állási csoport összes virtuális gép volt egy egyetlen vagy több hálózati illesztőre van szükség. Több hálózati adapterrel rendelkező virtuális gépek most létezhet az azonos rendelkezésre állási készletbe, akár a virtuális gép mérete által támogatott számra. A rendelkezésre állási készlet létrehozásakor azt, ha csak egy virtuális gép adhat hozzá. Rendelkezésre állási csoportokra vonatkozó további információkért olvassa el a [az Azure virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) cikk.
- Amíg az azonos virtuális gép hálózati adapterek különböző alhálózatokon történik egy Vneten belül lehet csatlakoztatni, a hálózati adapterek összes kapcsolódnia kell ugyanazt a virtuális hálózatot.
- Egy Azure Load Balancer háttér címkészletet bármely IP-konfiguráció bármely elsődleges vagy másodlagos hálózati adapter IP-címeket adhat hozzá. Korábban csak az elsődleges IP-címet az elsődleges hálózati illesztő sikerült felvenni egy háttér címkészletet. IP-címek és konfigurációkkal kapcsolatos további tudnivalókért olvassa el a [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md) cikk.
- A virtuális gép törlése nem érinti a hálózati adapterek, amelyek kapcsolódnak. A virtuális gép törlésekor a hálózati adapterek le vannak választva a virtuális gépről. A hálózati adapterek különböző virtuális gépek hozzáadása, vagy törölje őket.
- Ha egy hálózati illesztőnek egy titkos IPv6-címet kap, hozzá kell adnia (csatolása) úgy, hogy egy virtuális Gépet a virtuális gép létrehozásakor. Hálózati illesztő – hozzárendelt IPv6-cím nem adható hozzá egy virtuális géphez, a virtuális gép létrehozása után. Ha hálózati illesztő – hozzárendelt saját IPv6-címet ad hozzá, a virtuális gépek létrehozásakor, csak adhat hozzá, hogy a hálózati illesztő a virtuális gép, függetlenül attól, hogy hány hálózati illesztők támogatja a Virtuálisgép-méretet. Lásd: [hálózati illesztő IP-címek](virtual-network-network-interface-addresses.md) további információt az IP-címek hozzárendelése a hálózati adapterek.
- Hasonló IPv6-alapú, egy adott hálózati csatoló nem lehet csatolni a virtuális gép létrehozása után a virtuális géphez engedélyezett gyorsított hálózattal. További gyorsított hálózatkezelés előnyeit, is elvégzése után a virtuális gép operációs rendszer lépéseit. További információ a gyorsított hálózati és egyéb korlátozások használat során kapcsolatban lásd: a hálózat gyorsított [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépek.
