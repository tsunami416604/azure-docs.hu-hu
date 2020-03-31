---
title: Hálózati csatolók hozzáadása az Azure virtuális gépeihez, illetve azok eltávolítása
description: Megtudhatja, hogy miként adhat hálózati adaptereket a virtuális gépekhez, illetve hogyan távolíthatja el a hálózati adaptereket.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 4169bfb5da5b1ad13bab0eb01397f7c1fb20b11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060324"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Hálózati adapterek hozzáadása vagy eltávolítása virtuális gépekről

Ismerje meg, hogyan adhat hozzá egy meglévő hálózati adaptert, amikor létrehoz egy Azure virtuális gépet (VM). Azt is megtudhatja, hogy hálózati adapterek hozzáadása vagy eltávolítása egy meglévő virtuális gép a leállított (felszabadított) állapotban. A hálózati felület lehetővé teszi, hogy az Azure virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. A virtuális gép egy vagy több hálózati adapterrel rendelkezik. 

Ha hálózati adapter IP-címeinek hozzáadására, módosítására vagy eltávolítására van szüksége, olvassa el [a Hálózati adapter IP-címeinek kezelése című témakört.](virtual-network-network-interface-addresses.md) Hálózati adapterek létrehozásáról, módosításáról vagy törléséről a [Hálózati adapterek kezelése](virtual-network-network-interface.md)című témakörben látható.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nem rendelkezik ilyennel, állítson be egy Aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). A cikk további részének megkezdése előtt hajtsa végre az alábbi feladatok egyikét:

- **Portál felhasználói:** Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók:** Vagy futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/powershell)vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az Azure Cloud Shell böngészőlapján keresse meg a **Környezet kiválasztása** legördülő listát, majd válassza a **PowerShellt,** ha még nincs kiválasztva.

    Ha a PowerShellt helyileg futtatja, használja az Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Futtassa a `Connect-AzAccount` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói:** Vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/bash)vagy a CLI a számítógépről. Az Azure CLI 2.0.26-os vagy újabb verzióját használja, ha helyileg futtatja az Azure CLI-t. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Futtassa a `az login` parancsot, hogy kapcsolatot hozzon létre az Azure-ral.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati csatolók hozzáadása új virtuális géphez

Amikor virtuális gépet hoz létre a portálon keresztül, a portál létrehoz egy hálózati adaptert az alapértelmezett beállításokkal, és csatlakoztatja a hálózati adaptert a virtuális géphez. A portál használatával nem adhat hozzá meglévő hálózati adaptereket egy új virtuális géphez, vagy több hálózati adapterrel rendelkező virtuális gép létrehozásához. Mindkettőt megteheti a CLI vagy a PowerShell használatával. Ügyeljen arra, hogy megismerkedjen a [korlátokkal.](#constraints) Ha több hálózati adapterrel rendelkező virtuális gép létrehozása, akkor is be kell állítania az operációs rendszert, hogy megfelelően használja őket a virtuális gép létrehozása után. További információ a [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy a [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati kapcsolathoz való konfigurálásáról.

### <a name="commands"></a>Parancsok

A virtuális gép létrehozása előtt [hozzon létre egy hálózati adaptert.](virtual-network-network-interface.md#create-a-network-interface)

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[Új-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Hálózati adapter hozzáadása meglévő virtuális géphez

Hálózati adapter hozzáadása a virtuális géphez:

1. Az [Azure Portalon](https://portal.azure.com) megkeresheti a meglévő virtuális gépet. Keressen és válasszon **virtuális gépek**lehetőséget.

2. Válassza ki a virtuális gép nevét. A virtuális gépnek támogatnia kell a hozzáadni kívánt hálózati adapterek számát. Ha meg szeretné tudni, hogy az egyes virtuális gépek méretehány hálózati adaptert támogat, tekintse meg az Azure [linuxos virtuális gépekhez](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy Windows virtuális gépekhez való [méreteit.](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

3. A Virtuálisgép parancssávján válassza a **Leállítás**lehetőséget, majd a megerősítést kérő **párbeszédpanelen** az OK gombot. Ezután várja **meg,** amíg a virtuális gép állapota **Leállítva (felszabadított)** lesz.

4. A Virtuális gép menüsorából válassza a **Hálózati** > **csatoló csatolása**lehetőséget. Ezután a **Meglévő hálózati adapter csatolása**területen válassza ki a csatolni kívánt hálózati illesztőfelületet, és kattintson az **OK gombra.**

    >[!NOTE]
    >A kiválasztott hálózati adapternem engedélyezhető a gyorsított hálózatkezelés, nem rendelhető hozzá IPv6-cím, és ugyanannál a virtuális hálózatnál kell léteznie, amelyen a virtuális géphez jelenleg csatlakoztatott hálózati adapter található.

    Ha nem rendelkezik meglévő hálózati adapter, először létre kell hoznia egyet. Ehhez válassza a **Hálózati adapter létrehozása**lehetőséget. A hálózati adapter létrehozásáról a Hálózati adapter létrehozása című témakörben olvashat [bővebben.](virtual-network-network-interface.md#create-a-network-interface) Ha többet szeretne tudni arról, hogy milyen további korlátozások vannak a hálózati adapterek virtuális gépekhez való hozzáadásakor, olvassa el a Kényszerek ( Korlátozások ) [témakört.](#constraints)

5. A virtuális gép menüsorában válassza **az Áttekintés** > **kezdete** lehetőséget a virtuális gép újraindításához.

Most konfigurálhatja a virtuális gép operációs rendszerét, hogy több hálózati adaptert használjon megfelelően. További információ a [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy a [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati kapcsolathoz való konfigurálásáról.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (hivatkozás); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Virtuális gép hálózati adaptereinek megtekintése

Megtekintheti a virtuális géphez jelenleg csatlakoztatott hálózati adaptereket, hogy megismerje az egyes hálózati adapterek konfigurációját, valamint az egyes hálózati adapterekhez rendelt IP-címeket. 

1. Az [Azure Portalon](https://portal.azure.com) megkeresheti a meglévő virtuális gépet. Keressen és válasszon **virtuális gépek**lehetőséget.

    >[!NOTE]
    >Jelentkezzen be egy olyan fiókkal, amely az előfizetéshez tulajdonosi, közreműködői vagy hálózati közreműködői szerepkörrel van elrendelve. Ha többet szeretne tudni aszerepkörök fiókokhoz való hozzárendeléséről, olvassa [el az Azure szerepköralapú hozzáférés-vezérlésbeépített szerepköreit.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

2. Válassza ki annak a virtuális gépnek a nevét, amelynek a csatlakoztatott hálózati csatolóit meg szeretné tekinteni.

3. A Virtuálisgép menüsorban válassza a **Hálózat lehetőséget.**

A hálózati adapter beállításairól és módosításukról a [Hálózati csatolók kezelése (Manage) (Hálózati adapterek kezelése) témakörben](virtual-network-network-interface.md)olvashat. A hálózati adapterhez rendelt IP-címek hozzáadásáról, módosításáról és eltávolításáról a [Hálózati adapter IP-címeinek kezelése című](virtual-network-network-interface-addresses.md)témakörben olvashat.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic lista](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Hálózati adapter eltávolítása a virtuális gépről

1. Az [Azure Portalon](https://portal.azure.com) megkeresheti a meglévő virtuális gépet. Keressen és válasszon **virtuális gépek**lehetőséget.

2. Válassza ki annak a virtuális gépnek a nevét, amelynek a csatlakoztatott hálózati csatolóit meg szeretné tekinteni.

3. A Virtuálisgép eszköztáron válassza a **Leállítás választót.**

4. **Várjon,** amíg a virtuális gép állapota **Leállítva (felszabadított)** lesz.

5. A Virtuálisgép menüsorából válassza a **Hálózati** > **leválasztás hálózati adapter lehetőséget.**

6. A **Hálózati adapter leválasztása** párbeszédpanelen jelölje ki a leválasztani kívánt hálózati illesztőfelületet. Ezután kattintson az **OK** gombra.

    >[!NOTE]
    >Ha csak egy hálózati csatoló szerepel a listában, nem lehet leválasztani, mert a virtuális géphez mindig legalább egy hálózati illesztőnek kell hozzákapcsolódnia.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm nic eltávolítás](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (hivatkozás); [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Eltávolítás-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás); [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- A virtuális géphez legalább egy hálózati adapternek hozzá kell kapcsolódnia.

- A virtuális gép csak annyi hálózati adapterek csatolták hozzá, mint a virtuális gép mérete támogatja. Ha többet szeretne megtudni arról, hogy az egyes virtuális gépek méretehány hálózati adaptert támogat, tekintse meg az Azure [linuxos virtuális gépekhez](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy Windows virtuális gépekhez való [méreteit.](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Minden méretlegalább két hálózati adaptert támogat.

- A virtuális géphez hozzáadott hálózati adapterek jelenleg nem csatolhatók egy másik virtuális géphez. A hálózati adapterek létrehozásáról a Hálózati adapter létrehozása című témakörben olvashat [bővebben.](virtual-network-network-interface.md#create-a-network-interface)

- A múltban csak olyan virtuális gépekhez adhat hálózati adaptereket, amelyek több hálózati adaptert támogattak, és legalább két hálózati adapterrel jöttek létre. Nem lehetett hálózati adaptert hozzáadni egy virtuális géphez, amely et egy hálózati adapterrel hoztak létre, még akkor sem, ha a virtuális gép mérete egynél több hálózati adaptert támogatott. Ezzel szemben csak a legalább három hálózati csatolákkal rendelkező virtuális gép hálózati csatolóiközül távolíthatja el a hálózati adaptereket, mert a legalább két hálózati adapterrel létrehozott virtuális gépeknek mindig legalább két hálózati adapterrel kellett rendelkezniük. Ezek a megkötések már nem érvényesek. Most már létrehozhat egy virtuális gép tetszőleges számú hálózati adapterek (a virtuális gép mérete által támogatott számig).

- Alapértelmezés szerint a virtuális géphez csatolt első hálózati adapter az *elsődleges* hálózati csatoló. A virtuális gép összes többi hálózati csatolója *másodlagos* hálózati csatoló.

- Szabályozhatja, hogy melyik hálózati felületre küldkimenő forgalmat. Azonban a virtuális gép alapértelmezés szerint küldi az összes kimenő forgalmat az IP-cím, amely az elsődleges IP-konfiguráció az elsődleges hálózati adapter.

- A múltban az azonos rendelkezésre állási csoporton belüli összes virtuális gépnek egyetlen vagy több hálózati adapterrel kellett rendelkeznie. Tetszőleges számú hálózati adapterrel rendelkező virtuális gépek most már létezhetnek ugyanabban a rendelkezésre állási csoportban, a virtuális gép mérete által támogatott számig. Csak akkor adhat hozzá virtuális gép egy rendelkezésre állási csoport létrehozásakor. A rendelkezésre állási csoportokról a [Virtuális gépek azure-beli elérhetőségének kezelése című témakörben olvashat](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)bővebben.

- Az azonos virtuális gépen lévő hálózati adaptereket a virtuális hálózaton belüli különböző alhálózatokhoz csatlakoztathatja. A hálózati adaptereknek azonban ugyanahhoz a virtuális hálózathoz kell csatlakozniuk.

- Bármely elsődleges vagy másodlagos hálózati adapter bármely IP-konfigurációjához bármilyen IP-címet hozzáadhat egy Azure Load Balancer háttérkészlethez. A múltban csak az elsődleges hálózati adapter elsődleges IP-címe adható hozzá egy háttérkészlethez. Az IP-címekről és -konfigurációkról az [IP-címek hozzáadása, módosítása és eltávolítása](virtual-network-network-interface-addresses.md)című témakörben olvashat bővebben.

- A virtuális gép törlése nem törli a hozzá csatlakoztatott hálózati adaptereket. Virtuális gép törlésekor a hálózati adapterek leválnak a virtuális gépről. Ezeket a hálózati adaptereket hozzáadhatja a különböző virtuális gépekhez, vagy törölheti őket.

- Az IPv6-hoz is nem csatolhat olyan hálózati adaptert, amelyen a gyorsított hálózat engedélyezve van a virtuális géphez a létrehozása után. Továbbá a gyorsított hálózatkezelés előnyeinek kihasználásához a virtuális gép operációs rendszerén belül is végre kell hajtania a lépéseket. További információ a windowsos vagy [Linuxos](create-vm-accelerated-networking-cli.md) virtuális gépeken futó gyorsított hálózatkezelésről [és](create-vm-accelerated-networking-powershell.md) egyéb korlátozásokról.

## <a name="next-steps"></a>További lépések

Több hálózati adapterrel vagy IP-címmel rendelkező virtuális gép létrehozásáról a következő témakörben lehet tudni:

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Egyetlen hálózati adapter virtuális gép létrehozása több IPv4-címmel|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Egyetlen hálózati adapter virtuális gép létrehozása privát IPv6-címmel (egy Azure Load Balancer mögött)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
