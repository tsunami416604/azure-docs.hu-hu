---
title: Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el az Azure virtuális gépek |} Microsoft Docs
description: Útmutató a hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépekről.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: 6193dcc6ba2e78c55ed6c6f769aea50fcfb3ac40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603459"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adja hozzá a hálózati kapcsolatokat, vagy távolítsa el a hálózati adapterek a virtuális gépek

Útmutató hozzáadása a meglévő hálózati illesztő, amikor létrehoz egy Azure virtuális gép (VM), vagy adja hozzá, vagy távolítsa el a hálózati adapterek egy meglévő virtuális gép leállított (felszabadított) állapotában. Egy adott hálózati csatoló lehetővé teszi, hogy az internet, Azure és a helyszíni erőforrások kommunikálni Azure virtuális géphez. A virtuális gépek is egy vagy több hálózati illesztőre van szükség. 

Ha kell hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő IP-címek, lásd: [hálózati illesztő IP-címeinek kezelése](virtual-network-network-interface-addresses.md). Ha szeretne létrehozni, módosításához vagy törölni a hálózati adapterek, lásd: [kezelése a hálózati adapterek](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.2.0 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.26 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális géphez

Amikor létrehoz egy virtuális gépet a portálon keresztül, a portál létrehoz egy adott hálózati csatoló alapértelmezett beállításokkal, és csatolja a virtuális gép meg. Nem lehet a meglévő hálózati adapterek hozzáadása egy új virtuális Gépet, és több hálózati adapterrel rendelkező virtuális gép létrehozása az Azure portál használatával. Úgy teheti meg, mindkét a parancssori felületen vagy a PowerShell használatával, de győződjön meg arról, hogy megismerje a a [megkötések](#constraints). Ha több hálózati adapterrel egy virtuális Gépet hoz létre, az operációs rendszer megfelelően használhatja őket a virtuális gép létrehozása után is konfigurálnia kell. Megtudhatja, hogyan konfigurálhatja [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) a több hálózati adapterrel.

### <a name="commands"></a>Parancsok

A virtuális gép létrehozása előtt hozzon létre egy adott hálózati csatoló a lépések segítségével [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface).

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Egy adott hálózati csatoló hozzáadása egy meglévő virtuális Gépen

1. Jelentkezzen be az Azure portálra.
2. A portál felső keresési mezőbe, írja be a virtuális Gépet, amelyhez a hálózati adapter hozzáadása, vagy keresse meg a virtuális gép kiválasztásával kívánt nevét **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, válassza ki azt. A virtuális gép támogatnia kell a hozzáadni kívánt hálózati interfészek száma. Ha szeretné tudni, hogy hány hálózati adapterek minden egyes Virtuálisgép-méretet támogatja, lásd: [Azure Linux virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [méretek a Windows virtuális gépek Azure-ban](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Válassza ki **áttekintése**a **beállítások**. Válassza ki **leállítása**, és várjon, amíg a **állapot** változik a virtuális gép **leállítva (felszabadítva)**. 
4. Válassza ki **hálózati**a **beállítások**.
5. Válassza ki **Attach hálózati illesztő**. Válassza a listából, amely jelenleg nem csatlakozik másik virtuális gép hálózati adapterek csatolni szeretné. 

    >[!NOTE]
    A kiválasztott hálózati adapter nem rendelkezik az elérését gyorsítja fel hálózati engedélyezve, nem rendelkezik hozzárendelt IPv6-cím és már léteznie kell, amely tartalmazza a hálózati adapter jelenleg a virtuális Géphez csatlakozik, mint az azonos virtuális hálózatban. 

    Ha egy meglévő hálózati illesztő nem rendelkezik, akkor először létre kell hoznia egyet. Ehhez az szükséges, válassza ki a **létrehozás hálózati illesztő**. Egy adott hálózati csatoló létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface). További korlátozások további hálózati adapterek virtuális gépek hozzáadásakor, [megkötések](#constraints).

6. Kattintson az **OK** gombra.
7. Válassza ki **áttekintése**a **beállítások**, majd **Start** a virtuális gép elindításához.
8. Konfigurálja a virtuális gép operációs rendszer több hálózati adapterrel megfelelően. Megtudhatja, hogyan konfigurálhatja [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) a több hálózati adapterrel.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm hálózati adapter hozzáadása](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Adja hozzá AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>A virtuális gépek nézet hálózati illesztők

Megtekintheti a jelenleg a virtuális Géphez csatlakozik, mindegyik hálózati interfész konfigurációjával kapcsolatos további hálózati adapterek, és mindegyik hálózati interfész hozzárendelt IP-címeket. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózat közreműködő szerepkört az előfizetés. Szerepkörök hozzárendeléséről fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A mezőbe a szöveget tartalmazó **keresési erőforrások** az Azure portál felső részén írja be a **virtuális gépek**. Ha **virtuális gépek** megjelenik a keresési eredmények között, jelölje be.
3. Válassza ki a virtuális Gépet, amelynek meg szeretné tekinteni a hálózati adapter nevét.
4. Az a **beállítások** a virtuális gép számára választott ki, válassza ki a szakasz **hálózati**. Hálózati illesztő beállításai és azok módosítására vonatkozó további tudnivalókért lásd: [kezelése a hálózati adapterek](virtual-network-network-interface.md). Című témakörben olvashat hozzáadása, módosítása, vagy távolítsa el a hálózati illesztő IP-címtartományból, [hálózati illesztő IP-címeinek kezelése](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Távolítsa el a hálózati adaptert egy virtuális gépből

1. Jelentkezzen be az Azure portálra.
2. A keresési mezőbe, a portál felső, keresse meg az eltávolítani kívánt virtuális gép nevét (leválasztani) a hálózati illesztőt a, vagy a tallózással keresse meg a virtuális gép kiválasztásával **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, válassza ki azt.
3. Válassza ki **áttekintése**a **beállítások**, majd **leállítása**. Várjon, amíg a **állapot** változik a virtuális gép **leállítva (felszabadítva)**. 
4. Válassza ki **hálózati**a **beállítások**.
5. Válassza ki **leválasztási hálózati illesztő**. Jelenleg a virtuális géphez csatlakoztatott hálózati adapterek listájának megtekintéséhez jelölje ki a hálózati illesztő leválasztása szeretné. 

    >[!NOTE]
    Ha csak egy hálózati illesztővel szerepel, akkor nem választható le, mert a virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható.
6. Kattintson az **OK** gombra.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[Távolítsa el a virtuális gép hálózati az](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (hivatkozás) vagy [részletes lépései](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépései](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- A virtuális gépek rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható.
- A virtuális gépek csak van számos hálózati kapcsolat, a virtuális gép mérete által támogatott kapcsolódik. Hány hálózati adapterekkel kapcsolatos minden egyes Virtuálisgép-méretet támogatja, kapcsolatban további [Azure Linux virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [méretek a Windows virtuális gépek Azure-ban](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Összes méretének legalább két hálózati adapterrel támogatja.
- A hálózati adapterek ad hozzá egy virtuális gép jelenleg egy másik virtuális géphez nem lehet csatolni. Hálózati illesztők létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy adott hálózati csatoló](virtual-network-network-interface.md#create-a-network-interface).
- A múltban hálózati illesztők csak nem kell hozzá, amely támogatja több hálózati adapterrel, és létre lettek hozva legalább két hálózati adapterrel rendelkező virtuális gépeket. Meg nem lehet hozzáadni a hálózati adaptert egy virtuális Gépet egy hálózati csatoló hoztak létre akkor is, ha a Virtuálisgép-méretet támogatja több hálózati adapterrel. Ezzel szemben csak eltávolítható hálózati adapterek legalább három hálózati felületek, a virtuális gép alapján, mert legalább két hálózati illesztők mindig kell hozniuk legalább két hálózati adapterrel létrehozott virtuális gépeket. Ezek a megkötések egyike sem már érvényesek. Most létrehozhat egy virtuális gép tetszőleges számú, a hálózati adapterek (pótolni a virtuális gép mérete által támogatott).
- Alapértelmezés szerint az első hálózati adapter egy virtuális Géphez csatlakozik, típusúként van definiálva a *elsődleges* hálózati illesztőt. A virtuális gép más hálózati felületek *másodlagos* hálózati illesztőt.
- Bár szabályozhatja, hogy melyik hálózati csatolón kimenő forgalmat, alapértelmezés szerint küldi, minden kimenő forgalom a virtuális gép által kiküldött a hozzárendelt elsődleges IP-konfigurációja az elsődleges hálózati adapter IP-címet.
- A múltban belül az azonos rendelkezésre állási csoport összes virtuális gép volt egy egyetlen vagy több hálózati illesztőre van szükség. Több hálózati adapterrel rendelkező virtuális gépek most létezhet az azonos rendelkezésre állási készletbe, akár a virtuális gép mérete által támogatott számra. Csak hozzáadhat egy virtuális gép rendelkezésre állási készlet létrehozásakor. Rendelkezésre állási csoportokra vonatkozó további információkért lásd: [az Azure virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Miközben a virtuális hálózaton belül különböző alhálózatokon hálózati adapter van az azonos virtuális gép is kapcsolódnak, a hálózati adapterek összes kapcsolódnia kell az azonos virtuális hálózatban.
- Egy Azure Load Balancer háttér címkészletet bármely IP-konfiguráció bármely elsődleges vagy másodlagos hálózati adapter IP-címeket adhat hozzá. Korábban csak az elsődleges IP-címet az elsődleges hálózati illesztő sikerült felvenni egy háttér címkészletet. IP-címek és konfigurációkkal kapcsolatos további tudnivalókért lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md).
- A virtuális gép törlése nem érinti a hálózati adapterek, amelyek kapcsolódnak. Ha töröl egy virtuális Gépet, a hálózati adapterek le vannak választva a virtuális gépről. A hálózati adapterek különböző virtuális gépek hozzáadása, vagy törölje őket.
- Ha egy hálózati illesztőnek egy titkos IPv6-címet kap, hozzá kell adnia (csatolása) úgy, hogy egy virtuális Gépet a virtuális gép létrehozásakor. Hálózati illesztő – hozzárendelt IPv6-cím nem adható hozzá egy virtuális géphez, a virtuális gép létrehozása után. Ha hálózati illesztő – hozzárendelt saját IPv6-címet ad hozzá, a virtuális gépek létrehozásakor, csak adhat hozzá, hogy a hálózati illesztő a virtuális gép, függetlenül attól, hogy hány hálózati illesztők támogatja a Virtuálisgép-méretet. Lásd: [hálózati illesztő IP-címeinek kezelése](virtual-network-network-interface-addresses.md) további információt a hálózati adapterek IP-címek hozzárendelése.
- IPv6, mint a hálózati illesztő gyorsított hálózattal engedélyezve van a virtuális gép létrehozása után nem lehet csatolni. További gyorsított hálózatkezelés előnyeit, is elvégzése után a virtuális gép operációs rendszer lépéseit. Ha használ, ismerje meg a további gyorsított alkalmazás hálózatkezelési funkcióiról, és egyéb korlátozások [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépek.

## <a name="next-steps"></a>További lépések
Hozzon létre egy virtuális gép több hálózati adapterrel vagy IP-címek, olvassa el a következő cikkeket:

### <a name="commands"></a>Parancsok

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[Parancssori felület](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy hálózati adapter virtuális több IPv4-címekkel|[Parancssori felület](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy hálózati adapter virtuális magánhálózati IPv6-cím (mögött egy Azure Load Balancer)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


