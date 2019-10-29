---
title: Hálózati adapterek hozzáadása vagy eltávolítása az Azure Virtual Machines szolgáltatásból | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá hálózati adaptereket a virtuális gépek hálózati adapterei számára, illetve távolíthatja el azokat.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: 24f4b7435c2736527d033aa5ca7a65ad35a3a705
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022185"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hálózati adapterek hozzáadása vagy eltávolítása a virtuális gépek hálózati adapterei közül

Megtudhatja, hogyan adhat hozzá meglévő hálózati adaptert egy Azure-beli virtuális gép (VM) létrehozásakor, illetve hogyan adhat hozzá vagy távolíthat el hálózati adaptereket egy meglévő virtuális gépről a leállított (kiosztott) állapotban. A hálózati adapterek lehetővé teszik, hogy egy Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. A virtuális gépek egy vagy több hálózati adapterrel rendelkezhetnek. 

Ha IP-címeket kell hozzáadnia, módosítania vagy eltávolítania egy hálózati adapterhez, tekintse meg a [hálózati adapter IP-címeinek kezelése](virtual-network-network-interface-addresses.md)című témakört. Ha hálózati adaptereket kell létrehoznia, módosítania vagy törölnie, tekintse meg a [hálózati adapterek kezelése](virtual-network-network-interface.md)című témakört.

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.26 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor a `az login` futtatásával is létre kell hoznia egy, az Azure-hoz való kapcsolódást.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális géphez

Ha a portálon keresztül hoz létre virtuális gépet, a portál létrehoz egy alapértelmezett beállításokkal rendelkező hálózati adaptert, és csatolja azt a virtuális GÉPHEZ. Nem adhat hozzá meglévő hálózati adaptereket új virtuális géphez, és nem hozhat létre több hálózati adapterrel rendelkező virtuális gépet a Azure Portal használatával. Mindkettőt a CLI vagy a PowerShell használatával is elvégezheti, de mindenképpen ismerkedjen meg a [korlátozásokkal](#constraints). Ha több hálózati adapterrel rendelkező virtuális gépet hoz létre, akkor a virtuális gép létrehozása után is megfelelően kell konfigurálnia az operációs rendszert. Megtudhatja, hogyan konfigurálhat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows rendszert](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok

A virtuális gép létrehozása előtt hozzon létre egy hálózati adaptert a [hálózati adapter létrehozása](virtual-network-network-interface.md#create-a-network-interface)című témakör lépéseit követve.

|Eszköz|Parancs|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Hálózati adapter hozzáadása meglévő virtuális géphez

1. Jelentkezzen be az Azure portálra.
2. A portál felső részén található keresőmezőbe írja be annak a virtuális gépnek a nevét, amelyhez hozzá szeretné adni a hálózati adaptert, vagy tallózással keresse meg a virtuális GÉPET a **minden szolgáltatás**, majd a **virtuális gépek**lehetőség kiválasztásával. Miután megtalálta a virtuális gépet, válassza ki. A virtuális gépnek támogatnia kell a felvenni kívánt hálózati adapterek számát. Annak megállapításához, hogy az egyes virtuálisgép-méretek hány hálózati adaptert támogatnak, lásd: [a Linux rendszerű virtuális gépek méretei az Azure-ban](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , illetve a [Windowsos virtuális gépek méretei az Azure-ban](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  
3. Válassza az **Áttekintés**lehetőséget a **Beállítások**területen. Válassza a **Leállítás**lehetőséget, majd várjon, amíg a virtuális gép **állapota** leállítva értékre nem változik (fel van **foglalva)** .
4. Válassza a **hálózatkezelés**lehetőséget a **Beállítások**területen.
5. Válassza a **hálózati adapter csatolása**lehetőséget. Azon hálózati adapterek listájából, amelyek jelenleg nem csatlakoznak egy másik virtuális géphez, válassza ki azt, amelyet csatlakoztatni szeretne.

   >[!NOTE]
   >A kiválasztott hálózati adapter nem rendelkezhet a gyorsított hálózatkezelés engedélyezésével, nem rendelkezhet hozzá IPv6-címekkel, és ugyanabban a virtuális hálózatban kell lennie, mint amely a virtuális géphez jelenleg csatlakozó hálózati adaptert tartalmazza.

   Ha nem rendelkezik meglévő hálózati adapterrel, először létre kell hoznia egyet. Ehhez válassza a **hálózati adapter létrehozása**lehetőséget. A hálózati adapterek létrehozásával kapcsolatos további tudnivalókért tekintse meg [a hálózati adapterek](virtual-network-network-interface.md#create-a-network-interface)létrehozásával foglalkozó témakört. Ha többet szeretne megtudni a hálózati adapterek virtuális gépekhez adásával kapcsolatos további korlátozásokról, lásd: [megkötések](#constraints).

6. Kattintson az **OK** gombra.
7. Válassza az **Áttekintés**lehetőséget, a **Beállítások**területen, majd **indítsa** el a virtuális gépet.
8. Konfigurálja úgy a virtuális gép operációs rendszerét, hogy megfelelően több hálózati adaptert használjon. Megtudhatja, hogyan konfigurálhat [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows rendszert](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapterhez.

### <a name="commands"></a>Parancsok
|Eszköz|Parancs|
|---|---|
|CLI|[az VM NIC Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) vagy [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Virtuális gép hálózati adapterének megtekintése

Megtekintheti a virtuális géphez csatlakozó hálózati adaptereket, és megismerheti az egyes hálózati adapterek konfigurációját, valamint az egyes hálózati adapterekhez hozzárendelt IP-címeket. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózati közreműködő szerepkörhöz az előfizetéséhez. Ha többet szeretne megtudni a szerepkörök a fiókokhoz való hozzárendeléséről, tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)című témakört.
2. A Azure Portal tetején található szöveges **keresési erőforrásokat** tartalmazó mezőbe írja be a következőt: **Virtual Machines**. Ha a **virtuális gépek** megjelennek a keresési eredmények között, válassza ki.
3. Válassza ki annak a virtuális gépnek a nevét, amelyre vonatkozóan meg szeretné tekinteni a hálózati adaptereket.
4. A kiválasztott virtuális gép **Beállítások** szakaszában válassza a **hálózatkezelés**lehetőséget. A hálózati adapterek beállításairól és azok módosításáról a [hálózati adapterek kezelése](virtual-network-network-interface.md)című témakörben olvashat bővebben. A hálózati adapterhez hozzárendelt IP-címek hozzáadásával, módosításával vagy eltávolításával kapcsolatos további információkért lásd: a [hálózati adapter IP-címeinek kezelése](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Hálózati adapter eltávolítása egy virtuális gépről

1. Jelentkezzen be az Azure portálra.
2. A portál felső részén található keresőmezőbe keresse meg annak a virtuális gépnek a nevét, amelyet el szeretne távolítani (válassza le) a hálózati adaptert, vagy keresse meg a virtuális GÉPET a **minden szolgáltatás**, majd a **virtuális gépek**lehetőség kiválasztásával. Miután megtalálta a virtuális gépet, válassza ki.
3. Válassza az **Áttekintés**, a **Beállítások**, majd a **Leállítás**lehetőséget. Várjon, amíg a virtuális gép **állapota** leállítva értékre nem változik (fel van **foglalva)** .
4. Válassza a **hálózatkezelés**lehetőséget a **Beállítások**területen.
5. Válassza a **hálózati adapter leválasztása**lehetőséget. A virtuális géphez jelenleg csatlakozó hálózati adapterek listájából válassza ki a leválasztani kívánt hálózati adaptert.

   >[!NOTE]
   >Ha csak egy hálózati adapter van felsorolva, akkor nem lehet leválasztani, mert a virtuális gépnek mindig legalább egy hálózati adapterrel kell rendelkeznie.
6. Kattintson az **OK** gombra.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|CLI|[az VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json) (Reference) vagy [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- A virtuális gépekhez legalább egy hálózati adapternek hozzá kell tartoznia.
- A virtuális gépek csak annyi hálózati csatolóval rendelkezhetnek, amennyit a virtuálisgép-méret is támogat. Ha többet szeretne megtudni arról, hogy az egyes virtuálisgép-méretek hány hálózati adaptert támogatnak, tekintse meg a Linux rendszerű [virtuális gépek méretei az Azure-ban](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy az Azure-beli [Windowsos virtuális gépek](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)mérete Minden méret legalább két hálózati adaptert támogat.
- A virtuális géphez hozzáadott hálózati adapterek jelenleg nem csatlakoztathatók másik virtuális géphez. A hálózati adapterek létrehozásával kapcsolatos további tudnivalókért tekintse meg [a hálózati adapterek](virtual-network-network-interface.md#create-a-network-interface)létrehozásával foglalkozó témakört.
- A múltban a hálózati adapterek csak olyan virtuális gépekhez vehetők fel, amelyek több hálózati adaptert támogattak, és legalább két hálózati adapterrel lettek létrehozva. Nem lehet hálózati adaptert hozzáadni egy olyan virtuális géphez, amely egyetlen hálózati adapterrel lett létrehozva, még akkor is, ha a virtuális gép mérete több hálózati csatolót is támogat. Ezzel szemben a hálózati adapterek csak legalább három hálózati adapterrel távolíthatók el, mert legalább két hálózati adapterrel létrehozott virtuális gépeknek mindig legalább két hálózati adapterrel kell rendelkezniük. A megkötések egyike sem vonatkozik többé. Most már létrehozhat egy tetszőleges számú hálózati adapterrel rendelkező virtuális gépet (a virtuális gép mérete által támogatott számmal).
- Alapértelmezés szerint a virtuális géphez csatolt első hálózati adapter az *elsődleges* hálózati adapterként van meghatározva. A virtuális gép összes többi hálózati adaptere *másodlagos* hálózati adapter.
- Bár szabályozhatja, hogy melyik hálózati adaptert küldi el a kimenő forgalom, alapértelmezés szerint a virtuális gépről érkező összes kimenő forgalom az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt IP-címet kapja meg.
- A múltban az azonos rendelkezésre állási csoportba tartozó összes virtuális gépnek egyetlen vagy több hálózati adapterrel kell rendelkeznie. A tetszőleges számú hálózati adapterrel rendelkező virtuális gépek már ugyanabban a rendelkezésre állási készletben létezhetnek, a virtuális gép mérete által támogatott számmal. A létrehozáskor csak egy virtuális gépet adhat hozzá rendelkezésre állási készlethez. További információ a rendelkezésre állási csoportokról: [virtuális gépek rendelkezésre állásának kezelése az Azure-ban](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Míg az azonos virtuális GÉPEN lévő hálózati adapterek egy virtuális hálózaton belül más alhálózatokhoz is csatlakoztathatók, a hálózati adaptereknek mind ugyanahhoz a virtuális hálózathoz kell csatlakozniuk.
- Bármely elsődleges vagy másodlagos hálózati adapter IP-konfigurációjához bármilyen IP-címet hozzáadhat egy Azure Load Balancer háttér-készlethez. A múltban csak az elsődleges hálózati adapter elsődleges IP-címe adható hozzá a háttér-készlethez. További információ az IP-címekről és a konfigurációkról: [IP-címek hozzáadása, módosítása vagy eltávolítása](virtual-network-network-interface-addresses.md).
- A virtuális gép törlése nem törli a hozzá csatolt hálózati adaptereket. Ha töröl egy virtuális gépet, a hálózati adapterek le lesznek választva a virtuális gépről. A hálózati adaptereket különböző virtuális gépekhez is hozzáadhatja, vagy törölheti őket.
- Mint az IPv6 esetében, nem csatolhat olyan hálózati adaptert, amelynek a létrehozása után a gyorsított hálózatkezelés engedélyezve van a virtuális géphez. A gyorsított hálózatkezelés kihasználása érdekében a virtuális gép operációs rendszerének lépéseit is végre kell hajtania. További információ a gyorsított hálózatkezelésről és egyéb korlátozásokról Windows vagy [Linux](create-vm-accelerated-networking-cli.md) [rendszerű](create-vm-accelerated-networking-powershell.md) virtuális gépek esetén.

## <a name="next-steps"></a>Következő lépések
Ha több hálózati adapterrel vagy IP-címmel rendelkező virtuális gépet szeretne létrehozni, tekintse meg a következő cikkeket:

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása több IPv4-címmel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása magánhálózati IPv6-cím (Azure Load Balancer mögött)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
