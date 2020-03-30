---
title: Azure virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan adhat hozzá, módosíthat vagy törölhet virtuális hálózati alhálózatot az Azure-ban.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246942"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Virtuális hálózat alhálózatának hozzáadása, módosítása vagy törlése

További információ a virtuális hálózati alhálózat hozzáadásáról, módosításáról és törléséről. A virtuális hálózatban üzembe helyezett Azure-erőforrások a virtuális hálózat egy alhálózatán vannak üzembe helyezve. Ha még nem ismerkedik a virtuális hálózatokkal, többet is megtudhat róluk a [Virtuális hálózat áttekintése](virtual-networks-overview.md) vagy egy [rövid útmutató](quick-create-portal.md)kitöltésével. A virtuális hálózatok kezeléséről a [Virtuális hálózat létrehozása, módosítása és törlése](manage-virtual-network.md)című témakörben olvashat bővebben.

## <a name="before-you-begin"></a>Előkészületek

Ha még nem rendelkezik ilyennel, állítson be egy Aktív előfizetéssel rendelkező Azure-fiókot. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Ezután hajtsa végre az alábbi feladatok egyikét, mielőtt a cikk bármely szakaszában lépéseket kezdene: 

- **Portál felhasználói:** Jelentkezzen be az [Azure Portalon](https://portal.azure.com) az Azure-fiókjával.

- **PowerShell-felhasználók:** Vagy futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/powershell)vagy futtassa a PowerShellt a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az Azure Cloud Shell böngésző lapján keresse meg a **Környezet kiválasztása** legördülő listát, majd válassza a **PowerShell** t, ha még nincs kiválasztva.

    Ha a PowerShellt helyileg futtatja, használja az Azure PowerShell 1.0.0-s vagy újabb verzióját. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az.Network`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Is `Connect-AzAccount` futtassa a kapcsolatot az Azure-ral.

- **Azure parancssori felület (CLI) felhasználói:** Vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/bash)vagy a CLI a számítógépről. Az Azure CLI 2.0.31-es vagy újabb verzióját használja, ha helyileg futtatja az Azure CLI-t. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Is `az login` futtassa a kapcsolatot az Azure-ral.

A fiókot, amelybe bejelentkezik, vagy amelyhez csatlakozik az Azure-hoz, hozzá kell rendelnia a [Hálózati közreműködőszerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. A virtuális hálózatok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

2. Válassza ki annak a virtuális hálózatnak a nevét, amelyhez alhálózatot szeretne hozzáadni.

3. A **Beállítások területen**válassza az **Alhálózatok** > **alhálózat**lehetőséget.

4. Az **Alhálózat hozzáadása** párbeszédpanelen adja meg a következő beállítások értékeit:

    | Beállítás | Leírás |
    | --- | --- |
    | **Név** | A névnek egyedinek kell lennie a virtuális hálózaton belül. A más Azure-szolgáltatásokkal való maximális kompatibilitás érdekében azt javasoljuk, hogy a név első karaktereként egy betűt használjon. Például az Azure Application Gateway nem telepíti egy alhálózat, amelynek neve, amely egy számkezdődik. |
    | **Címtartomány** | <p>A tartománynak egyedinek kell lennie a virtuális hálózat címterén belül. A tartomány nem lehet átfedésben a virtuális hálózaton belüli más alhálózati címtartományokkal. A címteret osztálynélküli tartományok közötti útválasztás (CIDR) jelöléssel kell megadni.</p><p>A *10.0.0.0/16*címtérrel rendelkező virtuális hálózatban például *10.0.0.0/22*alhálózati címteret adhat meg. A legkisebb megadható tartomány *a /29*, amely nyolc IP-címet biztosít az alhálózathoz. Az Azure fenntartja az első és az utolsó címet az egyes alhálózatokban a protokollok megfelelőségéhez. Három további cím van fenntartva az Azure szolgáltatás használatához. Ennek eredményeképpen a */29* címtartományú alhálózat definiálása három használható IP-címet eredményez az alhálózatban.</p><p>Ha virtuális hálózatot kíván csatlakoztatni egy VPN-átjáróhoz, létre kell hoznia egy átjáró alhálózatot. További információ az [átjáróalhálózatok címtartományával kapcsolatos szempontokról.](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) Az alhálózat hozzáadása után bizonyos feltételek mellett módosíthatja a címtartományt. Az alhálózati címtartomány módosításáról az [Alhálózati beállítások módosítása (Alhálózati beállítások) (Alhálózati beállítások módosítása) témakörben](#change-subnet-settings)olvashat.</p> |
    | **Hálózati biztonsági csoport** | Az alhálózat bejövő és kimenő hálózati forgalmának szűréséhez egy meglévő hálózati biztonsági csoportot társíthat egy alhálózathoz. A hálózati biztonsági csoportnak ugyanabban az előfizetésben és helyen kell léteznie, mint a virtuális hálózatnak. További információ a [hálózati biztonsági csoportokról](security-overview.md) és [a hálózati biztonsági csoportok létrehozásáról.](tutorial-filter-network-traffic.md) |
    | **Útvonaltábla** | A hálózati forgalom más hálózatokra irányuló útválasztásának szabályozásához adott esetben egy meglévő útvonaltáblát is társíthat egy alhálózathoz. Az útvonaltáblának ugyanabban az előfizetésben és helyen kell léteznie, mint a virtuális hálózatnak. További információ az [Azure-útválasztásról](virtual-networks-udr-overview.md) és [az útvonaltábla létrehozásáról.](tutorial-create-route-table-portal.md) |
    | **Szolgáltatásvégpontok** | <p>Az alhálózatokhoz szükség esetén egy vagy több szolgáltatásvégpont is engedélyezhető. Ha egy szolgáltatásvégpontot szeretne engedélyezni egy szolgáltatáshoz, válassza ki azt a szolgáltatást vagy szolgáltatásokat, amelyhez a szolgáltatásvégpontokat engedélyezni szeretné a **Szolgáltatások** listából. Az Azure automatikusan konfigurálja a végpont helyét. Alapértelmezés szerint az Azure konfigurálja a szolgáltatás végpontok a virtuális hálózat régiójában. A regionális feladatátvételi forgatókönyvek támogatása érdekében az Azure automatikusan konfigurálja a végpontokat az [Azure párosított régióihoz](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) az Azure Storage számára.</p><p>Szolgáltatásvégpont eltávolításához törölje a szolgáltatás kijelölésének kijelölését, amelyhez el szeretné távolítani a szolgáltatásvégpontot. Ha többet szeretne megtudni a szolgáltatásvégpontokról és az okról a szolgáltatásokról, amelyekhez engedélyezhetők, olvassa el a Virtuális hálózati szolgáltatás végpontjai című [témakört.](virtual-network-service-endpoints-overview.md) Miután engedélyezte a szolgáltatás végpontját egy szolgáltatáshoz, engedélyeznie kell a hálózati hozzáférést az alhálózat számára a szolgáltatással létrehozott erőforráshoz. Ha például engedélyezi a szolgáltatásvégpontot a **Microsoft.Storage**számára, akkor hálózati hozzáférést is engedélyeznie kell az összes Azure Storage-fiókhoz, amelyhez hálózati hozzáférést szeretne biztosítani. A hálózati hozzáférés engedélyezéséhez azokhoz az alhálózatokhoz, amelyekhez egy szolgáltatásvégpont engedélyezve van, tekintse meg annak az egyes szolgáltatásnak a dokumentációját, amelyhez engedélyezte a szolgáltatásvégpontot.</p><p>Annak ellenőrzéséhez, hogy egy alhálózat hoz engedélyezve van-e egy szolgáltatásvégpont, tekintse meg az alhálózat bármely hálózati illesztőjének [érvényes útvonalait.](diagnose-network-routing-problem.md) Végpont konfigurálásakor megjelenik egy *alapértelmezett* útvonal a szolgáltatás címelőtagaival, valamint a **VirtualNetworkServiceEndpoint**következő ugrástípusával. Az útválasztásról a [Virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).</p> |
    | **Alhálózat delegálása** | Az alhálózatokhoz szükség esetén egy vagy több delegálás is engedélyezve lehet. Az alhálózati delegálás explicit engedélyeket ad a szolgáltatásnak, hogy szolgáltatásspecifikus erőforrásokat hozzon létre az alhálózatban egy egyedi azonosító használatával a szolgáltatás telepítése során. Ha delegálni szeretne egy szolgáltatást, válassza ki azt a szolgáltatást, amelynek delegálni szeretné a szolgáltatásokat a **Szolgáltatások** listából. |

5. Ha hozzá szeretné adni az alhálózatot a kiválasztott virtuális hálózathoz, válassza az **OK gombot.**

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Alhálózati beállítások módosítása

1. A virtuális hálózatok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

2. Jelölje ki a módosítani kívánt alhálózatot tartalmazó virtuális hálózat nevét.

3. A **Beállítások területen**válassza az **Alhálózatok**lehetőséget.

4. Az alhálózatok listájában jelölje ki azt az alhálózatot, amelynek beállításait módosítani szeretné.

5. Az alhálózati lapon módosítsa az alábbi beállítások egyikét:

    | Beállítás | Leírás |
    | --- | --- |
    | **Címtartomány** | Ha az alhálózaton belül nincs enek erőforrások telepítve, módosíthatja a címtartományt. Ha az alhálózatban vannak erőforrások, akkor vagy át kell helyeznie az erőforrásokat egy másik alhálózatba, vagy először törölnie kell őket az alhálózatból. Az erőforrások áthelyezéséhez vagy törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban lévő erőforrások áthelyezéséről és törléséről az egyes erőforrástípusok dokumentációjában tájékozódhat. Lásd a **Cím tartomány** ra vonatkozó korlátozásokat az [Alhálózat hozzáadása](#add-a-subnet)4. |
    | **Felhasználók** | Az alhálózathoz való hozzáférést beépített szerepkörök kel vagy saját egyéni szerepkörökkel szabályozhatja. Ha többet szeretne tudni aszerepkörök és a felhasználók hozzárendeléséről az alhálózat eléréséhez, olvassa el a [Szerepkör-hozzárendelés hozzáadása](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)című témakört. |
    | **Hálózati biztonsági csoport** és **Útválasztási táblázat** | Lásd az [Alhálózat hozzáadása](#add-a-subnet)4. |
    | **Szolgáltatásvégpontok** | <p>Lásd a szolgáltatás végpontjait az [Alhálózat hozzáadása](#add-a-subnet)4. Ha egy meglévő alhálózat szolgáltatásvégpontot engedélyez, győződjön meg arról, hogy nem futnak kritikus feladatok az alhálózat egyetlen erőforrásán sem. A szolgáltatásvégpontok az alhálózat minden hálózati illesztőjének útvonalait kapcsolják. A szolgáltatásvégpontok az alapértelmezett útvonal *0.0.0.0/0* címelőtaggal és az *internet*következő ugrástípusával történő használatától a szolgáltatás címelőtagaival rendelkező új útvonal és a *VirtualNetworkServiceEndpoint*következő ugrástípusáig haladnak.</p><p>A váltás során bármely nyitott TCP-kapcsolat megszakadhat. A szolgáltatásvégpont nincs engedélyezve, amíg az összes hálózati adapter szolgáltatására irányuló forgalom nem frissül az új útvonallal. Az útválasztásról a [Virtuális hálózati forgalom útválasztása](virtual-networks-udr-overview.md).</p> |
    | **Alhálózat delegálása** | Lásd a szolgáltatás végpontjait az [Alhálózat hozzáadása](#add-a-subnet)4. Az alhálózati delegálás módosítható nullára vagy több delegálásra. Ha egy szolgáltatás egyik erőforrása már telepítve van az alhálózatban, az alhálózati delegálás nem adható hozzá és távolítható el, amíg a szolgáltatás összes erőforrását el nem távolítja. Ha másik szolgáltatást szeretne delegálni, válassza ki azt a szolgáltatást, amelynek delegálni szeretné a **Szolgáltatásokat** listából. |

6. Kattintson a **Mentés** gombra.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Alhálózat törlése

Az alhálózat csak akkor törölhető, ha nincsenek erőforrások az alhálózatban. Ha az erőforrások az alhálózatban vannak, az alhálózat törlése előtt törölnie kell ezeket az erőforrásokat. Az erőforrások törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokban lévő erőforrások törléséről az egyes erőforrástípusok dokumentációjában tájékozódhat.

1. A virtuális hálózatok megtekintéséhez nyissa meg az [Azure Portalt.](https://portal.azure.com) Keressen és válassza a **Virtuális hálózatok lehetőséget.**

2. Jelölje ki a törölni kívánt alhálózatot tartalmazó virtuális hálózat nevét.

3. A **Beállítások területen**válassza az **Alhálózatok**lehetőséget.

4. Az alhálózatok listájában jelölje ki a törölni kívánt alhálózatot.

5. Válassza **a Törlés**lehetőséget, majd a megerősítést kérő párbeszédpanelen az **Igen** lehetőséget.

### <a name="commands"></a>Parancsok

| Eszköz | Parancs |
| ---- | ------- |
| Azure CLI | [az hálózati virtuális hálózat alhálózatának törlése](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Engedélyek

Az alhálózatokon végzett feladatok elvégzéséhez a fiókot hozzá kell rendelni a [Hálózat közreműködői szerepkörhöz](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) vagy egy [egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az alábbi táblázatban a megfelelő műveleteket rendelte hozzá:

|Műveletek                                                                   |   Név                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/alhálózatok/olvasás                           |   Virtuális hálózati alhálózat olvasása              |
|Microsoft.Network/virtualNetworks/alhálózatok/írás                          |   Virtuális hálózati alhálózat létrehozása vagy frissítése  |
|Microsoft.Network/virtualNetworks/alhálózatok/delete                         |   Virtuális hálózati alhálózat törlése            |
|Microsoft.Network/virtualNetworks/alhálózatok/join/action                    |   Csatlakozás virtuális hálózathoz                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Szolgáltatásvégpont engedélyezése alhálózathoz     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   A virtuális gépek beszereznie egy alhálózatban       |

## <a name="next-steps"></a>További lépések

- Virtuális hálózat és alhálózatok létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaparancsfájlokkal vagy Azure [Resource Manager-sablonok](template-samples.md) használatával [Azure CLI](cli-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
