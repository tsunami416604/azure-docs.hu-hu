---
title: Azure-útválasztási táblázat létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan hozhat létre, módosíthat vagy törölhet útválasztási táblázatot.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356654"
---
# <a name="create-change-or-delete-a-route-table"></a>Útválasztási táblázat létrehozása, módosítása vagy törlése

Az Azure automatikusan irányítja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útválasztási táblázatot. Ha még nem ismeri a virtuális hálózatok útválasztását, többet is megtudhat az [útválasztási áttekintésben](virtual-networks-udr-overview.md) vagy egy [oktatóanyag](tutorial-create-route-table-portal.md)elvégzésével.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

* Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).<br>
* Ha a portált használja, nyissa meg https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.<br>
* Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta a fiókjával való használathoz. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.<br>
* Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, a `az login` futtatásával is létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyek](#permissions)között felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az Azure-helyek és-előfizetések által létrehozható útválasztási táblák száma korlátozva van. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A portál bal felső sarkában válassza az **+ erőforrás létrehozása**lehetőséget.
1. Válassza a **hálózatkezelés**, majd az **útválasztási tábla**lehetőséget.
1. Adja meg az útválasztási táblázat **nevét** , válassza ki az **előfizetést**, hozzon létre egy új **erőforráscsoportot**, vagy válasszon ki egy meglévő erőforráscsoportot, válasszon ki egy **helyet**, majd kattintson a **Létrehozás**gombra. Ha azt tervezi, hogy az útválasztási táblázatot egy olyan virtuális hálózat alhálózatához rendeli hozzá, amely VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz, és letiltja a **virtuális hálózati átjáró útvonalának propagálását**, a helyszíni útvonalak nem lesznek propagálva az alhálózat hálózati adapterei között.

### <a name="create-route-table---commands"></a>Útválasztási táblázat létrehozása – parancsok

* Azure CLI: [az Network Route-Table Create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Útválasztási táblák megtekintése

A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki. Megjelenik az előfizetésben található útválasztási táblák.

### <a name="view-route-table---commands"></a>Útválasztási táblázat megtekintése – parancsok

* Azure CLI: [az Network Route-Table List](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Útválasztási táblázat részleteinek megtekintése

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki az útválasztási táblázatot azon a listán, amelyben meg szeretné tekinteni a részleteit. A **Beállítások**területen megtekintheti az útválasztási táblázatban szereplő **útvonalakat** és azon **alhálózatokat** , amelyekhez az útválasztási tábla hozzá van rendelve.
1. Az általános Azure-beállításokkal kapcsolatos további tudnivalókért tekintse meg a következő információkat:

    * [Tevékenységnapló](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)<br>
    * [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Zárak](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automation-parancsfájl](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Útválasztási táblázat részleteinek megtekintése – parancsok

* Azure CLI: [az Network Route-Table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Útválasztási táblázat módosítása

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki a módosítani kívánt útválasztási táblázatot. A leggyakoribb változások az útvonalak [hozzáadása](#create-a-route) vagy [eltávolítása](#delete-a-route) , illetve az útválasztási táblák [társítása](#associate-a-route-table-to-a-subnet) az alhálózatokról [, illetve az](#dissociate-a-route-table-from-a-subnet) útválasztási táblák leválasztása.

### <a name="change-a-route-table---commands"></a>Útválasztási táblázat módosítása – parancsok

* Azure CLI: [az Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* PowerShell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Az alhálózat nulla vagy egy útválasztási táblát is társíthat hozzá. Az útválasztási táblázatok nulla vagy több alhálózathoz is társíthatók. Mivel az útválasztási táblák nincsenek virtuális hálózatokhoz társítva, társítania kell egy útválasztási táblázatot minden olyan alhálózathoz, amelyhez hozzá kívánja rendelni az útválasztási táblázatot. Ha a virtuális hálózat egy Azure-beli virtuális hálózati átjáróhoz (ExpressRoute vagy VPN) van csatlakoztatva, az alhálózatot elhagyó összes forgalom irányítása az útválasztási táblák, az [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default)és a helyszíni hálózatról propagált útvonalak alapján létrehozott útvonalakon történik. Csak olyan alhálózatokhoz rendelhet útválasztási táblázatot, amelyek ugyanabban az Azure-helyen és-előfizetésben találhatók, mint az útválasztási táblázat.

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt az alhálózatot tartalmazó listából a virtuális hálózatot, amelyhez hozzá szeretné rendelni az útválasztási táblázatot.
1. Válassza ki az **alhálózatok** elemet a **Beállítások**területen.
1. Válassza ki azt az alhálózatot, amelyhez az útválasztási táblázatot hozzá szeretné rendelni.
1. Válassza az **útválasztási táblázat**lehetőséget, válassza ki az alhálózathoz társítandó útválasztási táblázatot, majd válassza a **Mentés**lehetőséget.

Ha a virtuális hálózat egy Azure VPN-átjáróhoz csatlakozik, ne társítson útválasztási táblázatot olyan [átjáróalhálózathoz](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub), amely 0.0.0.0/0 célú útvonalat tartalmaz. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. További információ a 0.0.0.0/0 útvonalon való használatáról: [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Útválasztási táblázat hozzárendelése – parancsok

* Azure CLI: [az Network vnet subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útválasztási táblázat leválasztása egy alhálózatból

Amikor alhálózatból választ ki egy útválasztási táblázatot, az Azure az [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default)alapján irányítja a forgalmat.

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt a virtuális hálózatot, amely tartalmazza azt az alhálózatot, amelyből el szeretné választani az útválasztási táblázatot.
1. Válassza ki az **alhálózatok** elemet a **Beállítások**területen.
1. Válassza ki azt az alhálózatot, amelyből el szeretné választani az útválasztási táblázatot.
1. Válassza az **útválasztási tábla**lehetőséget, válassza a **nincs**, majd a **Mentés**lehetőséget.

### <a name="dissociate-a-route-table---commands"></a>Útválasztási táblázat leválasztása – parancsok

* Azure CLI: [az Network vnet subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése

Ha egy útválasztási tábla bármely alhálózathoz van társítva, nem törölhető. [A törlés](#dissociate-a-route-table-from-a-subnet) megkísérlése előtt szüntesse meg az összes alhálózatból származó útválasztási táblázatot.

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza a **...** lehetőséget a törölni kívánt útválasztási táblázat jobb oldalán.
1. Válassza a **Törlés**lehetőséget, majd válassza az **Igen**lehetőséget.

### <a name="delete-a-route-table---commands"></a>Útválasztási táblázat törlése – parancsok

* Azure CLI: [az Network Route-Table delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Útvonal létrehozása

Az egyes útválasztási táblákhoz tartozó útvonalak száma az Azure-ban és az előfizetésben is létrehozható. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt a listából az útválasztási táblázatot, amelyhez hozzá kíván adni egy útvonalat.
1. Válassza az **útvonalak**lehetőséget a **Beállítások**területen.
1. Válassza a **+ Hozzáadás** lehetőséget.
1. Adja meg az útvonal egyedi **nevét** az útválasztási táblázatban.
1. Adja meg azt a CIDR-jelölést, amelybe át szeretné irányítani **a forgalmat**. Az előtag nem duplikálható egynél több útvonalon az útválasztási táblában, bár az előtag egy másik előtagjan belül is lehet. Ha például 10.0.0.0/16 előtagot adott meg egy útvonalon, akkor továbbra is definiálhat egy másik útvonalat a 10.0.0.0/24-címek előtagjával. Az Azure a leghosszabb előtag-egyezés alapján kiválasztja a forgalom útvonalát. Ha többet szeretne megtudni arról, hogy az Azure hogyan válassza az útvonalakat, tekintse meg az [Útválasztás áttekintését](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Válasszon egy **következő ugrási típust**. Az összes következő ugrás típusának részletes ismertetését lásd: az [Útválasztás áttekintése](virtual-networks-udr-overview.md).
1. Adjon meg egy IP-címet a **következő ugrási címhez**. Csak akkor adhat meg egy címeket, ha a **következő ugrási típushoz**a *virtuális berendezést* választotta.
1. Kattintson az **OK** gombra.

### <a name="create-a-route---commands"></a>Útvonal-parancsok létrehozása

* Azure CLI: [az Network Route-Table Route Create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Útvonalak megtekintése

Az útválasztási táblázat nulla vagy több útvonalat tartalmaz. Ha többet szeretne megtudni az útvonalak megtekintésekor felsorolt információkkal kapcsolatban, tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört.

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt a listából az útválasztási táblázatot, amelynek az útvonalait meg szeretné tekinteni.
1. Válassza az **útvonalak** elemet a **Beállítások**területen.

### <a name="view-routes---commands"></a>Útvonalak megtekintése – parancsok

* Azure CLI: [az Network Route-Table Route List](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt az útválasztási táblázatot, amelyre vonatkozóan meg szeretné tekinteni a útvonal részleteit.
1. Válassza az **útvonalak**lehetőséget.
1. Válassza ki azt az útvonalat, amelynek adatait meg szeretné tekinteni.

### <a name="view-details-of-a-route---commands"></a>Route-parancsok részleteinek megtekintése

* Azure CLI: [az Network Route-Table Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Útvonal módosítása

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt az útválasztási táblázatot, amelynek a útvonalát módosítani kívánja.
1. Válassza az **útvonalak**lehetőséget.
1. Válassza ki a módosítani kívánt útvonalat.
1. Módosítsa a meglévő beállításokat az új beállításokra, majd válassza a **Mentés**lehetőséget.

### <a name="change-a-route---commands"></a>Route-parancsok módosítása

* Azure CLI: [az Network Route-Table Route Update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Útvonal törlése

1. A portál felső részén található keresőmezőbe írja be a következőt: *Route Tables* in the Search Box. Ha az **útválasztási táblák** megjelennek a keresési eredmények között, válassza ki.
1. Válassza ki azt az útválasztási táblázatot, amelynek útvonalát törölni kívánja.
1. Válassza az **útvonalak**lehetőséget.
1. Az útvonalak listájából válassza a **...** lehetőséget a törölni kívánt útvonal jobb oldalán.
1. Válassza a **Törlés**lehetőséget, majd válassza az **Igen**lehetőséget.

### <a name="delete-a-route---commands"></a>Útvonal törlése – parancsok

* Azure CLI: [az Network Route-Table Route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

A virtuális géphez csatlakoztatott összes hálózati adapter érvényes útvonala a létrehozott útválasztási táblák kombinációja, az Azure alapértelmezett útvonalai, valamint a BGP-n keresztül egy Azure virtuális hálózati átjárón keresztül propagált útvonalak. A hálózati adapterek hatékony útvonalának megismerése az útválasztási problémák elhárításakor hasznos. Megtekintheti a futó virtuális géphez csatlakoztatott bármely hálózati adapter érvényes útvonalait.

1. A portál felső részén található Keresés mezőben adja meg annak a virtuális gépnek a nevét, amelynek a hatályos útvonalait meg szeretné tekinteni. Ha nem ismeri a virtuális gép nevét, a keresőmezőbe írja be a *virtuális gépek* kifejezést. Ha a **virtuális gépek** megjelennek a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
1. Válassza a **hálózatkezelés** lehetőséget a **Beállítások**területen.
1. Válassza ki a hálózati adapter nevét.
1. Válassza ki a **hatályos útvonalakat** a **támogatás + hibaelhárítás**területen.
1. Tekintse át az érvényes útvonalak listáját annak megállapításához, hogy a megfelelő útvonal létezik-e, ahová a forgalmat át szeretné irányítani. További információk az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakörben látható következő ugrási típusokról.

### <a name="view-effective-routes---commands"></a>Érvényes útvonalak megtekintése – parancsok

* Azure CLI: [az Network NIC show-effektív-Route-Table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Két végpont közötti útválasztás ellenőrzése

Megadhatja a következő ugrás típusát egy virtuális gép és egy másik Azure-erőforrás, egy helyszíni erőforrás vagy egy internetes erőforrás IP-címe között. Az útválasztási problémák elhárítása érdekében az Azure útválasztásának meghatározása hasznos. A feladat végrehajtásához rendelkeznie kell egy meglévő Network Watcher. Ha nem rendelkezik meglévő Network Watcher, hozzon létre egyet a [Network Watcher példány létrehozása](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben leírt lépések végrehajtásával.

1. A portál felső részén található Keresés mezőbe írja be a *Network Watcher* kifejezést a keresőmezőbe. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
1. Válassza a **következő ugrás** lehetőséget a **hálózati diagnosztikai eszközök**területen.
1. Válassza ki az **előfizetését** és annak a forrásként szolgáló virtuális gépnek az **erőforrás-csoportját** , amelyről az útválasztást érvényesíteni szeretné.
1. Válassza ki a virtuális **gépet, a**virtuális géphez csatolt **hálózati adaptert** , valamint a hálózati adapterhez hozzárendelt **forrás IP-címet** , amelyről az útválasztást érvényesíteni szeretné.
1. Adja meg azt a **cél IP-címet** , amelyre az útválasztást érvényesíteni szeretné.
1. Válassza a **következő ugrás**lehetőséget.
1. Rövid várakozás után a rendszer adatokat ad vissza, amely megadja a következő ugrás típusát és a forgalmat átirányító útvonal AZONOSÍTÓját. További információ az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakörben látható következő ugrási típusokról.

### <a name="validate-routing-between-two-endpoints---commands"></a>Útválasztás ellenőrzése két végpont között – parancsok

* Azure CLI: [az Network Watcher show-Next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Engedélyek

A táblák és útvonalak útválasztási feladatainak elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Műveletek                                                          |   Name (Név)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/READ                              |   Útválasztási táblázat beolvasása                                    |
| Microsoft. Network/routeTables/Write                             |   Útválasztási táblázat létrehozása vagy frissítése                        |
| Microsoft.Network/routeTables/delete                            |   Útválasztási táblázat törlése                                  |
| Microsoft. Network/routeTables/csatlakozás/művelet                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft. Network/routeTables/Routes/READ                       |   Útvonal beolvasása                                          |
| Microsoft. Network/routeTables/Routes/Write                      |   Útvonal létrehozása vagy frissítése                              |
| Microsoft.Network/routeTables/routes/delete                     |   Útvonal törlése                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   A hálózati adapter érvényes útválasztási táblázatának beolvasása |
| Microsoft.Network/networkWatchers/nextHop/action                |   Egy virtuális gép következő ugrásának beolvasása                           |

## <a name="next-steps"></a>Következő lépések

* Útválasztási táblázat létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) minta parancsfájljaival vagy az Azure [Resource Manager-sablonok](template-samples.md) használatával<br>
* [Azure](policy-samples.md) -szabályzat létrehozása és alkalmazása virtuális hálózatokhoz
