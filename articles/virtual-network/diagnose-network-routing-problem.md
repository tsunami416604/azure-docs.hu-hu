---
title: Egy Azure virtuális gép útválasztási probléma diagnosztizálása |} A Microsoft Docs
description: Megtudhatja, hogyan által a virtuális gép az érvényes útvonalak megtekintése egy virtuális gép útválasztási probléma diagnosztizálása érdekében.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: jdial
ms.openlocfilehash: 6864e282319bc5a0539c4c94f3062dcab7315970
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652246"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Virtuális gép útválasztási problémáinak diagnosztizálása

Ebből a cikkből elsajátíthatja, hogyan útválasztási problémáinak diagnosztizálása az útvonalakat, amelyek érvényesek a hálózati illesztő a virtuális gépeken (VM) megtekintésével. Az Azure minden egyes virtuális hálózat alhálózatának több alapértelmezett útvonalakat hoz létre. Útvonalak a egy útválasztási táblázatot, és majd társítása az útválasztási táblázatot egy alhálózathoz, az Azure alapértelmezett útvonalakat felül lehet bírálni. Útvonalakat hoz létre, az Azure alapértelmezett útvonalakat és a border gateway protocol (BGP), keresztül (Ha a virtuális hálózat a helyszíni hálózathoz csatlakozik) az Azure VPN-átjárón keresztül a helyszíni hálózatról propagálja hozzáférésvezérlési kombinációja a az alhálózat összes hálózati adapter érvényes útvonalai. Ha még nem ismeri a virtuális hálózathoz, a hálózati adapter vagy a fogalmak útválasztást, [virtuális hálózatok áttekintése](virtual-networks-overview.md), [hálózati adapter](virtual-network-network-interface.md), és [Útválasztás áttekintése](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Forgatókönyv

Próbál csatlakoztatni egy virtuális géphez, de a kapcsolat hibája esetén. Annak megállapításához, hogy miért nem tud csatlakozni a virtuális gép, megtekintheti az Azure hálózati adapter érvényes útvonalaihoz [portál](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI-vel](#diagnose-using-azure-cli).

A következő lépések azt feltételezik, hogy rendelkezik egy meglévő virtuális gép az érvényes útvonalak megtekintése. Ha egy meglévő virtuális gép nem rendelkezik, először telepítse a [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális Gépet ebben a cikkben a feladatok végrehajtásához. Ebben a cikkben szereplő példák vannak nevű virtuális gép *myVM* nevű hálózati adapter *myVMVMNic*. A virtuális gép és a hálózati adapter nevű erőforráscsoportban vannak *myResourceGroup*, és a *USA keleti RÉGIÓJA* régióban. Módosítsa az értékeket a megfelelő a lépéseket, a virtuális gép a probléma diagnosztizálása vannak.

## <a name="diagnose-using-azure-portal"></a>Diagnosztika az Azure portal használatával

1. Jelentkezzen be az Azure [portál](https://portal.azure.com) egy Azure-fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).
2. Az Azure portal tetején adja meg a virtuális gép futó állapotba kerül, a keresési mezőbe, a nevét. Amikor a virtuális gép neve megjelenik a keresési eredmények között, kattintson rá.
3. Válassza ki **diagnosztizálása és a problémák megoldásához**, majd a **javasolt lépések**válassza **érvényes útvonalak** 7, a konfigurációelem-a következő képen látható módon:

    ![Érvényes útvonalak megtekintése](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Az érvényes útvonalak nevű hálózati adapter **myVMVMNic** vannak, az alábbi képen is látható:

     ![Érvényes útvonalak megtekintése](./media/diagnose-network-routing-problem/effective-routes.png)

    Ha a virtuális géphez több hálózati adapterrel, megtekintheti bármely hálózati adapter érvényes útvonalaihoz kiválasztva. Mindegyik hálózati adapter egy másik alhálózat lehet, mivel mindegyik hálózati adapter rendelkezhet különböző érvényes útvonalakat.

    Az előző képen látható példában a felsorolt útvonalak alapértelmezett útvonalakat, amelyek az Azure létrehozza az egyes alhálózatokon. A listában legalább ezeket az útvonalakat tartalmaz, de előfordulhat, hogy további útvonalakat függően előfordulhat, hogy a virtuális hálózat és egy másik virtuális hálózat társviszonyban álló, vagy egy Azure VPN-átjárón keresztül a helyszíni hálózathoz csatlakozik, például engedélyezett képességek. Minden egyes az útvonalakat, és a többi útvonal a hálózati adapter látni kapcsolatos további információkért lásd: [virtuális hálózat forgalmának útválasztása](virtual-networks-udr-overview.md). Ha a lista útvonal sok van, akkor előfordulhat, hogy egyszerűbb kiválasztásához **letöltése**, hogy töltse le az útvonalak listája egy .csv-fájlt.

Érvényes útvonalak a virtuális Gépet az előző lépésekben is tekinthetők meg, bár az érvényes útvonalak keresztül is megtekintheti egy:
- **Egyes hálózati adapter**: Ismerje meg, hogyan [egy hálózati adapter megtekintésére](virtual-network-network-interface.md#view-network-interface-settings).
- **Az egyes útvonaltábla**: Ismerje meg, hogyan [megtekintése egy útválasztási táblázatot](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosztizálhatja a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a számítógépről futtatja a PowerShell, szüksége van-e az Azure PowerShell-modult, 1.0.0-s verziójának vagy újabb. Futtatás `Get-Module -ListAvailable Az` a számítógépen, a telepített verzió azonosításához. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha Ön helyileg futtatja a Powershellt, is futtatni szeretné `Connect-AzAccount` bejelentkezni az Azure-bA egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

Az érvényes útvonalak beolvasása a hálózati adapter [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable). Az alábbi példa lekéri nevű hálózati adapter érvényes útvonalaihoz *myVMVMNic*, azaz egy erőforráscsoportba tartozó nevű *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

A parancs kimenetében található információk megismeréséhez tekintse meg [Útválasztás áttekintése](virtual-networks-udr-overview.md). Kimeneti csak akkor ad vissza, ha a virtuális gép futó állapotban van. Ha a virtuális géphez több hálózati adapterrel, áttekintheti az egyes hálózati adapter érvényes útvonalaihoz. Mindegyik hálózati adapter egy másik alhálózat lehet, mivel mindegyik hálózati adapter rendelkezhet különböző érvényes útvonalakat. Ha még nem kommunikációs probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati adapter nevét, de tudja a nevét, a virtuális gép a hálózati adapter csatlakoztatva van, az alábbi parancsokat a virtuális Géphez csatlakoztatott összes hálózati adapter azonosítóit adja vissza:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet kapja:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimeneti hálózati adapter neve van *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztika az Azure CLI-vel

A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez a cikkhez az Azure CLI 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` , és jelentkezzen be Azure-fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

Az érvényes útvonalak beolvasása a hálózati adapter [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table). Az alábbi példa lekéri nevű hálózati adapter érvényes útvonalaihoz *myVMVMNic* nevű erőforráscsoportban ez *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A parancs kimenetében található információk megismeréséhez tekintse meg [Útválasztás áttekintése](virtual-networks-udr-overview.md). Kimeneti csak akkor ad vissza, ha a virtuális gép futó állapotban van. Ha a virtuális géphez több hálózati adapterrel, áttekintheti az egyes hálózati adapter érvényes útvonalaihoz. Mindegyik hálózati adapter egy másik alhálózat lehet, mivel mindegyik hálózati adapter rendelkezhet különböző érvényes útvonalakat. Ha még nem kommunikációs probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati adapter nevét, de tudja a nevét, a virtuális gép a hálózati adapter csatlakoztatva van, az alábbi parancsokat a virtuális Géphez csatlakoztatott összes hálózati adapter azonosítóit adja vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>A probléma megoldásához

Útválasztási problémák megoldásához általában áll:

- Hozzáadása egy egyéni útvonalon, egyet az Azure alapértelmezett útvonalak felülírásához. Ismerje meg, hogyan [adjon hozzá egy egyéni útvonalat](manage-route-table.md#create-a-route).
- Módosítsa vagy törölje egy egyéni útvonalon, amelyek nem kívánt helyre útválasztás okozhatnak. Ismerje meg, hogyan [módosítása](manage-route-table.md#change-a-route) vagy [törlése](manage-route-table.md#delete-a-route) egy egyéni útvonalon.
- Annak biztosítása, hogy az bármilyen egyéni útvonalakat tartalmazó útvonaltábla meghatározta az alhálózatot a hálózati adapterhez van társítva. Ismerje meg, hogyan [egy alhálózathoz útválasztási táblázat társítása](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Annak biztosítása, hogy eszközök, például az Azure VPN gateway vagy a hálózati virtuális berendezések üzembe helyezte működtethető. Használja a [VPN-diagnosztika](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) képességét a Network Watcher határozza meg az Azure VPN gateway problémáit.

Ha továbbra is kommunikációs problémákat tapasztal, tekintse meg [szempontok](#considerations) és további elemzés céljából.

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következőket, amikor kommunikációs problémák hibaelhárítása:

- Útválasztás alapján történik a leghosszabb előtag-megfeleltetés (LPM) útvonalak között, hogy meghatározta, border gateway protocol (BGP) és a rendszer útvonalakat. Ha egynél több útvonal azonos LPM van, akkor egy útvonal alapján választja ki a listában szereplő sorrendben megfeleltetéssel [Útválasztás áttekintése](virtual-networks-udr-overview.md#how-azure-selects-a-route). Érvényes útvonalak csak láthatja a tényleges útvonalakat, amelyek egy LPM egyezik, az összes elérhető útvonal alapján. Jelent meg, hogyan értékeli ki az útvonalakat a hálózati adapter teszi sokkal egyszerűbb, amely érintheti a kommunikációt a virtuális Géphez megadott útvonalakkal kapcsolatos hibaelhárítás.
- Ha egy hálózati virtuális készüléket (NVA), egyéni útvonalak a definiált *virtuális készülék* , következő ugrási típusba kerül, győződjön meg arról, hogy az nva-t a forgalmat fogadó IP-továbbítás engedélyezve van, vagy a csomagot a rendszer elveti. Tudjon meg többet [egy hálózati adapter IP-továbbítás engedélyezése](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Ezenkívül az operációs rendszer vagy alkalmazás az nva-n belül is kell lennie továbbítja a hálózati forgalmat, és ehhez kell konfigurálni.
- Ha létrehozott egy útvonalat a 0.0.0.0/0, a következő ugrás is megad, például egy nva-t vagy a VPN-átjáró összes kimenő internetes forgalom van átirányítva. Az ilyen útvonal létrehozása gyakran hivatkoznak úgy, ahogy a kényszerített bújtatás. Ezt az útvonalat, attól függően, hogyan a következő ugrás kezeli-e a forgalmat a virtuális géphez az internetről érkező RDP vagy SSH protokollok használatával távoli kapcsolatok esetleg nem fog. Kényszerített bújtatás engedélyezhető:
    - Site-to-site VPN, hozzon létre egy útvonalat a következő ugrási típusú használatakor *VPN-átjáró*. Tudjon meg többet [konfigurálása kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Ha egy 0.0.0.0/0 (alapértelmezett útvonal) van hirdetve BGP egy virtuális hálózati átjárón keresztül használatakor egy helyek közötti VPN vagy ExpressRoute-kapcsolatcsoportot. Tudjon meg többet a BGP használatával egy [site-to-site VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- A virtuális hálózati társviszony-létesítési forgalomhoz működéséhez, egy következő ugrási típusú rendszerútvonal *virtuális hálózatok közötti Társviszony* léteznie kell a virtuális társhálózatba tartozó virtuális hálózat előtag tartomány. Ha az ilyen útvonal nem létezik, és a virtuális hálózati társviszony-létesítési csatolás **csatlakoztatva**:
    - Várjon néhány másodpercet, és próbálkozzon újra. Ha ez egy újonnan létrehozott társviszony-létesítési csatolás, időnként hosszabb ideig tart útvonalakat az alhálózatot a hálózati adapterek propagálása. Virtuális hálózatok közötti társviszony létesítésével kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md) és [kezelheti a virtuális hálózatok közötti társviszony](virtual-network-manage-peering.md).
    - Hálózati biztonsági csoport szabályai kommunikációs érintheti. További információkért lásd: [egy virtuális gép hálózati forgalomszűrési problémáinak diagnosztizálása](diagnose-network-traffic-filter-problem.md).
- Bár az Azure alapértelmezett útvonalakat minden Azure-beli hálózati adapterhez rendeli hozzá, ha a virtuális géphez több hálózati adapterrel rendelkezik, csak az elsődleges hálózati adapter hozzá van rendelve, egy alapértelmezett útvonalat (0.0.0.0/0), vagy az átjáró a virtuális gép operációs rendszerén belül. Ismerje meg, hogyan hozhat létre csatolt másodlagos hálózati adapterek alapértelmezett útvonal egy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuális Gépet. Tudjon meg többet [elsődleges és másodlagos hálózati adapterek](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>További elemzés céljából

* Egy olyan helyre irányuló forgalom esetében a következő ugrás típusa határozza meg a gyors teszteléséhez használja a [következő Ugrás](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure Network Watcher képességét. Következő ugrás kiderül, hogy mi a következő ugrás típusa egy megadott hely felé irányuló forgalom az.
* Nem léteznek útvonalak miatt a virtuális gép hálózati kommunikáció meghiúsul, ha a probléma oka esetleg az lehet a virtuális gép operációs rendszerén belül futó tűzfalszoftverek
* Ha Ön [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) forgalmat egy VPN-átjárót, vagy nva-n keresztül a helyszíni eszközön, akkor nem lehet csatlakozni egy virtuális géphez az internetről, attól függően, hogyan konfigurálta az eszközök útválasztásának. Győződjön meg arról, hogy az eszköz konfigurált útválasztási irányítja a forgalmat, vagy egy nyilvános vagy magánhálózati IP-címet a virtuális gép.
* Használja a [kapcsolódási hibák elhárítása](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) képesség a Network Watcher útválasztási szűrési és az operációs rendszer kimenő kommunikációs problémák okainak meghatározásához.

## <a name="next-steps"></a>További lépések

- Ismerje meg az összes feladatok, tulajdonságai és beállításai egy [irányíthatja a tábla- és útvonalak](manage-route-table.md).
- További információ az összes [következő ugrási típusokat, rendszerútvonalakat és hogyan választ útvonalat az Azure](virtual-networks-udr-overview.md).
