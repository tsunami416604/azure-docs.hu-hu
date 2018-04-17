---
title: Hozzáadása, módosítása vagy egy Azure virtuális hálózati alhálózat törlése |} Microsoft Docs
description: Megtudhatja, hogyan hozzáadása, módosítása vagy törlése a virtuális hálózati alhálózat az Azure-ban.
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
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 550fe16c5997947b528d284b7afdce9af0b7a56b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Hozzáadása, módosítása vagy virtuális hálózati alhálózat törlése

Megtudhatja, hogyan hozzáadása, módosítása vagy virtuális hálózati alhálózat törlése. Ha még nem ismeri a virtuális hálózatok hozzáadása, módosítása vagy alhálózat törlése előtt, azt javasoljuk, hogy olvassa el [Azure Virtual Network áttekintése](virtual-networks-overview.md) és [létrehozása, módosítása vagy törlése a virtuális hálózati](manage-virtual-network.md). Egy virtuális hálózatot helyezett összes Azure-erőforrások egy alhálózatba a virtuális hálózaton belül vannak telepítve.
 
## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- A portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.2.0 szükséges vagy újabb. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.26 verziója szükséges, vagy később. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

## <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. Válassza ki a virtuális hálózati alhálózat hozzáadni kívánt virtuális hálózatok listája.
3. A **beállítások**, jelölje be **alhálózatok**.
4. Válassza ki **+ alhálózati**.
5. Adja meg a következő paraméterekkel:
    - **Név**: A nevet a virtuális hálózaton belül egyedinek kell lennie.
    - **Címtartomány**: A tartományon a címtartomány a virtuális hálózaton belül egyedinek kell lennie. A tartomány más alhálózati címtartományt, a virtuális hálózaton belül nem lehet átfedésben. A címterület Classless Inter-Domain Routing (CIDR) jelölésrendszer szerint kell adni. Például cím terület 10.0.0.0/16 rendelkező virtuális hálózatban, megadhat egy alhálózat címtartománya a 10.0.0.0/24. A legkisebb megadható tartománya /29, biztosító nyolc IP-cím az alhálózat. Azure fenntartja az első és utolsó cím protokoll megfelelési minden alhálózatban. Három további címek az Azure szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen meghatározása egy /29 alhálózat cím a tartomány eredményezi, hogy az alhálózat három használható IP-címet. Ha azt tervezi, VPN-átjáró egy virtuális hálózathoz csatlakozni, létre kell hoznia egy átjáró-alhálózatot. További információ [adott cím tartomány szempontjai átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány meghatározott feltételek mellett az alhálózat hozzáadása után módosíthatja. A alhálózati címtartományt módosításáról további tudnivalókért lásd: [alhálózati beállítások módosítása](#change-subnet-settings).
    - **Hálózati biztonsági csoport**: nulla vagy egy meglévő hálózati biztonsági csoport alhálózathoz az alhálózat bejövő és kimenő hálózati forgalom szűrésére is társíthat. A hálózati biztonsági csoport ugyanazt az előfizetést és a virtuális hálózat helyének léteznie kell. További információ [hálózati biztonsági csoportok](security-overview.md) és [hálózati biztonsági csoport létrehozása](tutorial-filter-network-traffic.md).
    - **Az útvonaltábla**: nulla vagy egy meglévő útvonaltábla alhálózathoz vezérlésére hálózati forgalmának irányítását a más hálózatokkal is társíthat. Az útvonal táblázatnak már léteznie kell az előfizetés és a virtuális hálózati helyen. További információ [Azure útválasztási](virtual-networks-udr-overview.md) és [egy útválasztási táblázatot létrehozása](tutorial-create-route-table-portal.md)
    - **Szolgáltatás végpontjait:** alhálózat állhat nulla vagy egynél több Szolgáltatásvégpontok az engedélyezve van. A szolgáltatás szolgáltatásvégpont engedélyezéséhez jelölje be a szolgáltatást vagy szolgáltatásokat, a szolgáltatás végpontjainak engedélyezni szeretné a **szolgáltatások** listája. A szolgáltatásvégpont eltávolításához a el kívánja távolítani a szolgáltatási végpont a szolgáltatás eltávolításához. Szolgáltatásvégpontok kapcsolatos további információkért lásd: [virtuális hálózati szolgáltatás végpontok áttekintése](virtual-network-service-endpoints-overview.md). Amennyiben engedélyezi egy szolgáltatás szolgáltatásvégpont, engedélyeznie kell a hálózati hozzáférés az alhálózat, a szolgáltatással létrehozott erőforrás. Például, ha engedélyezi a szolgáltatási végpont a *Microsoft.Storage*, is engedélyeznie kell az összes Azure Storage-fiók hozzáférési jogot szeretné hálózati hozzáférést. A szolgáltatásvégpont engedélyezett alhálózatokra hálózati hozzáférés engedélyezésével kapcsolatos részletekért lásd: a engedélyezte a szolgáltatási végpont a szolgáltatás dokumentációjában.
6. Az alhálózat hozzáadása a kiválasztott virtuális hálózat, válassza ki a **OK**.

**Parancsok**

- Az Azure CLI: [az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alhálózati beállítások módosítása

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a virtuális hálózat, amely tartalmazza az alhálózat beállításait módosítani szeretné.
3. A **beállítások**, jelölje be **alhálózatok**.
4. Az alhálózatok listájának megtekintéséhez jelölje ki az alhálózatot, beállításait módosítani szeretné. A következő beállításokat módosíthatja:

    - **Címtartomány:** nincsenek erőforrások az alhálózaton belül vannak telepítve, ha a címtartomány módosíthatja. Ha erőforrásokat az alhálózaton található, állítsa az erőforrások áthelyezése egy másik alhálózatot, vagy először törölje azokat az alhálózat. Áthelyezéséhez vagy az erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Megtudhatja, hogyan helyezze át vagy törölje az alhálózatok-erőforrást, olvassa el a dokumentációt, helyezze át vagy törölje kívánt minden erőforrástípus. Lásd: korlátait **-címtartományt** az 5. lépésében [adjon hozzá egy alhálózatot](#add-a-subnet).
    - **Felhasználók**: beépített szerepkörök vagy a saját egyéni szerepkörök használatával szabályozhatja a hozzáférést az alhálózathoz. Szerepkörök és a felhasználók hozzáférhessenek az alhálózat hozzárendelése kapcsolatos további információkért lásd: [az Azure-erőforrások hozzáférésének kezelése a szerepkör-hozzárendelés segítségével](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - További információt **hálózati biztonsági csoport**, **útvonaltábla**, **felhasználók**, és **szolgáltatás végpontjait**, tekintse meg az 5. lépés a [ Adjon hozzá egy alhálózatot](#add-a-subnet).
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Az Azure CLI: [az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Alhálózat törlése

Csak akkor, ha nincsenek erőforrások az alhálózatban lévő alhálózatot törölheti. Ha az alhálózatban lévő erőforrások, törölnie kell az erőforrást, amely az alhálózaton az alhálózat törlése előtt. Erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Megtudhatja, hogyan alhálózatok-erőforrást törölni, olvassa el a dokumentációt, minden erőforrástípus, hogy törölni szeretné.

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** jelennek meg a keresési eredmények között, válassza ki azt.
2. A virtuális hálózatok listájában jelölje ki a törölni kívánt alhálózat tartalmazó virtuális hálózaton.
3. A **beállítások**, jelölje be **alhálózatok**.
4. Az alhálózatok listájában jelölje ki **...** , a jobb oldalon, az alhálózat törölni kívánt
5. Válassza ki **törlése**, majd válassza ki **Igen**.

**Parancsok**

- Az Azure CLI: [az hálózati virtuális hálózat törlése](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

Alhálózatok feladatait, a fiókot hozzá kell rendelni a [hálózat közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkör vagy egy [egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkör, amely hozzá van rendelve a megfelelő engedélyekkel a következő táblázatban felsorolt:

|Művelet                                                                |   Művelet neve                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Virtuális hálózati alhálózat beolvasása                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Létrehozni vagy frissíteni a virtuális hálózati alhálózat      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Virtuális hálózati alhálózat törlése                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Csatlakozás a virtuális hálózat                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Csatlakozás a Service alhálózathoz                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Virtuális hálózati alhálózat virtuális gépeinek beolvasása  |
