---
title: Létrehozása, módosítása vagy törlése az Azure útválasztási táblázatban
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan létrehozása, módosítása vagy törlése egy útválasztási táblázatot.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: ae4f3c9f5aa9fecf997c3e367f9ca941edd40c1e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434087"
---
# <a name="create-change-or-delete-a-route-table"></a>Létrehozása, módosítása vagy egy útvonaltábla törlése

Az Azure automatikusan irányítja az Azure az alhálózatok, virtuális hálózatok közötti adatforgalom és a helyszíni hálózatokkal. Ha szeretné módosítani az Azure alapértelmezett útválasztás, hogy hozzon létre egy útválasztási táblázatot megteheti. Ha most ismerkedik az Útválasztás a virtuális hálózatok, többet is megtudhat a kapcsolatos a [Útválasztás áttekintése](virtual-networks-udr-overview.md) vagy; Ehhez hajtsa végre egy [oktatóanyag](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Létrehozhat egy Azure-helyen és az előfizetés hány útvonaltábláihoz korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. A portál bal felső sarkában válassza **+ erőforrás létrehozása**.
2. Válassza ki **hálózatkezelés**, majd **útvonaltábla**.
3. Adjon meg egy **neve** útvonaltáblához, válassza ki a **előfizetés**, hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő erőforráscsoportot, válassza ki egy **helye** , majd **létrehozás**. Ha azt tervezi, hogy társít egy alhálózathoz, virtuális hálózatban, amely a helyszíni hálózathoz egy VPN-átjárón keresztül csatlakozik, és letiltja a **BGP-útvonalpropagálás**, a helyszíni útvonalakat nem vonatkoznak az a a hálózati adapterek.

**Parancsok**

- Az Azure CLI: [az network route-table létrehozása](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Útvonaltáblák megtekintése

Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt. Az útválasztási táblázatokat, amelyek szerepelnek az előfizetésében szerepelnek.

**Parancsok**

- Az Azure CLI: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Útválasztási táblázat részleteinek megtekintése

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki az útvonaltáblában a listában a részleteinek megtekintéséhez. A **beállítások**, megtekintheti a **útvonalak** az útvonaltáblában lévő és a **alhálózatok** az útvonaltábla társítva.
3. Gyakori Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő információkat:
    *   [Tevékenységnapló](../azure-monitor/platform/activity-logs-overview.md)
    *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zárolások](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation-szkript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Útválasztási táblázat módosítása

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a módosítani kívánt útválasztási táblázatot. A leggyakoribb változások [hozzáadása](#create-a-route) vagy [eltávolítása](#delete-a-route) útvonalak és [társítása](#associate-a-route-table-to-a-subnet) útválasztási táblázatok, vagy [való társításának megszüntetése](#dissociate-a-route-table-from-a-subnet) útválasztási táblázatok a alhálózatok.

**Parancsok**

- Az Azure CLI: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Egy alhálózat rendelkezhet, nulla vagy egy útvonaltábla társítható. Egy útválasztási táblázatot lehet nulla vagy egynél több alhálózat van társítva. Az útvonaltáblák nem lettek társítva a virtuális hálózatok, mivel társítania kell egy útválasztási táblázatot az egyes alhálózatokon azt szeretné, hogy a társított útvonaltáblába. Minden, az alhálózatot elhagyó forgalom útválasztási táblázatokat, belül létrehozott útvonalak alapján továbbítja [alapértelmezett útvonalakat](virtual-networks-udr-overview.md#default), és útvonalak propagálni egy a helyszíni hálózathoz, ha a virtuális hálózathoz csatlakoztatva van egy Azure virtuális hálózati átjáró () ExpressRoute vagy VPN-, VPN gateway a BGP használatakor). Csak egy útválasztási táblázatot a megegyező Azure-helyen és előfizetésen az útvonaltáblában lévő virtuális hálózatok alhálózataihoz társíthatja.

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a virtuális hálózat, amely tartalmazza az alhálózat egy útválasztási táblázatot annak társítani szeretné a listában.
3. Válassza ki **alhálózatok** alatt **beállítások**.
4. Válassza ki az alhálózatot, hozzárendeli az útvonaltáblát.
5. Válassza ki **útvonaltábla**, válassza ki az útválasztási táblázatot az alhálózathoz társítja, majd válassza a kívánt **mentése**.

Ha a virtuális hálózat egy Azure VPN-átjáróhoz csatlakozik, ne társítson útválasztási táblázatot olyan [átjáróalhálózathoz](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub), amely 0.0.0.0/0 célú útvonalat tartalmaz. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. Egy útvonalat a 0.0.0.0/0 használatával kapcsolatos további információkért lásd: [virtuális hálózat forgalmának útválasztása](virtual-networks-udr-overview.md#default-route).

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat alhálózati update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Egy alhálózatról egy útválasztási táblázatot társításának megszüntetése

Egy alhálózatról egy útválasztási táblázatot, leválasztja, amikor Azure irányítja-e a forgalom alapján a [alapértelmezett útvonalakat](virtual-networks-udr-overview.md#default).

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki a virtuális hálózat, amely tartalmazza az leválasztja egy útválasztási táblázatot az alhálózathoz.
3. Válassza ki **alhálózatok** alatt **beállítások**.
4. Válassza ki az leválasztja az útválasztási táblázatot az alhálózathoz.
5. Válassza ki **útvonaltábla**, jelölje be **nincs**, majd **mentése**.

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat alhálózati update](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése

Ha egy útválasztási táblázatot is tartozik, az esetleges olyan alhálózatokra, nem lehet törölni. [Leválasztja](#dissociate-a-route-table-from-a-subnet) egy útválasztási táblázatot az összes alhálózatot, mielőtt megkísérelné törölni.

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki **...**  törli az útvonaltábla jobb-oldalán.
3. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az network route-table törlése](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Remove-AzureRmRouteTable](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermroutetable?view=azurermps-6.8.1) 

## <a name="create-a-route"></a>Útvonal létrehozása

Hány útvonalak útvonaltáblánkénti hozhat létre egy Azure-helyen és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki az útválasztási táblázatot, amely szeretne hozzáadni egy útvonalat a listából.
3. Válassza ki **útvonalak**alatt **beállítások**.
4. Válassza a **+ Hozzáadás** lehetőséget.
5. Adjon meg egy egyedi **neve** az útvonal route a táblán belül.
6. Adja meg a **címelőtag**, a CIDR-jelölésrendszerben, irányíthatja a forgalmat a kívánt. Az előtag nem duplikálható az útvonaltáblában lévő egynél több útvonal ellenére, hogy az előtag lehet egy másik előtag belül. Például ha egy útvonal előtagjaként definiált 10.0.0.0/16, is meghatározhatja egy másik útvonal a 10.0.0.0/24 címelőtaggal. Az Azure útvonalválasztásának a forgalmat a leghosszabb előtag-megfeleltetés alapján. Hogyan választja ki a Azure útvonalakkal kapcsolatos további információkért lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Válassza ki a **következő ugrás típusa**. Az összes következő ugrási típusokat részletes ismertetését lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).
8. Adja meg az IP-cím **következő ugrási cím**. Csak akkor adhat meg címet, ha a kiválasztott *virtuális berendezés* a **következő ugrás típusa**.
9. Kattintson az **OK** gombra.

**Parancsok**

- Az Azure CLI: [az network route-table route létrehozása](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Útvonalak megtekintése

Útválasztási táblázat nulla vagy egynél több útvonalakat tartalmazza. Útvonalak megtekintésekor felsorolt információkkal kapcsolatos további tudnivalókért lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki az útválasztási táblázatot, hogy meg szeretné tekinteni az útvonalakat a listából.
3. Válassza ki **útvonalak** alatt **beállítások**.

**Parancsok**

- Az Azure CLI: [az network route-table route listája](/cli/azure/network/route-table/route?view=azure-cli-latest)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Válassza ki az útválasztási táblázatot egy útvonalat a részletek megtekintéséhez.
3. Válassza ki **útvonalak**.
4. Válassza ki az útvonal részleteinek megtekintéséhez.

**Parancsok**

- Az Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Egy útvonalat módosítása

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Jelölje be az útvonaltábla módosítja egy útvonalat.
3. Válassza ki **útvonalak**.
4. Válassza ki a módosítani kívánt útvonalat.
5. Módosítsa a meglévő beállításokat új beállításait, majd válassza ki **mentése**.

**Parancsok**

- Az Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Útvonal törlése

1. Írja be a keresőmezőbe, a portál tetején, *útválasztási táblázatok* kifejezést a keresőmezőbe. Amikor **útválasztási táblázatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. Jelölje be az útvonaltábla törli egy útvonalat.
3. Válassza ki **útvonalak**.
4. Útvonalakat listáról válassza ki **...**  a jobb oldalon – az útvonal törli.
5. Válassza ki **törlése**, majd **Igen**.

**Parancsok**

- Az Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

Az érvényes útvonalak mindegyik hálózati interfész egy virtuális géphez csatolt létrehozott útvonaltáblák kombinációját, az Azure alapértelmezett útvonalakat és egy Azure virtuális hálózati átjárón keresztül BGP-n keresztül a helyszíni hálózatok propagálni hozzáférésvezérlési. Hálózati adapter érvényes útvonalaihoz ismertetése akkor hasznos, ha az útválasztási problémák elhárítása. Minden olyan futó virtuális géphez csatolt hálózati adapter érvényes útvonalaihoz tekintheti meg.

1. A keresőmezőbe, a portál tetején adja meg az érvényes útvonalak a megtekinteni kívánt virtuális gép nevét. Ha nem ismeri a virtuális gép nevét, adja meg a *virtuális gépek* kifejezést a keresőmezőbe. Amikor **virtuális gépek** jelennek meg a keresési eredmények között, válassza ki, és válasszon ki egy virtuális gépet a listából.
2. Válassza ki **hálózatkezelés** alatt **beállítások**.
3. Válassza ki a hálózati adapter nevét.
4. Válassza ki **érvényes útvonalak** alatt **támogatás + hibaelhárítás**.
5. Tekintse át a meghatározásához, hogy létezik-e a megfelelő útvonalat, ahol szeretné irányítani a forgalmat az érvényes útvonalak. További információ jelenik meg ebben a listában szereplő következő ugrástípusok [Útválasztás áttekintése](virtual-networks-udr-overview.md).

**Parancsok**

- Az Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 

## <a name="validate-routing-between-two-endpoints"></a>Ellenőrizze a két végpontok közötti útválasztás

Megadhatja, hogy a következő ugrás típusa között a virtuális gép és a egy másik Azure-erőforrás, egy helyszíni erőforráshoz, vagy internetes erőforrás IP-címét. Meghatározó Azure útválasztás akkor hasznos, ha az útválasztási problémák elhárítása. Ez a feladat végrehajtásához rendelkeznie kell egy meglévő hálózati figyelő. Ha egy meglévő a Network Watcher nincs, hozzon létre egyet a lépéseket követve [Network Watcher-példány létrehozása](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Írja be a keresőmezőbe, a portál tetején, *a network watcher* kifejezést a keresőmezőbe. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
2. Válassza ki **a következő Ugrás** alatt **hálózati diagnosztikai eszközök**.
3. Válassza ki a **előfizetés** és a **erőforráscsoport** a forrás virtuális gép szeretné érvényesíteni az útválasztást.
4. Válassza ki a **virtuális gép**, **hálózati adapter** a virtuális géphez csatolt és **forrás IP-címe** rendelve az érvényesíteni kívánt hálózati adapter útválasztás az.
5. Adja meg a **cél IP-címe** , amelyet szeretne érvényesíteni az Útválasztás.
6. Válassza ki **a következő Ugrás**.
7. Rövid várakozás után információt ad vissza, amely arról tájékoztatja, akkor a következő ugrás típusa és az azonosítója, amely irányítja a forgalmat útvonal. További tudnivalók a következő ugrási típusokat láthatja, amelyeket a visszaadott [Útválasztás áttekintése](virtual-networks-udr-overview.md).

**Parancsok**

- Az Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Engedélyek

Útválasztási táblázatokat és útvonalak feladatok végrehajtására, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy olyan [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                                          |   Name (Név)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Olvassa el az útválasztási táblázat                                    |
| Microsoft.Network/routeTables/write                             |   Hozzon létre vagy egy útvonaltábla frissítése                        |
| Microsoft.Network/routeTables/delete                            |   Útválasztási táblázat törlése                                  |
| Microsoft.Network/routeTables/join/action                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft.Network/routeTables/routes/read                       |   Olvassa el az útvonalat                                          |
| Microsoft.Network/routeTables/routes/write                      |   Egy útvonal létrehozása vagy módosítása                              |
| Microsoft.Network/routeTables/routes/delete                     |   Útvonal törlése                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Az effektív útvonaltábla első hálózati adapter |
| Microsoft.Network/networkWatchers/nextHop/action                |   A következő ugrás lekérdezi egy virtuális gépről                           |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy útválasztási táblázatban az [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok
