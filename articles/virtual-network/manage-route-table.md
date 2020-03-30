---
title: Azure-útvonaltábla létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: További információ útvonaltábla létrehozásáról, módosításáról és törléséről.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247061"
---
# <a name="create-change-or-delete-a-route-table"></a>Útvonaltábla létrehozása, módosítása vagy törlése

Az Azure automatikusan irányítja az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok közötti forgalmat. Ha módosítani szeretné az Azure bármelyik alapértelmezett útválasztását, akkor hozzon létre egy útvonaltáblát. Ha még nem ismerkedik a virtuális hálózatokban való útválasztással, többet is megtudhat róla a [virtuális hálózati forgalom útválasztásában](virtual-networks-udr-overview.md) vagy egy [oktatóanyag](tutorial-create-route-table-portal.md)kitöltésével.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik ilyennel, állítson be egy Aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ezután hajtsa végre az alábbi feladatok egyikét, mielőtt a cikk bármely szakaszában lépéseket kezdene:

- **Portál felhasználói:** Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók:** Vagy futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/powershell)vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az Azure Cloud Shell böngésző lapján keresse meg a **Környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** t, ha még nincs kiválasztva.

    Ha a PowerShellt helyileg futtatja, használja az Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Is `Connect-AzAccount` futtassa a kapcsolatot az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói:** Vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/bash)vagy a CLI a számítógépről. Az Azure CLI 2.0.31-es vagy újabb verzióját használja, ha helyileg futtatja az Azure CLI-t. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Is `az login` futtassa a kapcsolatot az Azure-ral.

A fiók, amelybe bejelentkezik, vagy csatlakozik az Azure-hoz, hozzá kell rendelnie a [hálózati közreműködői szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az Azure-hely és előfizetés szerint létrehozhat ó, hogy hány útvonaltáblát hozhat létre. További információt a Hálózati korlátok – Azure Resource Manager ( [Hálózati korlátok – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)) .

1. Az [Azure Portal](https://portal.azure.com) menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

1. A keresőmezőbe írja be az *Útvonal táblát*. Amikor **az Útvonal tábla** megjelenik a keresési eredmények között, jelölje ki azt.

1. Az **Útvonaltábla** lapon válassza a **Létrehozás gombot.**

1. Az **Útvonaltábla létrehozása** párbeszédpanelen:

    1. Adja meg az útvonaltábla **nevét.**
    1. Válassza ki az **Előfizetést**.
    1. Válasszon egy meglévő **erőforráscsoportot,** vagy válassza az **Új létrehozása lehetőséget** új erőforráscsoport létrehozásához.
    1. Válasszon ki egy **helyet**.
    1. Ha az útvonaltáblát egy olyan virtuális hálózat alhálózatához kívánja társítani, amely VPN-átjárón keresztül csatlakozik a helyszíni hálózathoz, és nem szeretné a helyszíni útvonalakat az alhálózat hálózati csatolóihoz terjeszteni, állítsa a **Virtuális hálózati átjáró útvonalának propagálását** **letiltva**értékre.

1. Az új útvonaltábla létrehozásához válassza a **Létrehozás** lehetőséget.

### <a name="create-route-table---commands"></a>Útvonaltábla létrehozása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [Új-Azroutetable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Útvonaltáblák megtekintése

A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza **az Útvonaltáblák lehetőséget.** Az előfizetésben található útvonaltáblák fel vannak sorolva.

### <a name="view-route-table---commands"></a>Útvonaltábla megtekintése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla listája](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-Azroutetable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Útvonaltábla részleteinek megtekintése

1. A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki azt az útvonaltáblát, amelynek részleteit meg szeretné tekinteni.

1. Az útvonaltábla lap **Beállítások területén**tekintse meg az **Útvonalak az** útvonaltáblában vagy az **Alhálózatok,** amelyekhez az útvonaltábla társítva van.

Ha többet szeretne megtudni a gyakori Azure-beállításokról, olvassa el az alábbi információkat:

- [Tevékenységnapló](../azure-monitor/platform/platform-logs-overview.md)
- [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
- [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zárak](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-szkript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Az útvonaltábla részleteinek megtekintése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla megjelenítése](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-Azroutetable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Útvonaltábla módosítása

1. A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki a módosítani kívánt útvonaltáblát.

A leggyakoribb változások az útvonalak [hozzáadása,](#create-a-route) az útvonalak [eltávolítása,](#delete-a-route) az alhálózatokhoz [rendelt](#associate-a-route-table-to-a-subnet) útvonaltáblák vagy az alhálózatoktól való [elválasztása.](#dissociate-a-route-table-from-a-subnet)

### <a name="change-a-route-table---commands"></a>Útvonaltábla módosítása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla frissítése](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-Azroutetable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Az útvonaltáblát mellékelheti egy alhálózathoz. Az útvonaltábla nulla vagy több alhálózathoz társítható. Mivel az útvonaltáblák nincsenek virtuális hálózatokhoz társítva, minden olyan alhálózathoz hozzá kell rendelnie egy útvonaltáblát, amelyhez az útvonaltáblát társítani szeretné. Az Azure az összes forgalmat elhagyja az alhálózatot az útvonaltáblákon, [az alapértelmezett útvonalakon](virtual-networks-udr-overview.md#default)és a helyszíni hálózatról propagált útvonalakon létrehozott útvonalak alapján, ha a virtuális hálózat egy Azure virtuális hálózati átjáróhoz (ExpressRoute vagy VPN) csatlakozik. Csak egy útvonaltáblát társíthat olyan virtuális hálózatok alhálózataihoz, amelyek ugyanabban az Azure-helyen és előfizetésben vannak, mint az útvonaltábla.

1. A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

1. A virtuális hálózatok listájában válassza ki azt a virtuális hálózatot, amely tartalmazza azt az alhálózatot, amelyhez útvonaltáblát szeretne társítani.

1. A virtuális hálózat menüsorában válassza az **Alhálózatok**lehetőséget.

1. Jelölje ki azt az alhálózatot, amelyhez az útvonaltáblát társítani szeretné.

1. Az **Útvonal táblában**válassza ki az alhálózathoz társítani kívánt útvonaltáblát.

1. Kattintson a **Mentés** gombra.

Ha a virtuális hálózat egy Azure VPN-átjáróhoz csatlakozik, ne társítson útvonaltáblát az [átjáró alhálózatához,](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) amely *egy 0.0.0.0/0-s*céllal rendelkező útvonalat tartalmaz. Ellenkező esetben előfordulhat, hogy az átjárószolgáltatás nem működik megfelelően. A *0.0.0.0/0* útvonalon való használatáról a [Virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md#default-route)című témakörben talál további információt.

### <a name="associate-a-route-table---commands"></a>Útvonaltábla társítása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útvonaltábla szétválasztása alhálózatról

Ha egy útvonaltáblát egy alhálózattól távolít el, az Azure az alapértelmezett útvonalak alapján irányítja a [forgalmat.](virtual-networks-udr-overview.md#default)

1. A virtuális hálózat kezeléséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

1. A virtuális hálózatok listájában válassza ki azt a virtuális hálózatot, amely azt az alhálózatot tartalmazza, amelytől el szeretné választani az útvonaltáblát.

1. A virtuális hálózat menüsorában válassza az **Alhálózatok**lehetőséget.

1. Jelölje ki azt az alhálózatot, amelytől el szeretné választani az útvonaltáblát.

1. Az **Útvonal táblában**válassza a **Nincs**lehetőséget.

1. Kattintson a **Mentés** gombra.

### <a name="dissociate-a-route-table---commands"></a>Útvonaltábla szétválasztása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Útvonaltábla törlése

Alhálózatokhoz társított útvonaltábla nem törölhető. Az útvonaltábla [elválasztása](#dissociate-a-route-table-from-a-subnet) az összes alhálózatról, mielőtt megpróbálna törölni.

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki a törölni kívánt útvonaltáblát.

1. Válassza **a Törlés**lehetőséget, majd a megerősítést kérő párbeszédpanelen az **Igen** lehetőséget.

### <a name="delete-a-route-table---commands"></a>Útvonaltábla törlése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla törlése](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Eltávolítás-Azroutetable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Útvonal létrehozása

Van egy korlát, hogy hány útvonalak útvonaltábla hozhat létre egy Azure-hely és előfizetés. További információt a Hálózati korlátok – Azure Resource Manager ( [Hálózati korlátok – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)) .

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki azt az útvonaltáblát, amelyhez útvonalat szeretne hozzáadni.

1. Az útvonaltábla menüsorából válassza az **Útvonalak** > **hozzáadása lehetőséget.**

1. Adja meg az útvonal egyedi **útvonalnevét** az útvonaltáblában.

1. Írja be a **Cím előtagot**a Classless Inter-Domain Routing (CIDR) jelölésbe, amelyhez a forgalmat irányítani szeretné. Az előtag nem kettőzhető le egynél több útvonalon az útvonaltáblán belül, bár az előtag egy másik előtagon belül is lehet. Ha például a *10.0.0.0/16-ot* egy útvonal előtagjaként definiálta, akkor is definiálhat egy másik útvonalat a *10.0.0.0/22* címelőtaggal. Az Azure kiválaszt egy útvonalat a forgalom hoz a leghosszabb előtag egyezés alapján. További információ: [Hogyan választ ki az Azure egy útvonalat.](virtual-networks-udr-overview.md#how-azure-selects-a-route)

1. Válasszon egy **Következő ugrástípust**. A következő ugrástípusokról a [Virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).

1. Ha a **Következő ugrás típusú** **virtuális berendezést**választotta, adja meg a **Következő ugráscím IP-címét.**

1. Válassza **az OK gombot.**

### <a name="create-a-route---commands"></a>Útvonal létrehozása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [Új-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Útvonalak megtekintése

Az útvonaltábla nulla vagy több útvonalat tartalmaz. Az útvonalak megtekintésekor felsorolt információkról a [Virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md)című témakörben olvashat bővebben.

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki azt az útvonaltáblát, amelynek útvonalait meg szeretné tekinteni.

1. Az útvonaltábla menüsorában válassza az **Útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

### <a name="view-routes---commands"></a>Útvonalak megtekintése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla útvonallistája](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Útvonal részleteinek megtekintése

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki azt az útvonaltáblát, amely azt az útvonalat tartalmazza, amelynek részleteit meg szeretné tekinteni.

1. Az útvonaltábla menüsorában válassza az **Útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki azt az útvonalat, amelynek részleteit meg szeretné tekinteni.

### <a name="view-details-of-a-route---commands"></a>Útvonal részleteinek megtekintése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonaltábla útvonal a show-t](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Útvonal módosítása

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki a módosítani kívánt útvonalat tartalmazó útvonaltáblát.

1. Az útvonaltábla menüsorában válassza az **Útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki a módosítani kívánt útvonalat.

1. Módosítsa a meglévő beállításokat az új beállításokra, majd válassza a **Mentés gombot.**

### <a name="change-a-route---commands"></a>Útvonal módosítása - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonal-tábla útvonal frissítése](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Útvonal törlése

1. Az [útvonaltáblák](https://portal.azure.com) kezeléséhez nyissa meg az Azure Portalt. Keresse meg és válassza **az Útvonaltáblák lehetőséget.**

1. Az útvonaltábla-listában válassza ki a törölni kívánt útvonalat tartalmazó útvonaltáblát.

1. Az útvonaltábla menüsorában válassza az **Útvonalak** lehetőséget az útvonalak listájának megtekintéséhez.

1. Válassza ki a törölni kívánt útvonalat.

1. Válassza **a Törlés**lehetőséget, majd a megerősítést kérő párbeszédpanelen válassza az **Igen** lehetőséget.

### <a name="delete-a-route---commands"></a>Útvonal törlése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati útvonal-tábla útvonal törlése](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Eltávolítás-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Hatékony útvonalak megtekintése

Az egyes virtuálisgép-csatlakozóhálózati adapterek hatékony útvonalai a létrehozott útvonaltáblák, az Azure alapértelmezett útvonalai és a Border Gateway Protocol (BGP) keresztül az Azure virtuális hálózaton keresztül a helyszíni hálózatokból propagált útvonalak kombinációja. Átjáró. A hálózati adapter ekkult útjainak hatékony útvonalainak ismertetése az útválasztási problémák elhárításakor hasznos. Megtekintheti a hatékony útvonalak at bármely hálózati adapter, amely egy futó virtuális géphez csatlakozik.

1. Lépjen az [Azure Portalra](https://portal.azure.com) a virtuális gépek kezeléséhez. Keressen és válasszon **virtuális gépek**lehetőséget.

1. A virtuális gépek listájában válassza ki azt a virtuális gépet, amelynek a hatékony útvonalait meg szeretné tekinteni.

1. A Virtuálisgép menüsorban válassza a **Hálózat lehetőséget.**

1. Válassza ki a hálózati adapter nevét.

1. A hálózati csatoló menüsorában válassza a **Hatékony útvonalak lehetőséget.**

1. Tekintse át a hatékony útvonalak listáját, és nézze meg, hogy létezik-e a megfelelő útvonal arra a helyre, ahová a forgalmat irányítani szeretné. További információ a [virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md)listában látható következő ugrástípusokról.

### <a name="view-effective-routes---commands"></a>Hatékony útvonalak megtekintése - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Útválasztás ellenőrzése két végpont között

Meghatározhatja a következő ugrástípust egy virtuális gép és egy másik Azure-erőforrás, egy helyszíni erőforrás vagy egy internetes erőforrás IP-címe között. Az Azure útválasztásának meghatározása akkor hasznos, ha útválasztási problémákat hárít el. A feladat végrehajtásához rendelkeznie kell egy meglévő hálózati figyelővel. Ha nem rendelkezik meglévő hálózatfigyelővel, hozzon létre egyet a Hálózatfigyelő példány létrehozása című részben található lépések [végrehajtásával.](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

1. Nyissa meg az [Azure Portalon](https://portal.azure.com) a hálózati figyelők kezeléséhez. Keresse meg és válassza a **Hálózatfigyelő lehetőséget.**

1. A hálózatfigyelő menüsorában válassza a **Továbbugrás**lehetőséget.

1. A **Network Watcher | Következő ugrás** oldal:

    1. Válassza ki az **előfizetést** és annak a forrásvirtuális gépnek az **erőforráscsoportját,** amelyből érvényesíteni szeretné az útválasztást.

    1. Válassza ki a **virtuális gépet** és a virtuális géphez csatlakoztatott **hálózati felületet.**
    
    1. Adja meg azt a hálózati adapterhez rendelt **forrás IP-címet,** amelyről ellenőrizni szeretné az útválasztást.

    1. Adja meg azt **a cél IP-címet,** amelynek az útválasztást érvényesíteni szeretné.

1. Válassza **a Következő ugrás**lehetőséget.

Rövid várakozás után az Azure közli a következő ugrástípust és a forgalmat továbbító útvonal azonosítóját. További információ a [virtuális hálózati forgalom útválasztásában](virtual-networks-udr-overview.md)visszaadott következő ugrástípusokról.

### <a name="validate-routing-between-two-endpoints---commands"></a>Útválasztás ellenőrzése két végpont között - parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati figyelő show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Engedélyek

Az útvonaltáblákon és útvonalakon végzett feladatok elvégzéséhez a fiókot hozzá kell rendelni a [Hálózat közreműködői szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az alábbi táblázatban felsorolt megfelelő műveleteket kapta:

| Műveletek                                                          |   Név                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Útvonaltábla felolvasása                                    |
| Microsoft.Network/routeTables/write                             |   Útvonaltábla létrehozása vagy frissítése                        |
| Microsoft.Network/routeTables/delete                            |   Útvonaltábla törlése                                  |
| Microsoft.Network/routeTables/join/action                       |   Útválasztási táblázat társítása alhálózattal                   |
| Microsoft.Network/routeTables/routes/read                       |   Útvonal olvasása                                          |
| Microsoft.Network/routeTables/routes/write                      |   Útvonal létrehozása vagy frissítése                              |
| Microsoft.Network/routeTables/routes/delete                     |   Útvonal törlése                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Hálózati adapter hatékony útvonaltáblájának beszereznie |
| Microsoft.Network/networkWatchers/nextHop/action                |   A következő ugrás leigazolása egy virtuális gépről                           |

## <a name="next-steps"></a>További lépések

- Útvonaltábla létrehozása [PowerShell-](powershell-samples.md) vagy [Azure CLI-mintaparancsfájlokkal](cli-samples.md) vagy Azure [Resource Manager-sablonokkal](template-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
