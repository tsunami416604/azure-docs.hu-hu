---
title: Létrehozása, módosítása vagy törlése az Azure útválasztási táblázatot |} Microsoft Docs
description: Megtudhatja, hogyan létrehozása, módosítása vagy törlése egy útválasztási táblázatot.
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
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 93ecd0264413e0eb719c9d33f0a0b756bcee6552
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726448"
---
# <a name="create-change-or-delete-a-route-table"></a>Létrehozása, módosítása vagy törlése egy útválasztási táblázatot

Azure automatikusan irányítja a forgalmat Azure alhálózatokat, virtuális hálózatok között, és a helyszíni hálózatokhoz. Ha bármely Azure alapértelmezett útválasztási módosítani kívánja, akkor hozzon létre egy útválasztási táblázatot megteheti. Ha most ismerkedik az útválasztást a virtuális hálózatok, további információt a a [Útválasztás – áttekintés](virtual-networks-udr-overview.md) vagy; Ehhez hajtsa végre a [oktatóanyag](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.31 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

Hozzá kell rendelni a fiókot, jelentkezzen be, vagy csatlakozzon az Azure-ba, a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Hány útvonal táblákat hozhat létre egy Azure-beli hely és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Válassza ki a portál bal felső sarkában **+ hozzon létre egy erőforrást**.
2. Válassza ki **hálózati**, majd jelölje be **útvonaltábla**.
3. Adja meg egy **neve** útvonaltábla, válassza ki a **előfizetés**, hozzon létre egy új **erőforráscsoport**, vagy válasszon ki egy meglévő erőforráscsoportot, válassza ki a **helye** , majd jelölje be **létrehozása**. A **tiltsa le a BGP útválasztási propagálás** beállítás megakadályozza, hogy a helyszíni útvonalakat a BGP keresztül jutott el a hálózati illesztők egyetlen alhálózatának sem, amely hozzá van rendelve az útvonaltábla. Ha a virtuális hálózat nincs csatlakoztatva egy Azure-hálózatot átjáró (VPN- vagy ExpressRoute), hagyja a beállítást *letiltott*.

**Parancsok**

- Az Azure CLI: [az hálózati útvonal-táblázat létrehozása](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [új AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Az útvonaltáblák megtekintése

Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt. Az útvonaltáblák az előfizetéshez létező vannak felsorolva.

**Parancsok**

- Az Azure CLI: [az útvonaltábla lista](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Egy útválasztási táblázatot részleteinek megtekintése

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Az útvonaltábla jelölje ki a listában a létrehozott részleteinek megtekintése. A **beállítások**, megtekintheti a **útvonalak** az útválasztási táblázatban és a **alhálózatok** útvonaltábla társítva.
3. Közös Azure beállításaival kapcsolatos további tudnivalókért tekintse meg a következő információkat:
    *   [Tevékenységnapló](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Hozzáférés-vezérlés (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Címkék](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zárolások feloldása](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatizálási parancsfájl](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Parancsok**

- Az Azure CLI: [az hálózati útvonal-táblázat megjelenítése](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Egy útválasztási táblázatot módosítása

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a módosítani kívánt útválasztási táblázatot. A leggyakoribb változások a következők [hozzáadása](#create-a-route) vagy [eltávolítása](#delete-a-route) útvonalak és [társítása](#associate-a-route-table-to-a-subnet) útvonaltáblát, vagy [törléséhez](#dissociate-a-route-table-from-a-subnet) az útvonaltáblát alhálózatok.

**Parancsok**

- Az Azure CLI: [az hálózati útvonal-táblázat frissítése](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Egy alhálózat állhat nulla vagy egy útválasztási táblázatot társítva. Egy útválasztási táblázatot lehet nulla vagy több alhálózatból társítva. Mivel az útvonaltáblák nem társított virtuális hálózatokhoz, társítania kell egy útválasztási táblázatot azt szeretné, hogy az útvonaltáblát társított minden egyes alhálózathoz. Az alhálózat összes forgalomra útvonaltábláit, belül létrehozott útvonalak alapján továbbítja [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default), útvonalak egy helyszíni hálózatból kiterjednek, ha a virtuális hálózathoz van csatlakoztatva egy Azure virtuális hálózati átjáró ( Expressroute-on, vagy VPN-profilok, ha a VPN-átjáró BGP használatával). Csak egy útválasztási táblázatot működő azonos Azure-beli hely és az útválasztási táblázatot előfizetés virtuális hálózatok alhálózatokra is hozzárendelhető.

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Jelölje ki a virtuális hálózat a hozzárendeli egy útválasztási táblázatot az alhálózathoz tartalmazó lista.
3. Válassza ki **alhálózatok** alatt **beállítások**.
4. Jelölje ki az alhálózatot, hozzárendeli az útvonaltábla.
5. Válassza ki **útvonaltábla**, jelölje be az útvonaltábla társítása alhálózathoz, majd válassza a kívánt **mentése**.

Ha a virtuális hálózat az Azure VPN-átjáró csatlakozik, ne társítson egy útválasztási táblázatot, hogy a [átjáróalhálózatot](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) , amely tartalmazza egy útvonalat a 0.0.0.0/0 célját. Így előfordulhat, hogy az átjáró helyes működését. Egy útvonalat a 0.0.0.0/0 használatával kapcsolatos további információkért lásd: [virtuális hálózati forgalmának irányítását a](virtual-networks-udr-overview.md#default-route).

**Parancsok**

- Az Azure CLI: [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>A társítást egy útválasztási táblázatot egy alhálózatból

Ha megszünteti egy útválasztási táblázatot alhálózatból származó, Azure irányítja a forgalmat alapján a [alapértelmezett útvonalak](virtual-networks-udr-overview.md#default).

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a virtuális hálózat, amely tartalmazza a leválasztja egy útválasztási táblázatot az alhálózathoz.
3. Válassza ki **alhálózatok** alatt **beállítások**.
4. Jelölje ki az alhálózatot, az útvonaltábla leválasztja.
5. Válassza ki **útvonaltábla**, jelölje be **nincs**, majd jelölje be **mentése**.

**Parancsok**

- Az Azure CLI: [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Új útvonaltábla törlése

Ha egyetlen alhálózatot társítva egy útválasztási táblázatot, nem lehet törölni. [A társítást](#dissociate-a-route-table-from-a-subnet) egy útválasztási táblázatot az összes alhálózatot az törlése megkísérlése előtt.

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki **...**  jobb oldalán a törölni kívánt útválasztási táblázatot.
3. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati útvonaltábla törlése](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Delete-AzureRmRouteTable](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Útvonal létrehozása

Hány útvonalak útvonaltáblánkénti hozhat létre egy Azure-beli hely és az előfizetés korlátozva van. További részletek: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki az útvonaltábla a listából, amely hozzá szeretné adni egy útvonalat.
3. Válassza ki **útvonalak**a **beállítások**.
4. Válassza a **+ Hozzáadás** lehetőséget.
5. Adjon meg egy egyedi **neve** belül az útvonaltáblát az útvonal.
6. Adja meg a **címelőtag**, a CIDR-jelöléssel irányíthatja a forgalmat a kívánt. Az előtag nem lehet duplikálni útválasztási táblázatot belül egynél több útvonal, bár a előtag lehet belül egy másik előtag. Például ha egy útvonal előtagjaként definiált 10.0.0.0/16, továbbra is definiálhat a 10.0.0.0/24 címelőtagot egy másik útvonalat. Azure egy útvonalat a forgalmat a leghosszabb előtag egyezés alapján választják ki. Hogyan választja ki a Azure útvonalak kapcsolatos további információkért lásd: [Útválasztás – áttekintés](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Válassza ki a **a következő ugrás típusa**. Összes következő ugrás típusa részletes ismertetését lásd: [Útválasztás – áttekintés](virtual-networks-udr-overview.md).
8. Adja meg az IP-címének **következő ugrási cím**. Csak akkor adhat meg egy címet, ha a kiválasztott *virtuális készülék* a **a következő ugrás típusa**.
9. Kattintson az **OK** gombra.

**Parancsok**

- Az Azure CLI: [az hálózati útvonaltábla útvonal létrehozása](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Útvonalak megtekintése

Egy útválasztási táblázatot nulla vagy egynél több útvonalak tartalmazza. Útvonalak megtekintésekor felsorolt információkkal kapcsolatos további tudnivalókért lásd: [Útválasztás – áttekintés](virtual-networks-udr-overview.md).

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Az útvonaltábla válassza a listából, hogy meg szeretné tekinteni az útvonalakat.
3. Válassza ki **útvonalak** alatt **beállítások**.

**Parancsok**

- Az Azure CLI: [az útvonaltábla útvonal lista](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki az útválasztási táblázatot egy útvonal a részletek megtekintéséhez.
3. Válassza ki **útvonalak**.
4. Válassza ki az útvonal részleteinek megtekintéséhez.

**Parancsok**

- Az Azure CLI: [az hálózati útvonaltábla útvonal megjelenítése](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Egy olyan útvonalat módosítása

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a módosítandó útvonal útválasztási táblázatot.
3. Válassza ki **útvonalak**.
4. Válassza ki a módosítani kívánt útvonalat.
5. Módosítsa a meglévő beállítások az új beállítások, majd válassza ki **mentése**.

**Parancsok**

- Az Azure CLI: [az hálózat útvonaltábla útvonal frissítése](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Útvonal törlése

1. Adja meg a keresési mezőbe, a portál felső, *útvonaltáblát* be a keresőmezőbe. Ha **útvonaltáblát** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a törlendő útvonal útválasztási táblázatot.
3. Válassza ki **útvonalak**.
4. Válassza ki a listáról az útvonalak **...**  jobb oldalán a törölni kívánt útvonal.
5. Válassza ki **törlése**, majd jelölje be **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati útvonaltábla útvonal törlése](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Hatékony útvonalak megtekintése

A hatékony útvonalak mindegyik hálózati interfész egy virtuális géphez csatolt a következők: útválasztási táblázatban, amely a korábban létrehozott kombinációja, Azure alapértelmezett útvonalak és bármely útvonalakat a helyszíni hálózatokban BGP keresztül egy Azure virtuális hálózati átjárón keresztül érvényesítve. Egy adott hálózati csatoló hatékony útvonalak ismertetése akkor hasznos, ha útválasztási problémák elhárítása. A futó virtuális gép csatlakoztatott hálózati csatolóhoz hatékony útvonalait tekintheti meg.

1. A keresési mezőbe, a portál felső adja meg meg szeretné tekinteni a hatékony útvonalait a virtuális gép nevét. Ha nem tudja a virtuális gép nevét, adja meg a *virtuális gépek* be a keresőmezőbe. Ha **virtuális gépek** jelennek meg a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. Válassza ki **hálózati** alatt **beállítások**.
3. Válassza ki a hálózati kapcsolat neve.
4. Válassza ki **hatékony útvonalak** alatt **támogatási + hibaelhárítás**.
5. Tekintse át a meghatározásához, hogy létezik-e a megfelelő útvonalat, ahol szeretné továbbítani a forgalmat hatékony útvonalak listáját. Tudjon meg többet a következő ugrás típusa, melyek megjelennek a listában lévő [Útválasztás – áttekintés](virtual-networks-udr-overview.md).

**Parancsok**

- Az Azure CLI: [az hálózati nic megjelenítése-hatályos-útvonal-tábla](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Két végpontok közötti útválasztást ellenőrzése

A következő ugrás típusa, a virtuális gépek és az IP-cím, egy másik Azure-erőforrás, egy helyszíni erőforrás vagy internetes erőforrás között azt is meghatározhatja. Meghatározó Azure útválasztási akkor hasznos, ha útválasztási problémák elhárítása. A feladat végrehajtásához rendelkeznie kell egy meglévő hálózati figyelőt. Ha még nem rendelkezik egy meglévő hálózati figyelőt, hozzon létre egyet a lépések végrehajtásával [hozzon létre egy hálózati figyelőt példányt](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Adja meg a keresési mezőbe, a portál felső, *hálózati figyelőt* be a keresőmezőbe. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
2. Válassza ki **a következő Ugrás** alatt **diagnosztikai eszközök**.
3. Válassza ki a **előfizetés** és a **erőforráscsoport** a forrás virtuális gép szeretné érvényesíteni az útválasztást.
4. Válassza ki a **virtuális gép**, **hálózati illesztő** csatolva a virtuális géphez, és **forrás IP-címe** rendelt az érvényesíteni kívánt hálózati adapter az útválasztást.
5. Adja meg a **cél IP-címe** , amely szeretné érvényesíteni a történő továbbítást.
6. Válassza ki **a következő Ugrás**.
7. Egy rövid várakozás után információt ad vissza, amely azt ismerteti, hogy a következő ugrás típusa és irányíthatja a forgalmat útvonal a azonosító. Tudjon meg többet a következő ugrás típusok, melyek megjelennek a visszaadott [Útválasztás – áttekintés](virtual-networks-udr-overview.md).

**Parancsok**

- Az Azure CLI: [az hálózati megjelenítése-következő ugrás figyelője](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Engedélyek

Az útvonaltáblák és útvonalak feladatok elvégzésekor, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

| Műveletek                                                          |   Name (Név)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Egy útválasztási táblázatot olvasása                                    |
| Microsoft.Network/routeTables/write                             |   Hozható létre vagy frissíthető egy útválasztási táblázatot                        |
| Microsoft.Network/routeTables/delete                            |   Új útvonaltábla törlése                                  |
| Microsoft.Network/routeTables/join/action                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft.Network/routeTables/routes/read                       |   Olvassa el a útvonal                                          |
| Microsoft.Network/routeTables/routes/write                      |   Egy útvonal létrehozása vagy frissítése                              |
| Microsoft.Network/routeTables/routes/delete                     |   Útvonal törlése                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Az effektív útvonaltábla beolvasása a hálózati illesztő |
| Microsoft.Network/networkWatchers/nextHop/action                |   A következő ugrás a virtuális gép beolvasása                           |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy útvonal tábla használatával [PowerShell](powershell-samples.md) vagy [Azure CLI](cli-samples.md) parancsfájlok, vagy az Azure használatával [Resource Manager-sablonok](template-samples.md)
- Létrehozása és alkalmazása [Azure házirend](policy-samples.md) virtuális hálózatok
