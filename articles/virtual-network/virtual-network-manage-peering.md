---
title: "Hozzon létre módosítsa, törölje vagy egy Azure virtuális hálózati társviszony-létesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan létrehozása, módosítása vagy törlése a virtuális hálózati társviszony-létesítés."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial;anavin
ms.openlocfilehash: a8d5c3675d51426a8e4619a3ae2133eccae69b75
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Létrehozása, módosítása vagy törlése a virtuális hálózati társviszony-létesítés

Megtudhatja, hogyan létrehozása, módosítása vagy törlése a virtuális hálózati társviszony-létesítés. Virtuális hálózati társviszony-létesítés lehetővé teszi a virtuális hálózatok csatlakoztatása az Azure gerincét a hálózaton keresztül. Amennyiben nincsenek társviszonyban, a rendszer továbbra is kezeli a virtuális hálózatok, külön erőforrások. Ha még nem ismeri a virtuális hálózati társviszony-létesítést, ajánlott olvasási a [virtuális hálózati társviszony-létesítési – áttekintés](virtual-network-peering-overview.md) befejezése és a [hozzon létre egy virtuális hálózati társviszony-létesítési oktatóanyag](tutorial-connect-virtual-networks-portal.md), befejezése előtt a Ez a cikk feladatokat.

Az azonos régiókban lévő virtuális hálózatok közötti társviszony kialakítása általánosan elérhető. Virtuális hálózatok különböző régiókban jelenleg előzetes verzióban érhetők társviszony. Lásd: [virtuális hálózati frissítések](https://azure.microsoft.com/updates/?product=virtual-network) az elérhető régiók. Meg kell [az előfizetés regisztrálása a előzetes](tutorial-connect-virtual-networks-powershell.md#register).

> [!WARNING]
> Az ezzel a forgatókönyvvel létrehozott virtuális társhálózatok rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadások forgatókönyveitől. Előfordulhat, hogy a virtuális társhálózatok korlátozott képességekkel rendelkeznek, vagy nem érhetőek el minden Azure-régióban. A szolgáltatás rendelkezésre állásával és állapotával kapcsolatos legfrissebb értesítésekért tekintse meg az [Azure virtuális hálózati frissítésekkel kapcsolatos](https://azure.microsoft.com/updates/?product=virtual-network) oldalát.
>

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bármely szakaszának lépéseit befejezése előtt hajtsa végre a következőket:

- Ha még nem rendelkezik Azure-fiókja, regisztráljon egy [ingyenes próbafiók](https://azure.microsoft.com/free).
- Ha a portál használatával, nyissa meg a https://portal.azure.com, és jelentkezzen be az Azure-fiókjával.
- Ha a PowerShell-parancsokkal ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.2.0 szükséges vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` telepített verziója található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez az oktatóanyag az Azure parancssori felület 2.0.26 verziója szükséges, vagy később. Futtatás `az --version` telepített verziója található. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure parancssori felület, is futtatásához szükséges `az login` az Azure VPN-kapcsolat létrehozásához.

## <a name="create-a-peering"></a>A társviszony-létesítés létrehozása

>[!NOTE]
>Mielőtt létrehozna egy társviszony-létesítést, ellenőrizze a már familiarized, akkor saját kezűleg a a [követelményeket és korlátokat](#requirements-and-constraints) és [szükséges engedélyek](#permissions).
>

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** megjelenik a keresési eredmények között, jelölje be. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listában nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózati társviszony.
2. Válassza ki a virtuális hálózatot szeretne létrehozni a társviszony-létesítés a listában szereplő.
3. A virtuális hálózatok listájában jelölje ki a virtuális hálózatot szeretne létrehozni a társviszony-létesítés esetében.
4. A **beállítások**, jelölje be **Társviszony**.
5. Válassza ki **+ Hozzáadás**. 
6. <a name="add-peering"></a>Adja meg, vagy válassza ki a következő beállítások értékeit:
    - **Name:** a társviszony-létesítést a nevet a virtuális hálózaton belül egyedinek kell lennie.
    - **Virtuális hálózat üzembe helyezési modellt:** válassza ki, melyik központi telepítési modell társkapcsolatot létesíteni kívánt virtuális hálózat keresztül telepítve lett.
    - **Tudható, hogy az erőforrás-azonosító:** Ha társkapcsolatot létesíteni kívánt virtuális hálózat olvasási hozzáféréssel rendelkezik, hagyja üresen ezt a jelölőnégyzetet bejelölve. Ha nincs olvasási hozzáférés a virtuális hálózat vagy társkapcsolatot létesíteni kívánt előfizetést, ezt a négyzetet. Adja meg a teljes erőforrás-azonosítója a virtuális hálózat társkapcsolatot létesíteni a kívánt a **erőforrás-azonosító** jelölőnégyzet, amelynek jelent meg, ha a lista be van jelölve. Azt adja meg az erőforrás-azonosító már szerepel az azonos Azure virtuális hálózat kell [régió](https://azure.microsoft.com/regions) , ez a virtuális hálózat. Ha olyan virtuális hálózatot válasszon egy másik régióban szeretné [az előfizetés regisztrálása a az előzetes kiadásban.](tutorial-connect-virtual-networks-portal.md) A teljes erőforrás-azonosítója a következő hasonlít<Id>/resourceGroups/ < erőforráscsoport--neve > /providers/Microsoft.Network/virtualNetworks/ < virtuális-hálózati-neve >. Az erőforrás-azonosítója a virtuális hálózat tulajdonságait megtekintve kaphat virtuális hálózatot. A virtuális hálózat tulajdonságainak megtekintéséhez, lásd: [virtuális hálózatok kezeléséhez](virtual-network-manage-network.md#view-virtual-networks-and-settings).
    - **Előfizetés:** válassza ki a [előfizetés](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) társkapcsolatot létesíteni kívánt virtuális hálózat. Egy vagy több előfizetés is szerepelnek, attól függően, hogy hány előfizetések olvasási hozzáféréssel rendelkezik fiókjába. Ha bejelölte a **erőforrás-azonosító** jelölőnégyzetét, ez a beállítás nem érhető el.
    - **Virtuális hálózat:** válassza ki a társkapcsolatot létesíteni kívánt virtuális hálózatot. Kiválaszthatja, hogy vagy az Azure-telepítés modell használatával létrehozott virtuális hálózatban. Ha olyan virtuális hálózatot válasszon egy másik régióban szeretné [az előfizetés regisztrálása a az előzetes kiadásban.](tutorial-connect-virtual-networks-portal.md) Ahhoz, hogy a listában láthatók a virtuális hálózat olvasási hozzáféréssel kell rendelkeznie. Ha egy virtuális hálózat szerepel a listában, de szürkén jelenik meg, mert a virtuális hálózat a címtér átfedésben van a virtuális hálózat a címtér lehet. Virtuális hálózati címterük hozzon létre, ha azok nem társítottak. Ha bejelölte a **erőforrás-azonosító** jelölőnégyzetét, ez a beállítás nem érhető el.
    - **Virtuális hálózati hozzáférés engedélyezése:** válasszon **engedélyezve** (alapértelmezett), ha azt szeretné, hogy a két virtuális hálózatok közötti kommunikáció engedélyezésére. Virtuális hálózatok közötti kommunikáció lehetővé teszi, hogy kommunikálni egymással sávszélesség és a késés, mintha ugyanahhoz a virtuális hálózathoz csatlakoztatva vagy virtuális hálózati erőforrásokhoz. A két virtuális hálózatokon lévő erőforrások közötti összes kommunikáció a Azure magánhálózaton keresztül van. A **VirtualNetwork** alapértelmezett címke a hálózati biztonsági csoportok magában foglalja a virtuális hálózat és a virtuális hálózat társviszonyban. Hálózati biztonsági csoport alapértelmezett címkékkel kapcsolatos további tudnivalókért olvassa el a [hálózati biztonsági csoportok – áttekintés](virtual-networks-nsg.md#default-tags) cikk.  Válassza ki **letiltott** Ha nem szeretné, hogy a peered virtuális hálózati forgalmat. Kiválaszthatja **letiltott** Ha egy másik virtuális hálózatot a virtuális hálózat már társviszonyban, de időnként le szeretné tiltani a két virtuális hálózatok közötti adatforgalom. Előfordulhat, hogy törli, majd újra létrehozza a társviszony mint kényelmesebb engedélyezése vagy tiltása. Ha ez a beállítás le van tiltva, a forgalom peered virtuális hálózatok közötti nem folyik.
    - **Továbbított forgalom engedélyezése:** jelölőnégyzet bejelölésével engedélyezi a forgalmat *továbbított* által a hálózati virtuális készülék (amely a virtuális hálózat nem származik) virtuális hálózatban a virtuális hálózathoz keresztül társviszony-létesítés folyamokhoz . Vegye figyelembe például Spoke1, Spoke2 és Hub nevű három virtuális hálózatok. Minden egyes küllős virtuális hálózat és a központ virtuális hálózat között társviszony-létesítés létezik, de esetében nem léteznek a küllős virtuális hálózatok között. A hálózati virtuális készülék telepítve van a központ virtuális hálózaton, és a felhasználó által definiált útvonalak minden küllős virtuális hálózat, amely a hálózati virtuális készülék keresztül az alhálózatok közötti forgalmat is vonatkozik. Ezt a jelölőnégyzetet a rendszer nem ellenőrzi a társviszony-létesítés minden küllős virtuális hálózat és a központ virtuális hálózat között, ha forgalmat a nem flow küllős virtuális hálózatok közötti, mert a központ továbbító a forgalom a virtuális hálózatok között. Ez a funkció lehetővé teszi, hogy a továbbított forgalmat a társviszony-létesítést, amíg azt nem hoz létre bármely felhasználó által definiált útvonalak vagy a virtuális hálózati készülékek. Felhasználó által definiált útvonalak és hálózati virtuális készülékek külön hoz létre. További tudnivalók [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md#user-defined). Nem kell ellenőrizze ezt a beállítást, ha a virtuális hálózatok az Azure VPN Gateway között továbbított forgalmat.
    - **Átjáró átvitel engedélyezése:** ezt a jelölőnégyzetet, ha a virtuális hálózathoz csatlakozó virtuális hálózati átjáró, és szeretné engedélyezni az átjárón keresztül peered virtuális hálózati forgalmát. Ez a virtuális hálózat például egy helyi hálózaton keresztül a virtuális hálózati átjáró is kapcsolódhat. Az átjáró lehet egy expressroute-on vagy VPN-átjárót. A jelölőnégyzet bejelölésével lehetővé teszi, hogy az átjáró, a virtuális hálózathoz, a helyszíni hálózathoz csatlakoztatott áramlása érdekében peered virtuális hálózati forgalmát. Ha bejelöli a jelölőnégyzetet, a peered virtuális hálózat konfigurált átjárók nem rendelkezhet. A peered virtuális hálózatnak rendelkeznie kell a **távoli átjárókat használ** jelölőnégyzet be van jelölve, a többi virtuális hálózati társviszony a virtuális hálózathoz való beállítása során. Ha nem adja meg ezt a jelölőnégyzetet bejelölve (alapértelmezett), a virtuális hálózat a virtuális hálózaton nincsenek társviszonyban, továbbra is folyamok forgalom, de nem folyik a virtuális hálózathoz csatlakozó virtuális hálózati átjárón keresztül. 
    
    Mellett egy a helyszíni hálózat felé irányuló forgalom továbbítása, a VPN-átjáró továbbíthatja hálózati forgalom virtuális hálózatok között, amelyek nincsenek társviszonyban, amelyek az átjáró le van, a virtuális hálózatok társítottak, egymás mellett kell nélkül a virtuális hálózattal. Ez akkor hasznos, ha azt szeretné, VPN-átjáró használata hubot (a ismertetett küllős példa **továbbított forgalom engedélyezése**), amelyek nincsenek társviszonyban egymással küllős virtuális hálózatok közötti forgalom virtuális hálózati. További információ [virtuális hálózati átjárók](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Ebben a forgatókönyvben igényel, adja meg a virtuális hálózati átjáró a következő ugrás típusa, felhasználó által definiált útvonalakat megvalósítása. További tudnivalók [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md#user-defined). Csak VPN-átjáró megadhatja a következő ugrás típusa, a felhasználó által megadott útvonal, a következő ugrás típusa, a felhasználó által megadott útvonal nem adható meg egy ExpressRoute-átjárót.

        You cannot enable this option if you're peering a virtual network (Resource Manager) with a virtual network (classic). Though the traffic flows between the two virtual networks, the virtual network (classic) traffic cannot flow through a network gateway attached to the virtual network (Resource Manager). 

    - **Távoli átjárókat használ:** jelölőnégyzet bejelölésével engedélyezi, hogy társviszony-létesítés a virtuális hálózathoz csatlakozó virtuális hálózati átjáró áramlása érdekében a virtuális hálózati forgalmat. Például a virtuális hálózat, hogy társviszony-létesítés rendelkezik csatolt VPN-átjáró, amely lehetővé teszi a kommunikációt a helyszíni hálózathoz.  A jelölőnégyzet bejelölésével lehetővé teszi, hogy a VPN-átjáró peered a virtuális hálózathoz áramlása érdekében a virtuális hálózati forgalmát. Ha bejelöli a jelölőnégyzetet, a peered virtuális hálózathoz rendelkeznie kell a virtuális hálózati átjáró csatlakozik, és rendelkeznie kell a **átjáró átvitel engedélyezése** jelölőnégyzet be van jelölve. Ha ezt a jelölőnégyzetet bejelölve (alapértelmezett) hagyja, a virtuális hálózat továbbra is áramolhasson az adatforgalom a peered virtuális hálózatról, de nem a virtuális hálózati átjáró keresztül bonyolódjon csatolni a virtuális hálózat. 
Ehhez a virtuális hálózathoz csak egy társviszony, lehet, hogy ez a beállítás engedélyezve van.
Ez a beállítás nem használható, ha már van konfigurálva a virtuális hálózati átjáró.
        Nem engedélyezi ezt a beállítást, ha egy virtuális hálózat (klasszikus) rendelkező virtuális hálózatban (erőforrás-kezelő) még társviszony. Bár a forgalmat a két virtuális hálózatok között, a virtuális hálózati (erőforrás-kezelő) forgalomnak a hálózati átjáró (klasszikus) a virtuális hálózathoz nem áramlása.

7. Kattintson a **OK** az alhálózat hozzáadása a kiválasztott virtuális hálózat gomb.

### <a name="commands"></a>Parancsok

- Az Azure CLI: [az hálózati vnetben társviszony-létesítés létrehozása](/cli/azure/network/vnet/peering#create)
- PowerShell: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

### <a name="scenarios"></a>Forgatókönyvek

A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Hajtsa végre az alábbi esetek valamelyike részletes oktatóanyaga:
 
|Azure üzembehelyezési modell  | Előfizetés  |
|---------|---------|
|Mindkét Resource Manager |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
| |[Különböző](create-peering-different-subscriptions.md)|
|Egy Resource Manager, egy klasszikus     |[Ugyanaz](create-peering-different-deployment-models.md)|
| |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

## <a name="view-or-change-peering-settings"></a>Társviszony-létesítési beállításainak megtekintése vagy módosítása

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** megjelenik a keresési eredmények között, jelölje be. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listában nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózati társviszony.
2. Válassza ki a virtuális hálózati társviszony-létesítési beállításait módosítani szeretné a listában.
3. A virtuális hálózatok listájában jelölje ki a virtuális hálózati társviszony-létesítési beállításait módosítani szeretné.
4. A **beállítások**, jelölje be **Társviszony**.
5. Kattintson a társviszony-létesítés szeretné a beállításainak megtekintése vagy módosítása.
6. Módosítsa a megfelelő beállítást. Olvassa el az egyes beállítások a beállítások [6. lépés](#add-peering) Create társviszony-létesítés. 

    >[!NOTE]
    >Mielőtt létrehozna egy társviszony-létesítést, ellenőrizze a már familiarized, akkor saját kezűleg a a [követelményeket és korlátokat](#requirements-and-constraints) és [szükséges engedélyek](#permissions).
    >

7. Kattintson a **Save** (Mentés) gombra.

**Parancsok**

Az Azure CLI: [az vnetben társviszony-létesítési lista](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) a lista esetében a virtuális hálózat [az hálózati vnetben társviszony-létesítési megjelenítése](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) beállítások megjelenítése az adott társviszony-létesítés, és [az hálózati vnetben társviszony-létesítési frissítés ](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) társviszony-létesítési beállításainak módosításához. |}
- PowerShell: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) beolvasása társviszony-létesítési beállításainak megjelenítése és [Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) beállításainak módosításához.

## <a name="delete-a-peering"></a>Törölje a társviszony-létesítés

Társviszony törlődik, ha a virtuális hálózat már nem a forgalom a peered virtuális hálózathoz. Virtuális hálózatok Resource Manager használatával telepített társviszonyban van, a minden egyes virtuális hálózati rendelkezik, a társviszony-létesítést úgy a virtuális hálózat. Ha egy virtuális hálózati társviszony törlése letiltja a virtuális hálózatok közötti kommunikáció, nem törli a virtuális hálózati társviszony. A társviszony-létesítési állapota a társviszony-létesítés már szerepel a virtuális hálózat **Disconnected**. Ön nem hozza létre újból a társviszony-létesítést csak akkor hozza létre újból a társviszony-létesítést az első virtuális hálózat és a társviszony-létesítési állapot mindkét virtuális hálózat módosításainak *csatlakoztatva*. 

Ha azt szeretné, hogy a virtuális hálózatok néha kommunikációra, de nem mindig társviszony-létesítés törléssel állíthatja be a **virtuális hálózati hozzáférés engedélyezése** beállítást **letiltott** helyette. Megtudhatja, hogyan, olvassa el a 6. lépésében a [létrehozása a társviszony-létesítés](#create-peering) című szakaszát. Előfordulhat, hogy letiltásával és egyszerűbb, mint törlése és újbóli létrehozása esetében a hálózati hozzáférés engedélyezéséhez.

1. Adja meg a keresési mezőbe, a portál felső, *virtuális hálózatok* be a keresőmezőbe. Ha **virtuális hálózatok** megjelenik a keresési eredmények között, jelölje be. Ne válassza **virtuális hálózatok (klasszikus)** Ha, megjelenik a listában nem hozható létre, a klasszikus üzembe helyezési modellben telepített virtuális hálózati társviszony.
2. Jelölje ki a virtuális hálózati társviszony-létesítés a törölni kívánt listájában.
3. Válassza ki a virtuális hálózati társviszony-létesítés a törölni kívánt virtuális hálózatok listája.
4. A **beállítások**, jelölje be **Társviszony**.
5. A jobb oldalon a törölni kívánt társviszony, válassza ki a **...** , jelölje be **törlése**, majd jelölje be **Igen** törli az első virtuális hálózat.
6. Az előző lépéseket a többi virtuális hálózati társviszony-létesítés törli.

**Parancsok**

- Az Azure CLI: [az hálózati vnetben társviszony törlése](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- PowerShell: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások 

- A virtuális hálózatok partnert, rendelkeznie kell egymást nem átfedő IP-címterületeken.
- Nem adja hozzá a címtartományt, vagy nem címtartomány törlése egy virtuális hálózat címtartományán, ha egy virtuális hálózathoz nincsenek társviszonyban, egy másik virtuális hálózathoz. Vegye fel vagy távolítsa el a címtartomány, törli, hozzáadásához vagy távolítsa el a-címtartományokat, majd hozza újra létre a társviszony-létesítést. -Címtartományokat adja hozzá, vagy távolítsa el a címtartomány a virtuális hálózatok, lásd: [virtuális hálózatok kezeléséhez](virtual-network-manage-network.md).
- Erőforrás-kezelő vagy a Resource Manager használatával telepített egy virtuális hálózattal, a klasszikus üzembe helyezési modellben telepített virtuális hálózaton keresztül telepített két virtuális hálózat is partnert. Két, a klasszikus üzembe helyezési modell használatával létrehozott virtuális hálózatok nem partnert. Ha nem ismeri az Azure üzembe helyezési modellel, olvassa el a [megértéséhez Azure üzembe helyezési modellel](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikk. A [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) használatával összeköthető két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat.
- Két, Resource Managerrel létrehozott virtuális hálózat közötti társviszony létrehozása esetén mindkét érintett virtuális hálózathoz konfigurálni kell egy társítást. 
    - *Kezdeményezett:* a társviszony-létesítést úgy a második virtuális hálózat első virtuális hálózati létrehozásakor a társviszony-létesítési állapot: *kezdeményezett*. 
    - *Csatlakoztatva:* a második virtuális hálózati társviszony az első virtuális hálózat létrehozásakor társviszony-létesítési állapotú-e *csatlakoztatva*. Ha az első virtuális hálózati társviszony-létesítési állapotának megtekintéséhez állapotának változása látható *kezdeményezett* való *csatlakoztatva*. A társviszony-létesítést nincs sikeresen létrehozva mindaddig, amíg a társviszony-létesítési állapota mindkét virtuális hálózati társviszony *csatlakoztatva*.
- Ha keresztül erőforrás-kezelőt hozott létre a klasszikus üzembe helyezési modell használatával létrehozott virtuális hálózatban a virtuális hálózati társviszony, csak konfigurálnia a Resource Manager használatával telepített virtuális hálózati társviszony. A virtuális hálózat (klasszikus) vagy a klasszikus üzembe helyezési modellben telepített két virtuális hálózatok közötti társviszony nem konfigurálható. A virtuális hálózat (klasszikus) (erőforrás-kezelő) virtuális hálózati társviszony létrehozásakor a társviszony-létesítési állapot: *Frissítéskísérleti*, majd hamarosan vált *csatlakoztatva*.
- Társviszony-létesítés létrejön a virtuális hálózatok között. Nincsenek tranzitív esetében. Ha létre közötti esetében:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Nincs nincs társviszony-létesítés VirtualNetwork1 és VirtualNetwork3 keresztül VirtualNetwork2 között. Ha szeretne létrehozni egy virtuális hálózati társviszony-létesítés VirtualNetwork1 és VirtualNetwork3 között, kell létrehoznia a társviszony-létesítés VirtualNetwork1 és VirtualNetwork3 között.
- Alapértelmezett Azure névfeloldás használó társítottak, virtuális hálózatok neveinek nem oldható fel. Más virtuális hálózatok neveinek feloldásához, egy egyéni DNS-kiszolgálót kell használnia. A saját DNS-kiszolgáló beállításáról, olvassa el a [névfeloldáshoz a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) cikk.
- Mindkét virtuális hálózat társviszony-létesítés erőforrásokat is kommunikálhatnak egymással sávszélesség és a késés, mintha ugyanahhoz a virtuális hálózatban. Minden virtuális gép méretét azonban rendelkezik saját maximális hálózati sávszélesség. Ha többet szeretne megtudni a különböző méretű virtuális gépek maximális hálózati sávszélességével kapcsolatban, olvassa el a [windowsos](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [linuxos](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretéről szóló cikkeket.
- Virtuális hálózatok Resource Manager használatával telepített az ugyanazon vagy másik előfizetések is partnert.
- Különböző üzembe helyezési modellel, amelyek az ugyanazon vagy másik előfizetésekhez keresztül telepített virtuális hálózatot is partnert. 
- Az előfizetések, amelyek mindkét virtuális hálózat az azonos Azure Active Directory-bérlő társítva kell lennie. Ha még nem rendelkezik az AD-bérlő, akkor gyorsan [hozzon létre egyet](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch). Használhatja a [VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) két virtuális hálózatokat társított Active Directory különböző bérlők különböző előfizetésekhez csatlakozni.
- Egy virtuális hálózatot is társítottak, egy másik virtuális hálózathoz, és is csatlakoztatható egy Azure virtuális hálózati átjárót egy másik virtuális hálózathoz. Ha a virtuális hálózatok társviszony-létesítés és egy átjárón keresztül csatlakoznak, a virtuális hálózatok közötti forgalom áthaladó a társviszony-létesítési konfiguráció ahelyett, hogy az átjáró.
- Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="permissions"></a>Engedélyek

A fiókokat hozhat létre a virtuális hálózati társviszony-létesítés a szükséges szerepkör vagy az engedélyekkel kell rendelkeznie. Például, ha két virtuális hálózatok myVnetA és myVnetB nevű volt társviszony, a fiókjához társítva kell lenni a következő minimális szerepkör vagy az engedélyekkel minden virtuális hálózathoz:
    
|Virtuális hálózat|Üzemi modell|Szerepkör|Engedélyek|
|---|---|---|---|
|myVnetA|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|–|
|myVnetB|Resource Manager|[Hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasszikus|[Klasszikus hálózati közreműködő](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

További információ [beépített szerepkörök](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) és rendelje hozzá adott engedélyeket [egyéni szerepkörök](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (csak Resource Manager).

## <a name="next-steps"></a>További lépések

További információ a [küllős hálózati topológiák](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) létrehozásáról
