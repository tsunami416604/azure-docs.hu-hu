---
title: A hálózati adapterek hozzáadása vagy eltávolítása abból az Azure virtual machines |} A Microsoft Docs
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
ms.openlocfilehash: 63baa023f781753755570bad9c714b17c7eebc95
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424591"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>A hálózati adapterek hozzáadása vagy eltávolítása a hálózati adapterek virtuális gépekről

Útmutató hozzáadása egy meglévő hálózati adaptert, amikor létrehoz egy Azure virtuális gép (VM), vagy adja hozzá, vagy távolítsa el a hálózati adapterek a leállított (felszabadított) állapotba a meglévő virtuális gépről. Hálózati adapter lehetővé teszi, hogy kommunikáljanak az internettel, az Azure és helyszíni erőforrások az Azure virtuális gépként. Virtuális gép egy vagy több hálózati adapter rendelkezhet. 

Ha meg kell hozzáadása, módosítása vagy eltávolítása a hálózati illesztő IP-címeket, tekintse meg [hálózati adapter IP-címek kezelése](virtual-network-network-interface-addresses.md). Ha szeretne létrehozni, módosításához vagy a hálózati adapterek törlésével, lásd: [hálózati adapterek kezelése](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz az Azure PowerShell-modul verzióját 5.2.0 vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.26-os verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Meglévő hálózati adapterek hozzáadása egy új virtuális Géphez

Amikor létrehoz egy virtuális gépet a portálon keresztül, a portál létrehoz egy hálózati adaptert az alapértelmezett beállításokkal, és csatlakoztatja a virtuális gép az Ön számára. Nem lehet a meglévő hálózati adapterek hozzáadása egy új virtuális Géphez, és több hálózati adapterrel rendelkező virtuális gép létrehozása az Azure portal használatával. Úgy teheti meg, mind a parancssori felület vagy a PowerShell használatával, de győződjön meg arról, hogy megismerje a a [megkötések](#constraints). Ha több hálózati adapterrel rendelkező virtuális Gépet hoz létre, az operációs rendszer megfelelően használhatja őket a virtuális gép létrehozása után is konfigurálnia kell. Ismerje meg, hogyan konfigurálhatja [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapter esetében.

### <a name="commands"></a>Parancsok

A virtuális Gépet hoz létre, mielőtt hálózati adaptert létrehozni a lépéseket követve [hozzon létre egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface).

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Hálózati adapter hozzáadása egy meglévő virtuális gép

1. Jelentkezzen be az Azure portálra.
2. A keresőmezőbe, a portál tetején, írja be a nevét, a virtuális gép, amelyhez a hálózati adapter hozzáadása, vagy keresse meg a virtuális gép kiválasztásával kívánt **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, kattintson rá. A virtuális gép támogatnia kell a hozzáadni kívánt hálózati adapterek számát. Ismerje meg, hány hálózati adapterek minden virtuális gép mérete támogatja, lásd: [az Azure-ban Linux rendszerű virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [méretek a Windows virtuális gépek az Azure-ban](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Válassza ki **áttekintése**alatt **beállítások**. Válassza ki **leállítása**, majd várjon, amíg a **állapot** módosítja a virtuális gép **leállítva (felszabadítva)**. 
4. Válassza ki **hálózatkezelés**alatt **beállítások**.
5. Válassza ki **hálózati adapter csatolása**. Nem jelenleg egy másik virtuális géphez csatolt hálózati adapterek listájából válassza ki a kívánt csatolása. 

    >[!NOTE]
    A kiválasztott hálózati adapter nem rendelkezik a gyorsított hálózatkezelés engedélyezett, nem rendelkezik hozzárendelt IPv6-címmel, és tartalmazza a jelenleg a virtuális Géphez csatolt hálózati adapter ugyanazon a virtuális hálózaton már léteznie kell. 

    Ha egy meglévő hálózati adapter nem rendelkezik, akkor először létre kell hoznia egyet. Ehhez válassza ki a **létrehozás hálózati adapter**. Hozzon létre egy hálózati adaptert kapcsolatos további tudnivalókért lásd: [hozzon létre egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface). További korlátozások további hálózati adaptereket a virtuális gépek hozzáadásakor, lásd: [megkötések](#constraints).

6. Kattintson az **OK** gombra.
7. Válassza ki **áttekintése**alatt **beállítások**, majd **Start** a virtuális gép elindításához.
8. A virtuális gép operációs rendszeréhez több hálózati adapterrel megfelelően használatára konfigurálja. Ismerje meg, hogyan konfigurálhatja [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) több hálózati adapter esetében.

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az virtuális gép hálózati adapter hozzáadása](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (hivatkozás) vagy [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Virtuális gép hálózati adaptereinek megtekintése

Megtekintheti a jelenleg csatlakoztatva van egy virtuális géphez, mindegyik hálózati adapter konfigurációjával kapcsolatos további hálózati adaptereket, és az egyes hálózati adapterhez hozzárendelt IP-címeket. 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a tulajdonos, közreműködő vagy hálózati közreműködő szerepkört az előfizetés. Szerepkörök hozzárendelése a fiókokhoz kapcsolatos további tudnivalókért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A szöveget tartalmazó mezőbe **erőforrások keresése** írja be az Azure portal tetején **virtuális gépek**. Amikor **virtuális gépek** megjelenik a keresési eredmények között, válassza ki.
3. Válassza ki a virtuális gép, amelynek meg szeretné tekinteni a hálózati adapter nevét.
4. Az a **beállítások** a kiválasztott virtuális gép, jelölje ki a következő szakaszban **hálózatkezelés**. Hálózati adapter beállításait és azok megváltoztatásáról kapcsolatos további információkért lásd: [hálózati adapterek kezelése](virtual-network-network-interface.md). Hozzáadása, módosítása vagy eltávolítása egy hálózati adapterhez hozzárendelt IP-címeket kapcsolatos további információkért lásd: [hálózati adapter IP-címek kezelése](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Távolítsa el a hálózati adaptert egy virtuális gépről

1. Jelentkezzen be az Azure portálra.
2. A keresőmezőbe, a portál tetején, keresse meg az eltávolítani kívánt virtuális gép nevét (leválasztása) a hálózati adaptert a, vagy a tallózással keresse meg a virtuális gép kiválasztásával **minden szolgáltatás**, majd **virtuális gépek**. Miután a virtuális Gépet talált, kattintson rá.
3. Válassza ki **áttekintése**alatt **beállítások**, majd **leállítása**. Várja meg, amíg a **állapot** módosítja a virtuális gép **leállítva (felszabadítva)**. 
4. Válassza ki **hálózatkezelés**alatt **beállítások**.
5. Válassza ki **leválasztási hálózati adapter**. Jelenleg a virtuális géphez csatlakoztatott hálózati adapterek listájából válassza ki a leválasztani kívánt hálózati adaptert. 

    >[!NOTE]
    Ha csak egy hálózati adapter szerepel a listán, választhatja le, mert a virtuális gép mindig rendelkeznie kell legalább egy hálózati adapter csatlakozik.
6. Kattintson az **OK** gombra.

### <a name="commands"></a>Parancsok

|Eszköz|Parancs|
|---|---|
|parancssori felület|[Eltávolítás az vm nic](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (hivatkozás) vagy [részletes lépések](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-azurermvmnetworkinterface parancsmagban](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (hivatkozás) vagy [részletes lépések](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Korlátozások

- Virtuális gép rendelkeznie kell legalább egy hálózati adapter csatlakozik.
- Virtuális gép legfeljebb, a hálózati kapcsolat, a virtuális gép mérete által támogatott kapcsolódik. További információért, hány hálózati adapterekkel kapcsolatos minden virtuális gép mérete támogatja, lásd: [az Azure-ban Linux rendszerű virtuális gépek méretei](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [méretek a Windows virtuális gépek az Azure-ban](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Méretek támogatják a legalább két hálózati adaptert.
- A hálózati adapterek hozzáadása egy virtuális Géphez jelenleg nem lehet csatolni, egy másik virtuális géphez. Hálózati adapterek létrehozásával kapcsolatos további tudnivalókért lásd: [hozzon létre egy hálózati adaptert](virtual-network-network-interface.md#create-a-network-interface).
- Múltbeli időpont hálózati adapterek csak nem kell hozzá, amely támogatja több hálózati adapterrel és jöttek létre legalább két hálózati adapterrel rendelkező virtuális gépeket. Ön nem adható hozzá egy hálózati adapter egy-egy hálózati adapterrel, létrehozott virtuális gép még akkor is, ha a virtuális gép mérete támogatja több hálózati adapterrel. Ezzel szemben csak eltávolítható hálózati adapterek a hálózati adapterek legalább három virtuális gép, mert a virtuális gépeket legalább két hálózati adapter mindig kell hozniuk legalább két hálózati adaptert hoz létre. Ezek a korlátozások egyike sem többé vonatkoznak. Mostantól létrehozhat egy virtuális gép tetszőleges számú hálózati adapterrel (akár a virtuális gép mérete által támogatott szám).
- Alapértelmezés szerint az első hálózati adaptert egy virtuális Géphez csatolt számít, ha a *elsődleges* hálózati adaptert. A virtuális gép más hálózati adapterek vannak *másodlagos* hálózati adapterek.
- Bár szabályozhatja, hogy mely hálózati adapter küldött kimenő forgalmat, alapértelmezés szerint, a virtuális gép minden kimenő forgalom küldése az elsődleges hálózati adapter elsődleges IP-konfigurációhoz rendelt IP-cím.
- Múltbeli időpont az azonos rendelkezésre állási csoporton belül minden virtuális gép rendelkezniük kellett egy egyetlen vagy több hálózati adapterrel. Tetszőleges számú hálózati adapterrel rendelkező virtuális gépek mostantól létrejöhet ugyanabban a rendelkezésre állási csoportban, akár a virtuális gép mérete által támogatott számra. Virtuális gép csak egy rendelkezésre állási csoport létrehozásakor adhat hozzá. A rendelkezésre állási csoportok kapcsolatos további információkért lásd: [az Azure-beli virtuális gépek rendelkezésre állásának kezelése](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Hálózati adaptereinek ugyanazon a virtuális Gépen csatlakoztatható egy virtuális hálózaton belül különböző alhálózatokat, miközben a hálózati adapterek összes kapcsolódnia kell az azonos virtuális hálózatban.
- Minden olyan IP-konfiguráció minden olyan elsődleges vagy másodlagos hálózati adapter IP-címeket adhat hozzá egy Azure Load Balancer háttérkészlethez. Korábban csak az elsődleges IP-címet az elsődleges hálózati adapter sikerült felvenni a háttérkészlethez. IP-címek és konfigurációkkal kapcsolatos további tudnivalókért lásd: [hozzáadása, módosítása vagy eltávolítása IP-címek](virtual-network-network-interface-addresses.md).
- Virtuális gép törlésével nem törli a hozzá csatlakozó hálózati adaptereket. Ha töröl egy virtuális Gépet, a hálózati adaptereket a virtuális gépről leválasztotta őket. Adja hozzá a hálózati adaptereket különböző virtuális gépekhez, vagy törölheti őket.
- Ha egy hálózati adapterrel rendelkezik hozzárendelt magánhálózati IPv6-címek, hozzá kell adnia (csatolás) azt egy virtuális Géphez a virtuális gép létrehozásakor. A virtuális gép létrehozása után egy hozzárendelt IPv6-címmel rendelkező hálózati adapter nem lehet hozzáadni egy virtuális Gépet. Adhat hozzá rendelt magánhálózati IPv6-cím egy hálózati adaptert egy virtuális gépet hoz létre, ha a virtuális gép, hány hálózati adapter támogatja a virtuális gép méretétől függetlenül csak a hálózati adapter is hozzáadhat. Lásd: [hálózati adapter IP-címek kezelése](virtual-network-network-interface-addresses.md) további információ a hálózati adapterek IP-címek hozzárendelése.
- IPv6-hálózati adapter gyorsított hálózatkezeléssel engedélyezve van a virtuális gép létrehozása után nem csatolható. További gyorsított hálózatkezelés előnyeit is ki kell töltenie lépéseit a virtuális gép operációs rendszerén belül. Gyorsított hálózatkezeléssel kapcsolatos további vagy egyéb megszorítások ismerje meg, ha használ, a [Windows](create-vm-accelerated-networking-powershell.md) vagy [Linux](create-vm-accelerated-networking-cli.md) virtuális gépeket.

## <a name="next-steps"></a>További lépések
Hozzon létre egy virtuális Géphez több hálózati adapterrel vagy IP-címek, olvassa el a következő cikkeket:

### <a name="commands"></a>Parancsok

|Tevékenység|Eszköz|
|---|---|
|Több hálózati adapterrel rendelkező virtuális gép létrehozása|[Parancssori felület](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Hozzon létre egy egyetlen hálózati adapterrel rendelkező virtuális több IPv4-cím|[Parancssori felület](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Hozzon létre egy egyetlen hálózati adapterrel rendelkező virtuális magánhálózati IPv6-címek (mögött az Azure Load Balancerhez)|[Parancssori felület](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sablon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|


