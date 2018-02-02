---
title: "Hozzáadása, módosítása vagy egy Azure virtuális hálózati alhálózat törlése |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzáadása, módosítása vagy törlése a virtuális hálózati alhálózat az Azure-ban."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.openlocfilehash: 413ec2ef4fcc7752b95984a209818eeba535746e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Hozzáadása, módosítása vagy virtuális hálózati alhálózat törlése

Megtudhatja, hogyan hozzáadása, módosítása vagy virtuális hálózati alhálózat törlése. 

Ha még nem ismeri a virtuális hálózatok hozzáadása, módosítása vagy alhálózat törlése előtt, azt javasoljuk, hogy olvassa el [Azure Virtual Network áttekintése](virtual-networks-overview.md) és [létrehozása, módosítása vagy törlése a virtuális hálózati](virtual-network-manage-network.md). Egy virtuális hálózatot helyezett összes Azure-erőforrások egy alhálózatba a virtuális hálózaton belül vannak telepítve. Általában több alhálózatot a virtuális hálózaton belüli jönnek létre:
- **Alhálózatok közötti forgalmat**. Hálózati biztonsági csoportok alkalmazhatja az összes erőforrás (például virtuális gépek), a virtuális hálózat bejövő és kimenő hálózati forgalom szűrésére alhálózatokra. A hálózati biztonsági csoportok létrehozásával kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok létrehozása a](virtual-networks-create-nsg-arm-pportal.md).
- **Szabályozhatja az alhálózatok közötti útválasztást**. Azure alapértelmezett útvonalak hoz létre, így automatikusan továbbítódik alhálózatok között. Felhasználó által definiált útvonalak létrehozásával felülbírálhatja az Azure alapértelmezett útvonalak. Felhasználó által definiált útvonalak kapcsolatos további információkért lásd: [hozhat létre útvonalakat felhasználói](virtual-network-create-udr-arm-ps.md). 

Ez a cikk azt ismerteti, hogyan hozzáadása, módosítása és törlése az Azure Resource Manager telepítési modell használatával létrehozott virtuális hálózatok alhálózatot.
 
## <a name="before"></a>Előkészületek

Az ebben a cikkben ismertetett feladatok megkezdése előtt hajtsa végre a következő előfeltételek teljesülését:

- Ha most ismerkedik a virtuális hálózatok használata, azt javasoljuk, hogy tekintse át a gyakorlatban [az első Azure virtuális hálózat létrehozása](quick-create-portal.md). Ebben a gyakorlatban segítségével Megismerkedés a virtuális hálózatok.
- Virtuális hálózatok korlátairól kapcsolatos információkért tekintse át a [Azure korlátozását](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jelentkezzen be az Azure-portálon, az Azure parancssori eszköz (Azure CLI) vagy az Azure PowerShell használatával az Azure-fiókjával. Ha nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha azt tervezi, a jelen cikkben ismertetett feladatok végrehajtásához PowerShell-parancsok használata, először [Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Győződjön meg arról, hogy rendelkezik-e a legújabb verziója található az Azure PowerShell-parancsmagjai telepítve vannak-e. A példákban PowerShell-parancsok súgójának megjelenítéséhez írja be a következőt `get-help <command> -full`.
- Ha azt tervezi, a jelen cikkben ismertetett feladatok végrehajtásához Azure parancssori felület parancsai használni, először:
    - [Telepítse és konfigurálja az Azure parancssori felület](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Győződjön meg arról, hogy rendelkezik-e telepítve az Azure parancssori felület legújabb verziója.
    - Az Azure-felhőbe rendszerhéj használata. A parancssori felület és függőségeinek telepítése, helyett az Azure-felhő rendszerhéj is használhatja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. A felhő rendszerhéj használatához kattintson a felhő rendszerhéj (**> _**) ikonra a bal felső az Azure portálról. 

  Ha segítséget szeretne kérni az Azure parancssori felület parancsait, adja meg a `az <command> --help`.

## <a name="create-subnet"></a>Adjon hozzá egy alhálózatot

Egy alhálózat hozzáadása:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között kattintson **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózat hozzá szeretné adni egy alhálózat.
4. Kattintson a virtuális hálózat panel **alhálózatok**.
5. Kattintson a **+ alhálózati**.
6. Az a **alhálózat hozzáadása** panelen adja meg a következő paraméterekkel:
    - **Név**: A nevet a virtuális hálózaton belül egyedinek kell lennie.
    - **Címtartomány**: A tartományon a címtartomány a virtuális hálózaton belül egyedinek kell lennie. A tartomány más alhálózati címtartományt, a virtuális hálózaton belül nem lehet átfedésben. A címterület Classless Inter-Domain Routing (CIDR) jelölésrendszer szerint kell adni. Például cím terület 10.0.0.0/16 rendelkező virtuális hálózatban, megadhat egy alhálózat címtartománya a 10.0.0.0/24. A legkisebb megadható tartománya /29, biztosító nyolc IP-cím az alhálózat. Azure fenntartja az első és utolsó cím protokoll megfelelési minden alhálózatban. Három további címek az Azure szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen meghatározása egy /29 alhálózat cím a tartomány eredményezi, hogy az alhálózat három használható IP-címet. Ha azt tervezi, VPN-átjáró egy virtuális hálózathoz csatlakozni, létre kell hoznia egy átjáró-alhálózatot. További információ [adott cím tartomány szempontjai átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány meghatározott feltételek mellett az alhálózat hozzáadása után módosíthatja. A alhálózati címtartományt módosításáról további tudnivalókért lásd: [alhálózati beállítások módosítása](#change-subnet) ebben a cikkben.
    - **Hálózati biztonsági csoport**: opcionálisan társíthatja a meglévő hálózati biztonsági csoportot a bejövő és kimenő hálózati forgalmat az alhálózat szűrés alhálózat. A hálózati biztonsági csoport ugyanazt az előfizetést és a virtuális hálózat helyének léteznie kell. Azt is kell használatával hozható létre a Resource Manager üzembe helyezési modellben. A hálózati biztonsági csoportok létrehozásával kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok](virtual-networks-create-nsg-arm-pportal.md).
    - **Az útvonaltábla**: opcionálisan társíthatja egy meglévő útvonaltábla az alhálózat szabályozhatja a hálózati forgalmának irányítását a más hálózatokhoz. Az útvonal táblázatnak már léteznie kell az előfizetés és a virtuális hálózati helyen. Azt is kell használatával hozható létre a Resource Manager üzembe helyezési modellben. Az útvonaltáblák létrehozásával kapcsolatos további tudnivalókért lásd: [felhasználó által definiált útvonalak](virtual-network-create-udr-arm-ps.md).
    - **Felhasználók**: beépített szerepkörök vagy a saját egyéni szerepkörök használatával szabályozhatja a hozzáférést az alhálózathoz. Szerepkörök és a felhasználók hozzáférhessenek az alhálózat hozzárendelése kapcsolatos további információkért lásd: [az Azure-erőforrások hozzáférésének kezelése a szerepkör-hozzárendelés segítségével](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Az alhálózat hozzáadása a kiválasztott virtuális hálózat, kattintson a **OK**.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az alhálózaton virtuális hálózat létrehozása](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Alhálózati beállítások módosítása

Módosíthatja hálózati biztonsági csoportok, útvonaltábláit és a felhasználói hozzáférés alhálózathoz, amelyek az alhálózat-erőforrások kezelése. Ezek a beállítások tájékozódhat a [adjon hozzá egy alhálózatot](#create-subnet), lásd a 6. lépést. Ha egy alhálózat címtartománya a módosítani kívánt, először törölnie kell minden olyan erőforrásnál, amely az alhálózaton. Erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Megtudhatja, hogyan alhálózatok-erőforrást törölni, olvassa el a dokumentációt, minden erőforrástípus, hogy törölni szeretné. Az alhálózat címtartománya módosítása:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között kattintson **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózat, amelynek a alhálózati címtartományt módosítani szeretné.
4. Kattintson az alhálózat, amelynek a címtartomány módosítani szeretné.
5. Az alhálózati panel a a **-címtartományt** mezőbe írja be az új címtartományt. A tartomány a címtartomány a virtuális hálózaton belül egyedinek kell lennie. A tartomány más alhálózati címtartományt, a virtuális hálózaton belül nem lehet átfedésben. A címtartomány CIDR jelölésrendszer szerint kell adni. Például cím terület 10.0.0.0/16 rendelkező virtuális hálózatban, megadhat egy alhálózat címtartománya a 10.0.0.0/24. A legkisebb megadható tartománya /29, biztosító nyolc IP-cím az alhálózat. Azure fenntartja az első és utolsó cím protokoll megfelelési minden alhálózatban. Három további címek az Azure szolgáltatás használati számára vannak fenntartva. Ennek eredményeképpen a /29 alhálózat címtartománya három használható IP-címmel rendelkezik. Ha azt tervezi, VPN-átjáró egy virtuális hálózathoz csatlakozni, létre kell hoznia egy átjáró-alhálózatot. További információ [adott cím tartomány szempontjai átjáró alhálózatok](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány meghatározott feltételek mellett az alhálózat létrehozása után módosíthatja. A alhálózati címtartományt módosításáról további tudnivalókért lásd: [alhálózati beállítások módosítása](#change-subnet) ebben a cikkben.
6. Kattintson a **Save** (Mentés) gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az hálózat vnet alhálózat frissítése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Alhálózat törlése

Csak akkor, ha nincsenek erőforrások az alhálózatban lévő alhálózatot törölheti. Ha az alhálózatban lévő erőforrások, törölnie kell az erőforrást, amely az alhálózaton az alhálózat törlése előtt. Erőforrás törlése lépései eltérőek attól függően, hogy az erőforrás. Megtudhatja, hogyan alhálózatok-erőforrást törölni, olvassa el a dokumentációt, minden erőforrástípus, hogy törölni szeretné. Egy alhálózat törléséhez:

1. Jelentkezzen be a [portal](https://portal.azure.com) egy olyan fiókkal, amely hozzá van rendelve a hálózat közreműködő szerepkört az előfizetés (minimum) engedélyeit. Szerepkörök és engedélyek hozzárendelése a fiókok kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. A portál keresési mezőbe, írja be a **virtuális hálózatok**. A keresési eredmények között kattintson **virtuális hálózatok**.
3. Az a **virtuális hálózatok** panelen kattintson a virtuális hálózati alhálózat a törölni kívánt.
4. A virtuális hálózati panelen a **beállítások**, kattintson a **alhálózatok**.
5. Az alhálózatok listájának az alhálózatok paneljén megjelenő, kattintson a jobb gombbal a törléséhez kattintson a kívánt alhálózat **törlése**, és kattintson a **Igen** törölni az alhálózatot.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|Azure CLI|[az hálózati virtuális hálózat törlése](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Következő lépések

A virtuális gép egy alhálózat létrehozásához lásd: [hozzon létre egy virtuális hálózatot és az alhálózatban lévő virtuális gépek telepítése](quick-create-portal.md#create-virtual-machines).
