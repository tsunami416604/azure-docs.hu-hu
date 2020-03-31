---
title: Azure virtuálisgép-útválasztási probléma diagnosztizálása | Microsoft dokumentumok
description: Ismerje meg, hogyan diagnosztizálhatja a virtuális gép útválasztási problémáját a virtuális gépek hatékony útvonalainak megtekintésével.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350617"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Virtuálisgép-útválasztási probléma diagnosztizálása

Ebben a cikkben megtudhatja, hogyan diagnosztizálhatja az útválasztási problémát a virtuális gép (VM) hálózati összeköttetéséhez hatékony útvonalak megtekintésével. Az Azure több alapértelmezett útvonalat hoz létre az egyes virtuális hálózati alhálózatokhoz. Felülírhatja az Azure alapértelmezett útvonalait, ha útvonalakat definiál egy útvonaltáblában, majd az útvonaltáblát egy alhálózathoz társítja. A létrehozott útvonalak, az Azure alapértelmezett útvonalai és a helyszíni hálózatról egy Azure VPN-átjárón keresztül propagált útvonalak (ha a virtuális hálózat a helyszíni hálózathoz csatlakozik) a border gateway protokollon (BGP) keresztül, a következők: hatékony útvonalakat az alhálózat összes hálózati illesztőjéhez. Ha nem ismeri a virtuális hálózattal, a hálózati adapterrel vagy az útválasztási koncepciókkal kapcsolatos fogalmakat, olvassa el a [Virtuális hálózat áttekintése](virtual-networks-overview.md), [A hálózati adapter](virtual-network-network-interface.md)és az [Útválasztás áttekintés című témakört.](virtual-networks-udr-overview.md)

## <a name="scenario"></a>Forgatókönyv

Megpróbál csatlakozni egy virtuális géphez, de a kapcsolat megszakad. Annak megállapításához, hogy miért nem tud csatlakozni a virtuális géphez, megtekintheti a hálózati felület hatékony útvonalait az Azure [Portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell)vagy az [Azure CLI](#diagnose-using-azure-cli)használatával.

A következő lépések feltételezik, hogy rendelkezik egy meglévő virtuális gép a hatékony útvonalak megtekintéséhez. Ha nem rendelkezik egy meglévő virtuális gép, először telepítse n [a Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [a Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép a jelen cikkben szereplő feladatok elvégzéséhez. A példák ebben a cikkben egy virtuális gép nevű *myVM* nevű hálózati adapter nevű *myVMNic1*. A virtuális gép és a hálózati adapter egy *myResourceGroup*nevű erőforráscsoportban található, és az *USA keleti régiójában* található. Módosítsa az értékeket a lépésekben, a probléma diagnosztizálása érdekében a virtuális gép.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálás az Azure Portal használatával

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com) egy azure-fiókkal, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)
2. Az Azure Portal tetején adja meg a futó állapotban lévő virtuális gép nevét a keresőmezőbe. Amikor a virtuális gép neve megjelenik a keresési eredmények között, jelölje ki azt.
3. A bal oldali **Beállítások** csoportban válassza a **Hálózat**lehetőséget, és a nevének kiválasztásával keresse meg a hálózati csatoló erőforrását.
     ![Hálózati adapterek megtekintése](./media/diagnose-network-routing-problem/view-nics.png)
4. A bal oldalon válassza a **Hatékony útvonalak lehetőséget.** A **myVMNic1** nevű hálózati adapter ek érvényes útvonalai ![a következő képen láthatók: Hatékony útvonalak megtekintése](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Ha több hálózati adapter van csatlakoztatva a virtuális géphez, bármelyik hálózati adapter érvényes útvonalait megtekintheti annak kiválasztásával. Mivel az egyes hálózati adapterek különböző alhálózatokban lehetnek, minden hálózati adapter különböző hatékony útvonalakkal rendelkezhet.

    Az előző képen látható példában a felsorolt útvonalak olyan alapértelmezett útvonalak, amelyeket az Azure hoz létre az egyes alhálózatokhoz. A lista legalább ezeket az útvonalakat tartalmazza, de további útvonalakkal is rendelkezhet, attól függően, hogy a virtuális hálózathoz engedélyezett-e, például egy másik virtuális hálózattal létesített, vagy egy Azure VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz. Ha többet szeretne megtudni az egyes útvonalakról és a hálózati adapterhez esetleg látható egyéb útvonalakról, olvassa el a [Virtuális hálózati forgalom útválasztása című témakört.](virtual-networks-udr-overview.md) Ha a lista nagy számú útvonallal rendelkezik, könnyebben **kiválaszthatja**a Letöltés lehetőséget, ha az útvonalak listáját tartalmazó .csv fájlt szeretne letölteni.

Bár a hatékony útvonalakat az előző lépésekben a virtuális gépen keresztül tekintették meg, a hatékony útvonalakat a következőkön keresztül is megtekintheti:
- **Egyéni hálózati adapter**: Ismerje meg, hogyan tekintheti meg [a hálózati adaptert.](virtual-network-network-interface.md#view-network-interface-settings)
- **Egyéni útvonaltábla**: Ismerje meg, hogyan tekintheti meg [az útvonaltáblát.](manage-route-table.md#view-details-of-a-route-table)

## <a name="diagnose-using-powershell"></a>Diagnosztizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modul 1.0.0-s vagy újabb verzióra. Futtassa `Get-Module -ListAvailable Az` a számítógépen, hogy megtalálja a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` akkor is futnia kell, hogy olyan fiókkal jelentkezzen be az Azure-ba, amely rendelkezik a [szükséges engedélyekkel.](virtual-network-network-interface.md#permissions)

A [Get-AzEffectiveRouteTable segítségével](/powershell/module/az.network/get-azeffectiveroutetable)a hálózati adapterek hatékony útvonalainak beszereznie. A következő példa a *myVMNic1*nevű hálózati adapter hatékony útvonalait kapja meg, amely egy *myResourceGroup*nevű erőforráscsoportban található:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

A kimenetben visszaadott információk megértéséhez olvassa el az [Útválasztás áttekintéscímű témakört.](virtual-networks-udr-overview.md) Kimenet csak akkor adja vissza, ha a virtuális gép a futó állapotban van. Ha több hálózati adapter van csatlakoztatva a virtuális géphez, megtekintheti az egyes hálózati adapterek hatékony útvonalait. Mivel az egyes hálózati adapterek különböző alhálózatokban lehetnek, minden hálózati adapter különböző hatékony útvonalakkal rendelkezhet. Ha továbbra is kommunikációs problémája van, tekintse meg a [további diagnózist](#additional-diagnosis) és [szempontokat.](#considerations)

Ha nem tudja a hálózati adapter nevét, de tudja annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati csatoló azonosítóit adják vissza:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

A következő példához hasonló kimenetet kap:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

Az előző kimenetben a hálózati csatoló neve *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálás az Azure CLI használatával

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/bash)követett parancsokat, vagy a CLI-t a számítógépről. Ez a cikk az Azure CLI 2.0.32-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor a szükséges `az login` [engedélyekkel](virtual-network-network-interface.md#permissions)rendelkező fiókkal is futtatnia kell és be kell jelentkeznie az Azure-ba.

Az [az hálózati adapter show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)hatékony útvonal-csatornával rendelkező hatékony útvonalait szerezheti be. A következő példa a *myVMNic1* nevű hálózati adapter hatékony útvonalait kapja meg, amely egy *myResourceGroup*nevű erőforráscsoportban található:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

A kimenetben visszaadott információk megértéséhez olvassa el az [Útválasztás áttekintéscímű témakört.](virtual-networks-udr-overview.md) Kimenet csak akkor adja vissza, ha a virtuális gép a futó állapotban van. Ha több hálózati adapter van csatlakoztatva a virtuális géphez, megtekintheti az egyes hálózati adapterek hatékony útvonalait. Mivel az egyes hálózati adapterek különböző alhálózatokban lehetnek, minden hálózati adapter különböző hatékony útvonalakkal rendelkezhet. Ha továbbra is kommunikációs problémája van, tekintse meg a [további diagnózist](#additional-diagnosis) és [szempontokat.](#considerations)

Ha nem tudja a hálózati adapter nevét, de tudja annak a virtuális gépnek a nevét, amelyhez a hálózati adapter csatlakozik, a következő parancsok a virtuális géphez csatlakoztatott összes hálózati csatoló azonosítóit adják vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Probléma megoldása

Az útválasztási problémák megoldása általában a következőkből áll:

- Egyéni útvonal hozzáadása az Azure egyik alapértelmezett útvonalának felülbírálásához. További információ az [egyéni útvonal hozzáadásáról.](manage-route-table.md#create-a-route)
- Olyan egyéni útvonal módosítása vagy eltávolítása, amely nem kívánt helyre történő útválasztást okozhat. További információ az egyéni útvonal [módosításáról](manage-route-table.md#change-a-route) és [törléséről.](manage-route-table.md#delete-a-route)
- Győződj meg arról, hogy a definiált egyéni útvonalakat tartalmazó útvonaltábla ahhoz az alhálózathoz van társítva, amelyben a hálózati illesztő található. Megtudhatja, hogy [miként társíthat útvonaltáblát alhálózathoz.](manage-route-table.md#associate-a-route-table-to-a-subnet)
- Annak biztosítása, hogy az olyan eszközök, mint az Azure VPN-átjáró vagy a telepített hálózati virtuális eszközök működőképesek legyenek. A Network Watcher [VPN-diagnosztikai](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) funkcióval megállapíthatja az Azure VPN-átjáróval kapcsolatos problémákat.

Ha továbbra is kommunikációs problémái vannak, olvassa el [a Szempontok](#considerations) és további diagnózis című témakört.

## <a name="considerations"></a>Megfontolandó szempontok

A kommunikációs problémák elhárításakor vegye figyelembe az alábbi szempontokat:

- Az útválasztás a definiált útvonalak, a border gateway protokoll (BGP) és a rendszerútvonalak közötti leghosszabb előtag-egyezésen (LPM) alapul. Ha egynél több, azonos LPM-egyezéssel rendelkező útvonal van, akkor a program az Útvonal eredete alapján választ ki egy útvonalat az [Útválasztás áttekintése című témakörben](virtual-networks-udr-overview.md#how-azure-selects-a-route)felsorolt sorrendben. Hatékony útvonalak esetén csak olyan hatékony útvonalakat láthat, amelyek az összes rendelkezésre álló útvonal alapján lpm-egyezést jelentenek. Az útvonalak hálózati adapterek kiértékelésének megtekintésével sokkal könnyebb en a virtuális gép kommunikációját befolyásoló konkrét útvonalak hibaelhárítása.
- Ha egyéni útvonalakat adott meg egy hálózati virtuális berendezéshez (NVA), *amelynek a virtuális berendezés* a következő ugrástípus, győződjön meg arról, hogy az IP-továbbítás engedélyezve van a forgalmat fogadó NVA-n, vagy a csomagok eldobása. További információ a [hálózati adapter EK-továbbításának engedélyezéséről.](virtual-network-network-interface.md#enable-or-disable-ip-forwarding) Ezenkívül az operációs rendszernek vagy az NVA-n belüli alkalmazásnak képesnek kell lennie a hálózati forgalom továbbítására, és erre konfigurálhatónak.
- Ha létrehozott egy útvonalat a 0.0.0/0, az összes kimenő internetes forgalom a megadott következő ugrás, például egy NVA vagy VPN-átjáró. Egy ilyen útvonal létrehozását gyakran kényszerített bújtatásnak nevezik. Távoli kapcsolatok az RDP vagy SSH protokollok az internetről a virtuális gép nem működik ezzel az útvonallal, attól függően, hogy a következő ugrás kezeli a forgalmat. A kényszerített bújtatás engedélyezhető:
    - A helyek közötti VPN használatakor hozzon létre egy útvonalat a *vpn-átjáró*következő ugrástípusával. További információ [a kényszerített bújtatás konfigurálásáról.](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - Ha egy 0.0.0.0/0 (alapértelmezett útvonal) a BGP-n keresztül érhető el egy virtuális hálózati átjárón keresztül, ha egy hely közötti VPN-t vagy ExpressRoute-áramkört használ. További információ a BGP helyek közötti [VPN-ben](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [ExpressRoute-on](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)való használatáról.
- Ahhoz, hogy a virtuális hálózati társviszony-létesítési forgalom megfelelően működjön, léteznie kell egy rendszerútvonalnak a következő ugrástípusú *Virtuális hálózati társviszony-létesítési* típussal a társviszony-létesítési előtagtartományához. Ha ilyen útvonal nem létezik, és a virtuális hálózati társviszony-létesítési kapcsolat **csatlakoztatva**van:
    - Várjon néhány másodpercet, majd próbálkozzon újra. Ha újonnan létrehozott társviszony-létesítési kapcsolatról van szó, időnként hosszabb időt vesz igénybe az útvonalak propagálása az alhálózat összes hálózati csatolójára. Ha többet szeretne megtudni a virtuális hálózati társviszony-létesítésről, olvassa el a [Virtuális hálózati társviszony-létesítés áttekintése](virtual-network-peering-overview.md) és [a virtuális hálózati társviszony-létesítés kezelése című témakört.](virtual-network-manage-peering.md)
    - A hálózati biztonsági csoport szabályai hatással lehetnek a kommunikációra. További információ: [A virtuális gépek hálózati forgalomszűrőjének diagnosztizálása](diagnose-network-traffic-filter-problem.md)című témakörben talál.
- Bár az Azure hozzárendeli az alapértelmezett útvonalakat az egyes Azure-hálózati adapterek, ha több hálózati adapter ek a virtuális géphez, csak az elsődleges hálózati adapter van rendelve egy alapértelmezett útvonal (0.0.0/0), vagy átjáró, a virtuális gép operációs rendszer. Ismerje meg, hogyan hozhat létre alapértelmezett útvonalat a [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuális géphez csatlakoztatott másodlagos hálózati adapterekhez. További információ az [elsődleges és másodlagos hálózati adapterekről.](virtual-network-network-interface-vm.md#constraints)

## <a name="additional-diagnosis"></a>További diagnózis

* Ha egy gyors tesztet futtathat egy adott helyre irányuló forgalom következő ugrástípusának meghatározásához, használja az Azure Network Watcher [következő ugrási](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) képességét. A következő ugrás megmutatja, hogy mi a következő ugrástípus egy adott helyre irányuló forgalomhoz.
* Ha nincsenek olyan útvonalak, amelyek a virtuális gép hálózati kommunikációjának sikertelensítését okozzák, a problémát a virtuális gép operációs rendszerén belül futó tűzfalszoftver okozhatja
* Ha egy VPN-átjárón vagy nva-n keresztül kényszeríti a helyszíni eszközre irányuló [bújtatási forgalmat,](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) előfordulhat, hogy nem tud csatlakozni egy virtuális géphez az internetről, attól függően, hogy hogyan konfigurálta az eszközök útválasztását. Győződjön meg arról, hogy az eszközhöz konfigurált útválasztás a forgalmat a virtuális gép nyilvános vagy privát IP-címére irányítja.
* A Hálózatfigyelő [kapcsolathibaelhárítási](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) képességével meghatározhatja a kimenő kommunikációs problémák útválasztását, szűrését és az operációs rendszeren belül okozott okokat.

## <a name="next-steps"></a>További lépések

- Ismerje meg az útvonaltábla és az útvonalak összes feladatát, tulajdonságát [és beállítását.](manage-route-table.md)
- Ismerje meg az összes [következő ugrástípust, rendszerútvonalakat, valamint azt, hogy az Azure hogyan választ útvonalat.](virtual-networks-udr-overview.md)
