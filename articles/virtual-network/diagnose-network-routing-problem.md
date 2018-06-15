---
title: Egy Azure virtuális gép útválasztási probléma diagnosztizálása |} Microsoft Docs
description: 'Útmutató: virtuális gépek hatékony útvonalak megtekintésével egy virtuális gép útválasztási probléma diagnosztizálása érdekében.'
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
ms.openlocfilehash: 07352a5d7c8b465440efab17c654979662a95f8e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702373"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>A virtuális gép útválasztási probléma diagnosztizálása

Ebből a cikkből megismerheti, hogyan vannak érvényben a hálózati adaptert egy virtuális gép (VM) útvonalakat megtekintésével útválasztási probléma diagnosztizálása érdekében. Több alapértelmezett útvonalak minden egyes virtuális hálózati alhálózat az Azure létrehoz. Azure alapértelmezett útvonalak útvonalak a egy útválasztási táblázatot, és majd társítása alhálózathoz útválasztási táblázatot lehet felülbírálni. Az útvonalak hoz létre, Azure alapértelmezett útvonalak és bármely útvonalak propagálása a(z) a helyi hálózaton keresztül az Azure VPN-átjáró (Ha a virtuális hálózat csatlakozik-e a helyi hálózaton) keresztül a border gateway protocol (BGP), a kombinációja a az alhálózat minden hálózati interfészen hatékony útvonalakat. Ha még nem ismeri a virtuális hálózat, a hálózati adapter vagy a útválasztási fogalmakat, lásd: [virtuális hálózat áttekintése](virtual-networks-overview.md), [hálózati illesztő](virtual-network-network-interface.md), és [Útválasztás – áttekintés](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Forgatókönyv

A virtuális gépek csatlakozni, de a kapcsolódás sikertelen. Annak megállapításához, hogy miért nem tud csatlakozni a virtuális gép, megtekintheti a hálózati illesztő a Azure használatával hatékony útvonalak [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), vagy a [Azure CLI](#diagnose-using-azure-cli).

A következő lépések során feltételezzük, hogy egy meglévő virtuális gép a hatékony útvonalait megtekintéséhez. Ha egy meglévő virtuális gép nem rendelkezik, először telepítsen egy [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM ebben a cikkben szereplő feladatok elvégzéséhez. Ebben a cikkben szereplő példák vannak a virtuális gépek nevű *myVM* az egy adott hálózati csatoló nevű *myVMVMNic*. A virtuális gép és a hálózati adapter szerepelnek nevű erőforráscsoport *myResourceGroup*, és a *USA keleti régiója* régió. A lépéseket, hogy megfelelő értékének módosításához a virtuális gép vannak a probléma diagnosztizálásához.

## <a name="diagnose-using-azure-portal"></a>Diagnosztizálja az Azure-portál használatával

1. Jelentkezzen be a Azure [portal](https://portal.azure.com) az Azure-fiókot, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).
2. Az Azure portál felső részén írja be a keresőmezőbe a futó állapotú virtuális gép nevét. Ha a keresési eredmények között megjelenik a virtuális gép nevét, válassza ki azt.
3. Válassza ki **Diagnosztizálás és problémák megoldására**, majd a **a javasolt lépéseket**, jelölje be **hatékony útvonalak** 7, az elem, az alábbi ábrán látható módon:

    ![Hatékony útvonalak megtekintése](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. A hatályos irányítja a hálózati illesztő nevű **myVMVMNic** látható, az alábbi képen látható:

     ![Hatékony útvonalak megtekintése](./media/diagnose-network-routing-problem/effective-routes.png)

    Ha a virtuális Géphez csatlakozik, több hálózati adapterrel, megtekintheti a hálózati csatolóhoz hatékony útvonalait lehetőséget. Mivel mindegyik hálózati interfész lehet egy másik alhálózat, mindegyik hálózati interfész lehet különböző életbelépési útvonalak.

    A példában az előző ábrán látható a felsorolt útvonalak alapértelmezett útvonalak Azure hozza létre az egyes alhálózatokon. A listában legalább ezeket az útvonalakat tartalmaz, de rendelkezhetnek további útvonalakat, attól függően, hogy képességek, előfordulhat, hogy engedélyezte a virtuális hálózat, például az éppen egy másik virtuális hálózathoz nincsenek társviszonyban, vagy a helyszíni hálózat az Azure VPN-átjárón keresztül kapcsolódik. Az útvonalak, és láthatja, hogy a hálózati illesztő útvonalakat kapcsolatos további információkért lásd: [virtuális hálózati forgalmának irányítását a](virtual-networks-udr-overview.md). Ha a lista útvonalak nagy mennyiségű, előfordulhat, hogy ez egyszerűbbé teszi kiválasztásához **letöltése**, letölteni egy .csv fájlt útvonalak listája.

Bár a hatékony útvonalak az előző lépésben a virtuális gép keresztül is megtekinthetők, hatékony útvonalakat is megtekintheti egy:
- **Egyes hálózati adapter**: megtudhatja, hogyan [megtekintheti egy adott hálózati csatoló](virtual-network-network-interface.md#view-network-interface-settings).
- **Az egyes útválasztási táblázatot**: megtudhatja, hogyan [megtekintheti egy útválasztási táblázatot](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnosztizálja a PowerShell használatával

A parancsok futtatása a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy futtassa a PowerShell a számítógépről. Az Azure-felhő rendszerhéj a szabad interaktív rendszerhéjat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha PowerShell a számítógépen futtatja, akkor a *AzureRM* PowerShell-modult, 6.0.1 verzió vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` a számítógépen, a telepített verzió található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtat PowerShell, is futtatásához szükséges `Login-AzureRmAccount` Azure bejelentkezni egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

A hatékony útvonalak lekérése a hálózati illesztő – [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). A következő példa egy adott hálózati csatoló nevű hatékony útvonalak lekérdezi *myVMVMNic*, amely pedig erőforráscsoportban nevű *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

A kimenet a visszakapott információk ismertetése: [Útválasztás – áttekintés](virtual-networks-udr-overview.md). Kimeneti csak akkor ad vissza, ha a virtuális gép futó állapotban van. Ha a virtuális Géphez csatlakozik, több hálózati adapterrel, mindegyik hálózati interfész hatékony útvonalak tekintheti meg. Mivel mindegyik hálózati interfész lehet egy másik alhálózat, mindegyik hálózati interfész lehet különböző életbelépési útvonalak. Ha még nem kommunikációs probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati kapcsolat neve, de tudja a virtuális Gépet, a hálózati adapter csatlakozik a nevét, a következő parancsokat egy virtuális géphez csatolt minden hálózati interfészen azonosítóit adja vissza:

```azurepowershell-interactive
$VM = Get-AzureRmVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Az alábbi példához hasonló kimenetet jelenhet meg:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Az előző kimeneti, a hálózati kapcsolat neve: *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosztizálja az Azure parancssori felület használatával

A parancsok futtatása a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez a cikk igényel az Azure parancssori felület 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` , és jelentkezzen be a Azure egy olyan fiókkal, amely rendelkezik a [szükséges engedélyek](virtual-network-network-interface.md#permissions).

A hatékony útvonalak lekérése a hálózati illesztő – [az hálózati nic megjelenítése-hatályos-útvonal-tábla](/cli/azure/network/nic#az-network-nic-show-effective-route-table). A következő példa egy adott hálózati csatoló nevű hatékony útvonalak lekérdezi *myVMVMNic* nevű erőforráscsoport szerepel *myResourceGroup*:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

A kimenet a visszakapott információk ismertetése: [Útválasztás – áttekintés](virtual-networks-udr-overview.md). Kimeneti csak akkor ad vissza, ha a virtuális gép futó állapotban van. Ha a virtuális Géphez csatlakozik, több hálózati adapterrel, mindegyik hálózati interfész hatékony útvonalak tekintheti meg. Mivel mindegyik hálózati interfész lehet egy másik alhálózat, mindegyik hálózati interfész lehet különböző életbelépési útvonalak. Ha még nem kommunikációs probléma, lásd: [további elemzés céljából](#additional-diagnosis) és [szempontok](#considerations).

Ha nem ismeri a hálózati kapcsolat neve, de tudja a virtuális Gépet, a hálózati adapter csatlakozik a nevét, a következő parancsokat egy virtuális géphez csatolt minden hálózati interfészen azonosítóit adja vissza:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>A probléma megoldásához

Útválasztási problémák megoldása érdekében általában áll:

- Azure alapértelmezett útvonalak felülbírálására egyéni útvonal hozzáadását. Megtudhatja, hogyan [adjon hozzá egy egyéni útvonalat](manage-route-table.md#create-a-route).
- Módosítsa vagy távolítsa el a kívánt helyre útválasztási okozhat egyéni útvonal. Megtudhatja, hogyan [módosítása](manage-route-table.md#change-a-route) vagy [törlése](manage-route-table.md#delete-a-route) egy egyéni útvonalat.
- Győződjön meg arról, hogy az útválasztási táblázatot, amely tartalmazza az összes egyéni útvonalak meghatározta társítva az alhálózatot a hálózati adaptert. Megtudhatja, hogyan [társítson egy útválasztási táblázatot az alhálózathoz](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Annak biztosítására, hogy eszközök, például az Azure VPN gateway vagy a hálózati virtuális készülékek telepítése után futtatható. Használja a [VPN diagnosztika](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) meghatározásához az Azure VPN gateway problémákat hálózati figyelőt képességét.

Ha még nem kommunikációs problémák, lásd: [szempontok](#considerations) és [további elemzés céljából](#additional-dignosis).

## <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következő szempontokat, amikor kommunikációs problémák megoldása:

- Útválasztás alapul leghosszabb előtag egyezés (LPM) útvonalak között, hogy meghatározta, border gateway protocol (BGP), és a rendszer útvonalak. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel van, akkor egy útvonal van jelölve a listában szereplő sorrendben Kiindulás alapján [Útválasztás – áttekintés](virtual-networks-udr-overview.md#how-azure-selects-a-route). Az hatékony útvonalakat hogy hatékony útvonalakat, amelyek az LPM megfeleltetéssel, az összes rendelkezésre álló útvonal alapján. Jelent meg, hogyan útvonalak értékeli ki a rendszer egy adott hálózati csatoló megkönnyíti sokkal elhárítása meghatározott útvonalakat, előfordulhat, hogy mely negatív hatással lehet a virtuális gép érkező kommunikációt.
- Ha meghatározta a hálózati virtuális készülékre (NVA), egyéni útvonalak *virtuális készülék* a következő ugrás típusa, győződjön meg róla, hogy az IP-továbbítás engedélyezve van az NVA, hanem a forgalmat fogadó, vagy a rendszer eldobott csomagok. További információ [engedélyezése egy adott hálózati csatoló IP-továbbítás](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Emellett az operációs rendszer vagy alkalmazás az NVA belül is kell tudni továbbítja a hálózati forgalmat, és ehhez konfigurálható.
- Ha létrehozott egy útvonalat a 0.0.0.0/0, a következő Ugrás megadott, például egy NVA vagy VPN-átjáró minden kimenő internetforgalom van átirányítva. Ilyen útvonal létrehozása gyakran hivatkoznak, a kényszerített bújtatást. Az RDP és az SSH protokoll használatával az internetről a virtuális géphez távoli kapcsolatok nem feltétlenül útvonalhoz, attól függően, hogy miként kezeli a következő ugrás a forgalmat. A kényszerített bújtatás engedélyezhető:
    - Hozzon létre egy útvonalat a következő ugrás típusa, a telephelyek közötti VPN, használatakor *VPN-átjáró*. További információ [konfigurálása a kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Ha egy 0.0.0.0/0 (alapértelmezett útvonalat) van meghirdetett előtag BGP keresztül a virtuális hálózati átjáró használata a telephelyek közötti VPN, vagy ExpressRoute-kapcsolatcsoportot. További információ a BGP egy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering).
- A virtuális hálózati társviszony-létesítési forgalom működéséhez, egy következő ugrás típusa, a rendszer útvonal *Vnetben társviszony-létesítés* léteznie kell a peered virtuális hálózati előtag tartomány. Ha ilyen útvonal nem létezik, és a virtuális hálózati társviszony-létesítési hivatkozás **csatlakoztatva**:
    - Várjon néhány másodpercet, és próbálkozzon újra. Ha ez egy újonnan létrehozott társviszony-létesítési hivatkozást, időnként hosszabb ideig tart egy alhálózatot a hálózati adapterek útvonalakat propagálására. Virtuális hálózati társviszony-létesítés kapcsolatos további információkért lásd: [virtuális hálózati társviszony-létesítési áttekintése](virtual-network-peering-overview.md) és [kezelheti a virtuális hálózati társviszony-létesítés](virtual-network-manage-peering.md).
    - Hálózati biztonsági csoportszabályok kommunikációs lehet, hogy érinti. További információkért lásd: [egy virtuális gép hálózati forgalom szűrő probléma diagnosztizálása](diagnose-network-traffic-filter-problem.md).
- Bár az Azure minden Azure hálózati csatoló rendel alapértelmezett útvonalak, ha a virtuális Géphez csatlakozik, több hálózati adapterrel rendelkezik, csak elsődleges hálózati adapterének hozzá van rendelve, a alapértelmezett útvonalat (0.0.0.0/0), vagy az átjáró, a virtuális gép operációs rendszerében. Megtudhatja, hogyan hozzon létre egy alapértelmezett útvonalat csatolt másodlagos hálózati adapterrel egy [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) vagy [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) virtuális gép. További információ [elsődleges és másodlagos hálózati adapterek](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>További elemzés céljából

* Határozza meg a következő ugrás típusa egy helyre irányuló forgalom a gyors vizsgálat futtatásához használja a [következő Ugrás](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure hálózati figyelőt képességét. Következő ugrás megtudhatja, mi a következő ugrás típusa a megadott helyre irányuló forgalmat az.
* Nem léteznek útvonalak, amely a virtuális gép hálózati kommunikáció sikertelen lesz, ha a hiba oka esetleg az lehet a virtuális gép operációs rendszerben futó tűzfal szoftver
* Ha Ön [kényszerített bújtatás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) forgalom a helyszíni eszközök egy VPN-átjáró vagy NVA, számára meg nem lehet csatlakozni a virtuális gépek az internetről, attól függően, hogy hogyan konfigurálta az eszközök számára útválasztási. Győződjön meg arról, hogy az eszköz konfigurált útválasztási irányítja a forgalmat vagy egy nyilvános vagy privát IP-címet a virtuális gép számára.
* Használja a [kapcsolati hibáinak elhárítása](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kimenő kommunikáció problémák útválasztási, szűrési és az operációs rendszer okok megállapításához hálózati figyelőt képességét.

## <a name="next-steps"></a>További lépések

- Minden feladatok, a tulajdonságok és a beállítások egy [útvonal-tábla és útvonalak](manage-route-table.md).
- További tudnivalók az összes [a következő ugrás típusa, a rendszer útvonalakat és a hogyan Azure egy útvonalat választják ki](virtual-networks-udr-overview.md).
