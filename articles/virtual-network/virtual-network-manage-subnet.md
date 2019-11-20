---
title: Azure-beli virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan adhat hozzá, módosíthat vagy törölhet egy virtuális hálózati alhálózatot az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: e8717d10f61dfd50b9cdfa20a91203a5842d4c7d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185377"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése

Megtudhatja, hogyan adhat hozzá, módosíthat vagy törölhet egy virtuális hálózati alhálózatot. A virtuális hálózatba telepített összes Azure-erőforrás üzembe helyezése egy virtuális hálózaton belüli alhálózatba történik. Ha még nem ismeri a virtuális hálózatokat, a [virtuális hálózatok áttekintésében](virtual-networks-overview.md) és az [oktatóanyag](quick-create-portal.md)elvégzésével többet is megtudhat. Virtuális hálózat létrehozásához, módosításához vagy törléséhez tekintse meg a [virtuális hálózat kezelése](manage-virtual-network.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, a `az login` futtatásával is létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyhez hozzá kíván adni egy alhálózatot.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Válassza a **+ alhálózat**lehetőséget.
5. Adja meg a következő paraméterek értékeit:
   - **Név**: a névnek egyedinek kell lennie a virtuális hálózaton belül. A többi Azure-szolgáltatással való kompatibilitás érdekében javasoljuk, hogy használjon egy betűt a név első karaktere. Az Azure Application Gateway például nem fog olyan alhálózatba telepíteni, amelynek a neve egy számmal kezdődik.
   - **Címtartomány**: a tartománynak egyedinek kell lennie a virtuális hálózat címterület területén. A tartomány nem fedi át a virtuális hálózaton belüli többi alhálózati címtartományt. A címtartományt az osztály nélküli Inter-domain Routing (CIDR) jelöléssel kell megadni. Ha például egy virtuális hálózatban a 10.0.0.0/16 címtartomány van megadva, akkor megadhatja a 10.0.0.0/24 alhálózati címtartományt. A legkisebb megadható tartomány a/29, amely nyolc IP-címet biztosít az alhálózathoz. Az Azure fenntartja az egyes alhálózatok első és utolsó címeit a protokollok megfelelőségének megadásához. Három további cím van fenntartva az Azure-szolgáltatások használatára. Ennek eredményeképpen a/29 címtartományt tartalmazó alhálózat definiálása három használható IP-címet eredményez az alhálózatban. Ha virtuális hálózatot szeretne csatlakozni egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. További információ [az átjáró-alhálózatokkal kapcsolatos adott címtartomány-megfontolásokról](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Az alhálózat hozzáadása után a megadott feltételek szerint módosíthatja a címtartományt. Az alhálózati címtartomány módosításával kapcsolatos további információkért lásd: [alhálózat beállításainak módosítása](#change-subnet-settings).
   - **Hálózati biztonsági csoport**: nullát vagy egy meglévő hálózati biztonsági csoportot rendelhet egy alhálózathoz az alhálózat bejövő és kimenő hálózati forgalmának szűréséhez. A hálózati biztonsági csoportnak ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ a [hálózati biztonsági](security-overview.md) csoportokról és [a hálózati biztonsági csoportok létrehozásáról](tutorial-filter-network-traffic.md).
   - **Útválasztási táblázat**: nulla vagy egy meglévő útválasztási táblázatot rendelhet egy alhálózathoz a hálózati forgalom útválasztásának más hálózatokra való irányításához. Az útválasztási táblázatnak ugyanabban az előfizetésben és helyen kell lennie, mint a virtuális hálózatnak. További információ az [Azure útválasztásáról](virtual-networks-udr-overview.md) és az útválasztási [táblázat létrehozásáról](tutorial-create-route-table-portal.md)
   - **Szolgáltatási végpontok:** Egy alhálózat esetében nulla vagy több szolgáltatás-végpont is engedélyezhető. Egy szolgáltatás végpontjának engedélyezéséhez válassza ki azt a szolgáltatást vagy szolgáltatásokat, amely számára engedélyezni szeretné a szolgáltatás-végpontokat a **szolgáltatások** listájából. A hely automatikusan konfigurálva van egy végponthoz. Alapértelmezés szerint a szolgáltatási végpontok konfigurálva vannak a virtuális hálózat régiójához. Az Azure Storage esetében a regionális feladatátvételi forgatókönyvek támogatásához a végpontok automatikusan az [Azure párosított régiókhoz](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)vannak konfigurálva.
   - **Alhálózat delegálása:** Egy alhálózat több delegálásra is képes lehet. Az alhálózati delegálás explicit jogosultságokat biztosít a szolgáltatás számára, hogy a szolgáltatás telepítésekor egyedi azonosítóval hozzon létre szolgáltatás-specifikus erőforrásokat az alhálózatban. Egy szolgáltatás delegálásához válassza ki azt a szolgáltatást, amelyet delegálni szeretne a **szolgáltatások** listájából.

       Egy szolgáltatási végpont eltávolításához szüntesse meg a szolgáltatás végpontjának eltávolításához használni kívánt szolgáltatást. Ha többet szeretne megtudni a szolgáltatás-végpontokról és azok szolgáltatásairól, tekintse meg a [Virtual Network szolgáltatás végpontjai – áttekintés](virtual-network-service-endpoints-overview.md)című témakört. Miután engedélyezte a szolgáltatási végpontot a szolgáltatáshoz, engedélyeznie kell az alhálózat hálózati hozzáférését is a szolgáltatással létrehozott erőforrásokhoz. Ha például engedélyezi a Service-végpontot a *Microsoft. Storage*szolgáltatáshoz, engedélyeznie kell a hálózati hozzáférést minden olyan Azure Storage-fiókhoz, amelyhez hálózati hozzáférést szeretne biztosítani. A szolgáltatás-végpont által engedélyezett alhálózatokhoz való hálózati hozzáférés engedélyezéséről az egyes szolgáltatásokhoz kapcsolódó dokumentációban talál további információt.

     Annak ellenőrzéséhez, hogy a szolgáltatási végpont engedélyezve van-e az alhálózaton, tekintse meg az alhálózat bármely hálózati adapterének [érvényes útvonalait](diagnose-network-routing-problem.md) . Ha egy végpont konfigurálva van, megjelenik egy *alapértelmezett* útvonal a szolgáltatás előtagjaival, és egy nextHopType **VirtualNetworkServiceEndpoint**. Az útválasztással kapcsolatos további tudnivalókért tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört.
6. Ha hozzá szeretné adni az alhálózatot a kiválasztott virtuális hálózathoz, kattintson az **OK gombra**.

**Parancsok**

- Azure CLI: [az Network vnet subnet Create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alhálózat beállításainak módosítása

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt az alhálózatot tartalmazó virtuális hálózatot, amelynek a beállításait módosítani szeretné.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Az alhálózatok listájában válassza ki azt az alhálózatot, amelynek a beállításait módosítani szeretné. A következő beállításokat módosíthatja:

    - **Címtartomány:** Ha az alhálózaton belül nincs erőforrás telepítve, módosíthatja a címtartományt. Ha bármilyen erőforrás létezik az alhálózatban, akkor az erőforrásokat át kell helyeznie egy másik alhálózatba, vagy először törölnie kell őket az alhálózatból. Az erőforrások áthelyezéséhez vagy törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban található erőforrások áthelyezéséhez vagy törléséhez olvassa el az áthelyezni vagy törölni kívánt erőforrástípusok dokumentációját. Tekintse meg az [alhálózat hozzáadása](#add-a-subnet)szakasz 5. lépésében található **címtartomány** korlátozásait.
    - **Felhasználók**: az alhálózathoz való hozzáférést beépített szerepkörök vagy saját egyéni szerepkörök használatával szabályozhatja. Ha többet szeretne megtudni a szerepkörök és felhasználók az alhálózat eléréséhez való hozzárendeléséről, olvassa el a [szerepkör-hozzárendelés használata az Azure-erőforrásokhoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)című témakört.
    - **Hálózati biztonsági csoport** és **útválasztási táblázat**: lásd az [alhálózat hozzáadása](#add-a-subnet)című 5. lépést.
    - **Szolgáltatási végpontok**: lásd a szolgáltatási végpontokat az [alhálózat hozzáadása](#add-a-subnet)szakasz 5. lépésében. Ha egy meglévő alhálózathoz engedélyez egy szolgáltatási végpontot, győződjön meg arról, hogy az alhálózat egyik erőforrásán sem futnak kritikus feladatok. A szolgáltatási végpontok az alhálózaton lévő összes hálózati adapteren az útvonalakat a *0.0.0.0/0* címnek és a következő ugrási típusnak megfelelő alapértelmezett útvonal használatával válthatják ki a szolgáltatás előtagjaival *, valamint*a *VirtualNetworkServiceEndpoint*következő ugrási típusával. A kapcsoló során előfordulhat, hogy a nyitott TCP-kapcsolatok megszakadnak. A szolgáltatási végpont nincs engedélyezve, amíg az összes hálózati adapter számára a szolgáltatás felé irányuló adatforgalom az új útvonallal frissül. Az útválasztással kapcsolatos további tudnivalókért tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört.
    - **Alhálózat delegálása:** Tekintse meg az [alhálózat hozzáadása](#add-a-subnet)5. lépésében található szolgáltatási végpontokat. Az alhálózat delegálása nulla vagy több, engedélyezett delegálásra módosítható. Ha egy szolgáltatás erőforrása már telepítve van az alhálózatban, akkor az alhálózati delegálás nem vehető fel és nem távolítható el, amíg el nem távolítja a szolgáltatás összes erőforrását. Egy másik szolgáltatás delegálásához válassza ki azt a szolgáltatást, amelyet delegálni szeretne a **szolgáltatások** listájából.
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [az Network vnet subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Alhálózat törlése

Csak akkor törölhet egy alhálózatot, ha nincsenek erőforrások az alhálózatban. Ha vannak erőforrások az alhálózatban, törölnie kell az alhálózatban lévő erőforrásokat az alhálózat törlése előtt. Az erőforrások törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban található erőforrások törlésének megismeréséhez olvassa el a törölni kívánt erőforrástípusok dokumentációját.

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amely a törölni kívánt alhálózatot tartalmazza.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Az alhálózatok listájában válassza a **...** , a jobb oldalon a törölni kívánt alhálózat elemet.
5. Válassza a **Törlés**lehetőséget, majd válassza az **Igen**lehetőséget.

**Parancsok**

- Azure CLI: [az Network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

Az alhálózatokon lévő feladatok végrehajtásához a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

|Műveletek                                                                   |   Name (Név)                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Virtuális hálózat alhálózatának beolvasása              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Virtuális hálózati alhálózat létrehozása vagy frissítése  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Virtuális hálózati alhálózat törlése            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Csatlakozás virtuális hálózathoz                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Szolgáltatási végpont engedélyezése egy alhálózathoz     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Virtuális gépek beolvasása egy alhálózaton       |

## <a name="next-steps"></a>Következő lépések

- Virtuális hálózat és alhálózatok létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) parancsfájl használatával vagy Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure](policy-samples.md) -szabályzat létrehozása és alkalmazása virtuális hálózatokhoz
