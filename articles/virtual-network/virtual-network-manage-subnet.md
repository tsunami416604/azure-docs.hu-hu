---
title: Hozzáadása, módosítása vagy törlése az Azure virtuális hálózat alhálózat
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan hozzáadása, módosítása vagy törlése az Azure-beli virtuális hálózatának alhálózatához.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 1810c92eb98a78510db79cb72ca2e8209768fc89
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660901"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Hozzáadása, módosítása vagy virtuális hálózati alhálózat törlése

Ismerje meg, hogyan hozzáadása, módosítása vagy törlése a virtuális hálózat alhálózatán. Egy virtuális hálózaton üzembe helyezett összes Azure-erőforrást a rendszer üzembe helyezi egy virtuális hálózat alhálózatán. Ha most ismerkedik a virtuális hálózatok, többet is megtudhat a velük kapcsolatos a [virtuális hálózatok áttekintése](virtual-networks-overview.md) vagy; Ehhez hajtsa végre egy [oktatóanyag](quick-create-portal.md). Létrehozásához, módosításához vagy a virtuális hálózat törlése, lásd: [virtuális hálózat kezelése](manage-virtual-network.md).

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszban ismertetett lépések elvégzése előtt hajtsa végre a következőket:

- Ha még nincs Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` kapcsolat létrehozása az Azure-ral.

Jelentkezzen be, vagy csatlakozhat az Azure-ban, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni szerepkör](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepel a megfelelő műveleteket rendelt [engedélyek ](#permissions).

## <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, alhálózat hozzáadni kívánt.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Válassza ki **+ alhálózat**.
5. Adja meg az értékeket az alábbi paraméterekkel:
    - **Név**: A névnek egyedinek kell lennie a virtuális hálózaton belül. A maximális kompatibilitás érdekében az egyéb Azure-szolgáltatásokkal javasoljuk betűvel a név első karaktert. Például az Azure Application Gateway nem fog üzembe egy alhálózatot, amely számos kezdetű névvel rendelkezik.
    - **Címtartomány**: A tartomány a virtuális hálózathoz megadott címtéren belül egyedinek kell lennie. A tartomány nem lehet átfedésben a többi alhálózati címtartomány a virtuális hálózaton belül. A címtér használatával Classless Inter-Domain Routing (CIDR) jelölésrendszer szerint kell megadni. Például egy virtuális hálózatot a 10.0.0.0/16 címteret, definiálhat egy alhálózat címtere a 10.0.0.0/24. A legkisebb is megadhat tartománya akár/29 méretű, ami nyolc IP-címet biztosít az alhálózat. Az Azure lefoglalja a első és utolsó cím mindegyik olyan alhálózatban, a protokoll irányítópultja. Három további címek Azure-szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen az alhálózat egy/29 meghatározása három használható IP-címet az alhálózat időtartam cím. Ha azt tervezi, a virtuális hálózat összekapcsolása egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. Tudjon meg többet [címhez tartomány szempontjai átjáróalhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Meghatározott körülmények között az alhálózat hozzáadása után módosíthatja a címtartomány. Ismerje meg, hogyan módosíthatja egy alhálózati címtartományt, lásd: [alhálózati beállítások módosítása](#change-subnet-settings).
    - **Hálózati biztonsági csoport**: Nulla vagy egy meglévő hálózati biztonsági csoport egy alhálózathoz, az alhálózat bejövő és kimenő hálózati forgalom szűrése társíthat. A hálózati biztonsági csoport ugyanazt az előfizetést és helyet a virtuális hálózatnak léteznie kell. Tudjon meg többet [hálózati biztonsági csoportok](security-overview.md) és [egy hálózati biztonsági csoport létrehozása](tutorial-filter-network-traffic.md).
    - **Útválasztási táblázat**: Nulla vagy egy meglévő útválasztási táblázatot egy alhálózathoz, szabályozhatja a hálózat forgalmának útválasztása más hálózatokhoz is hozzárendelhetők. Az útvonaltábla ugyanazt az előfizetést és helyet a virtuális hálózatnak léteznie kell. Tudjon meg többet [Azure útválasztási](virtual-networks-udr-overview.md) és [útválasztási táblázat létrehozása](tutorial-create-route-table-portal.md)
    - **A Szolgáltatásvégpontok:** Egy alhálózat rendelkezhet, nulla vagy egynél több Szolgáltatásvégpontok engedélyezve. Ahhoz, hogy egy végpontot a szolgáltatáshoz, válassza ki a szolgáltatás vagy az, hogy engedélyezze a szolgáltatásvégpontokat a kívánt szolgáltatások a **szolgáltatások** listája. A hely automatikusan konfigurálva van a végpont. Alapértelmezés szerint a Szolgáltatásvégpontok a virtuális hálózati régióban van konfigurálva. Az Azure Storage esetében támogatja a regionális feladatátvételi funkciók, a végpontok automatikusan megtörténik [Azure párosított régióiról](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
    - **Alhálózat delegálás:** Egy alhálózaton több delegálásokat engedélyezve van, a nulla is rendelkezhet. Alhálózat delegálás révén a szolgáltatásspecifikus-erőforrások létrehozása az alhálózat egy egyedi azonosító segítségével, a szolgáltatás telepítésekor a szolgáltatás a kifejezett engedélyek. Delegálhat egy szolgáltatáshoz, majd a szolgáltatás a delegálni szeretne a **szolgáltatások** listája. 

    Eltávolít egy végpontot, hogy törölje a szolgáltatást el kívánja távolítani a szolgáltatásvégpontot. A Szolgáltatásvégpontok és a szolgáltatások is engedélyezhető a kapcsolatos további tudnivalókért lásd: [virtuális hálózati Szolgáltatásvégpontok áttekintése](virtual-network-service-endpoints-overview.md). Miután engedélyezte egy végpontot a szolgáltatáshoz, az IP-alhálózatot a szolgáltatással létrehozott erőforrások hálózati hozzáférésének is engedélyeznie kell. Például, ha engedélyezi a szolgáltatás végpontját *Microsoft.Storage*, is engedélyeznie kell az összes Azure Storage-fiókok, hálózati hozzáférést szeretne való hálózati hozzáférés. További információ az alhálózatokra, amelyek a szolgáltatásvégpont engedélyezve van a hálózati hozzáférés engedélyezése, az adott szolgáltatás dokumentációjában talál engedélyezve van a szolgáltatásvégpontot.

    Ellenőrizze, hogy a szolgáltatásvégpont engedélyezve van-e egy alhálózathoz, tekintse meg a [érvényes útvonalak](diagnose-network-routing-problem.md) az alhálózat bármely hálózati interfész. Ha a végpont konfigurálva van, megjelenik egy *alapértelmezett* útvonal a címelőtagokat a szolgáltatás és a egy a nexthoptype elem **VirtualNetworkServiceEndpoint**. További információ az útválasztásról, lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).
6. Az alhálózat hozzáadása a kiválasztott virtuális hálózat, válassza ki a **OK**.

**Parancsok**

- Az Azure CLI: [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alhálózati beállítások módosítása

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, amely tartalmazza az alhálózat beállításait módosítani szeretné.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Alhálózatok listájában válassza ki az alhálózatot, beállításait módosítani szeretné. A következő beállításokat módosíthatja:

    - **Címtartomány:** Ha nincsenek erőforrások az alhálózaton belüli van telepítve, módosíthatja a címtartomány. Ha erőforrásokat az alhálózat létezik, meg kell vagy az erőforrások áthelyezése egy másik alhálózatot, vagy először törölje azokat az alhálózat. A lépéseket, mozgathat vagy törölhet egy erőforrás az erőforrás változhat. Helyezze át vagy törölje az erőforrást, amely alhálózatokon vannak, olvassa el az egyes erőforrástípusok áthelyezni vagy törölni kívánt dokumentációját. Tekintse meg a függőségeket **címtartomány** 5. lépésében [adjon hozzá egy alhálózatot](#add-a-subnet).
    - **Felhasználók**: Szabályozhatja az alhálózathoz való hozzáférést beépített szerepkörökkel vagy saját egyéni szerepkörök használatával. Szerepkörök és a felhasználók férhetnek hozzá az alhálózat hozzárendelése kapcsolatos további információkért lásd: [az Azure-erőforrásokhoz való hozzáférés kezelése szerepkör-hozzárendelés használatával](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Hálózati biztonsági csoport** és **útvonaltábla**: Lásd az 5. lépés: [adjon hozzá egy alhálózatot](#add-a-subnet).
    - **Szolgáltatásvégpontokat**: Tekintse meg az 5. lépésében Szolgáltatásvégpontok [adjon hozzá egy alhálózatot](#add-a-subnet). Egy meglévő alhálózat szolgáltatásvégpont engedélyezésekor győződjön meg arról, hogy semmilyen kritikus feladat fut-e a az alhálózaton található erőforrásokat. Szolgáltatásvégpontok váltson az alapértelmezett útvonal használatával az alhálózat összes hálózati adapteren útvonalakat a *0.0.0.0/0* -előtag és következő ugrási típusú *Internet*, egy új útvonalat használja a oldja meg a szolgáltatás és a egy következő ugrási típusú előtagok *VirtualNetworkServiceEndpoint*. A váltás során nyitott TCP-kapcsolatokat lehet megszüntetni. A szolgáltatási végpont nem érhető el, amíg adatforgalmakat, hogy a szolgáltatás az összes hálózati adapter frissülnek az új útvonal. További információ az útválasztásról, lásd: [Útválasztás áttekintése](virtual-networks-udr-overview.md).
    - **Alhálózat delegálás:** Tekintse meg az 5. lépésében Szolgáltatásvégpontok [adjon hozzá egy alhálózatot](#add-a-subnet). Alhálózat delegálás engedélyezve nulla vagy egynél több delegálásokat módosíthatók. Ha egy erőforrást egy szolgáltatás már telepítve van az alhálózaton, alhálózat delegálás nem távolítható el a szolgáltatás az összes erőforrást eltávolították. Delegálni egy másik szolgáltatás, válassza ki a szolgáltatást, a delegálni szeretne a **szolgáltatások** listája. 
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat alhálózati update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Alhálózat törlése

Egy alhálózat csak akkor, ha nincsenek erőforrások az alhálózat törölheti. Ha az alhálózaton lévő erőforrásokat, törölnie kell az erőforrások, amelyek az alhálózaton, az alhálózat törlése előtt. Erőforrás törlése a lépéseket, az erőforrás változhat. Megtudhatja, hogyan törölheti az erőforrást, amely alhálózatokon vannak, olvassa el a dokumentációt, egyes erőforrástípusok, amelyet törölni szeretne.

1. Írja be a keresőmezőbe, a portál tetején, *virtuális hálózatok* kifejezést a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények közül válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, amely tartalmazza az alhálózat törli.
3. A **BEÁLLÍTÁSOK** területen válassza az **Alhálózatok** elemet.
4. Alhálózatok listájában válassza ki a **...** , a jobb oldalon az alhálózat kívánt törlése
5. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat törlése](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

Alhálózatok feladatait, a fiókot hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő műveleteket az alábbi táblázatban felsorolt:

|Műveletek                                                                   |   Name (Név)                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Olvassa el a virtuális hálózat alhálózatának              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Létrehozni vagy frissíteni a virtuális hálózat alhálózatának  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Virtuális hálózati alhálózat törlése            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Csatlakozás virtuális hálózathoz                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Egy alhálózat szolgáltatásvégpont engedélyezése     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Az alhálózat a virtuális gépek beolvasása       |

## <a name="next-steps"></a>További lépések

- Hozzon létre egy virtuális hálózat és alhálózatok [PowerShell](powershell-samples.md) vagy [Azure CLI-vel](cli-samples.md) parancsfájlokat, vagy az Azure-minta [Resource Manager-sablonok](template-samples.md)
- Hozzon létre, és a alkalmazni [az Azure policy](policy-samples.md) virtuális hálózatok
