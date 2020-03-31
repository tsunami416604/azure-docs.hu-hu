---
title: Azure virtuális hálózati társviszony-létesítésének létrehozása, módosítása vagy törlése | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózati társviszony-létesítést.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 97acac61d0397a4e13fb64d39a6aba92e4de2afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123301"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Virtuális hálózati társviszony-létesítés létrehozása, módosítása vagy törlése

Ismerje meg, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózati társviszony-létesítést. A virtuális hálózati társviszony-létesítés lehetővé teszi, hogy az Azure gerinchálózaton keresztül virtuális hálózatokat csatlakoztasson ugyanabban a régióban és a régiók (más néven globális virtuális hálózati társviszony-létesítés) között. A társviszony-létesítést követően a virtuális hálózatok továbbra is külön erőforrásként lesznek kezelve. Ha most ismerkedik a virtuális hálózati társviszony-létesítés, akkor többet tud meg róla a [virtuális hálózat társviszony-létesítés áttekintést,](virtual-network-peering-overview.md) vagy egy [oktatóanyag](tutorial-connect-virtual-networks-portal.md)befejezése .

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely részében ismertetett lépések végrehajtása előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy [ingyenes próbafiókot.](https://azure.microsoft.com/free)
- Ha a portált https://portal.azure.comhasználja, nyissa meg a és jelentkezzen be olyan fiókkal, amely rendelkezik a társtársi kapcsolatokkal való munkához [szükséges engedélyekkel.](#permissions)
- Ha a PowerShell-parancsok at feladatok elvégzéséhez ebben a cikkben, vagy futtassa a parancsokat az [Azure Cloud Shell,](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ez az oktatóanyag az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Connect-AzAccount` olyan fiókkal is futtatnia kell, amely rendelkezik a társviszony-létesítéshez [szükséges engedélyekkel,](#permissions) hogy kapcsolatot hozzon létre az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez az oktatóanyag az Azure CLI 2.0.31-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI helyileg fut, akkor `az login` is kell futtatni egy fiókot, amely rendelkezik a [szükséges engedélyekkel](#permissions) való társviszony-létesítés, az Azure-ral való kapcsolat létrehozásához.

A fiók, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködői](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [olyan egyéni szerepkörhöz,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) amely az [Engedélyek](#permissions)listában felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-peering"></a>Társviszony-létesítés létrehozása

Társviszony-létesítés létrehozása előtt ismerkedjen meg a követelményekkel, korlátozásokkal és a [szükséges engedélyekkel.](#permissions)

1. Az Azure Portal tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt. Ne válassza a **virtuális hálózatok (klasszikus),** ha megjelenik a listában, mivel nem hozhat létre társviszony-létesítést a klasszikus telepítési modellen keresztül telepített virtuális hálózatról.
2. Jelölje ki azt a virtuális hálózatot a listában, amelyhez társviszony-létesítést szeretne létrehozni.
3. A **BEÁLLÍTÁSOK csoportban**válassza **a Társviszony-létesítések**lehetőséget.
4. Válassza a **+ Hozzáadás** lehetőséget. 
5. <a name="add-peering"></a>Adja meg vagy jelölje ki az értékeket a következő beállításokhoz:
    - **Név:** A társviszony-létesítés nevének egyedinek kell lennie a virtuális hálózaton belül.
    - **Virtuális hálózat telepítési modellje:** Válassza ki, hogy melyik üzembe helyezési modellen keresztül a kívánt virtuális hálózatot telepítették.
    - **Ismerem az erőforrás-azonosítómat:** Ha olvasási hozzáférése van ahhoz a virtuális hálózathoz, amelyhez társviszonyt kíván létesíteni, hagyja bejelölve ezt a jelölőnégyzetet. Ha nem rendelkezik olvasási hozzáféréssel ahhoz a virtuális hálózathoz vagy előfizetéshez, amelyhez társviszonyt kíván létesíteni, jelölje be ezt a jelölőnégyzetet. Adja meg annak a virtuális hálózatnak a teljes **Resource ID** erőforrás-azonosítóját, amelya jelölőnégyzet bejelölésétjelzőként jelent meg. A megadott erőforrás-azonosítónak olyan virtuális hálózathoz kell tartaszta, amely ugyanabban a virtuális azure-régióban létezik, vagy támogatja a virtuális hálózattal [azonos különböző](#requirements-and-constraints) Azure-régiót. [region](https://azure.microsoft.com/regions) A teljes erőforrás-azonosító `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`a hoz hasonló. A virtuális hálózat erőforrás-azonosítóját a virtuális hálózat tulajdonságainak megtekintésével szerezheti be. A virtuális hálózatok tulajdonságainak megtekintéséről a [Virtuális hálózatok kezelése című](manage-virtual-network.md#view-virtual-networks-and-settings)témakörben olvashat. Ha az előfizetés egy másik Azure Active Directory-bérlőhöz van társítva, mint a virtuális hálózattal való előfizetés, amelyből a társviszony-létesítést létrehozza, először adjon hozzá egy felhasználót minden bérlőből [vendégfelhasználóként](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) a szemközti bérlőben.
    - **Előfizetés:** Válassza ki annak a virtuális hálózatnak az [előfizetését,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) amelyhez társviszonyt kíván létesíteni. Egy vagy több előfizetés szerepel a listában, attól függően, hogy a fiók hány előfizetéshez rendelkezik olvasási hozzáféréssel. Ha bejelölte az **Erőforrás-azonosító jelölőnégyzetet,** ez a beállítás nem érhető el.
    - **Virtuális hálózat:** Jelölje ki azt a virtuális hálózatot, amelyhez társviszonyt kíván létesíteni. Kiválaszthatja a virtuális hálózat által létrehozott vagy Az Azure üzembe helyezési modell. Ha egy másik régióban szeretne virtuális hálózatot választani, akkor egy támogatott régióban kell kijelölnie egy virtuális [hálózatot.](#cross-region) Ahhoz, hogy az látható legyen a listában, olvasási hozzáféréssel kell rendelkeznie a virtuális hálózathoz. Ha egy virtuális hálózat szerepel a listában, de szürkén jelenik meg, annak az lehet az oka, hogy a virtuális hálózat címterülete átfedésben van a virtuális hálózat címterével. Ha a virtuális hálózati címterek átfedik egymást, nem társviszony- és társviszony-létesítési. Ha bejelölte az **Erőforrás-azonosító jelölőnégyzetet,** ez a beállítás nem érhető el.
    - **Virtuális hálózati hozzáférés engedélyezése:** Válassza **az Engedélyezve** (alapértelmezett) lehetőséget, ha engedélyezni szeretné a kommunikációt a két virtuális hálózat között. A virtuális hálózatok közötti kommunikáció engedélyezése lehetővé teszi, hogy a virtuális hálózathoz csatlakoztatott erőforrások ugyanolyan sávszélességgel és késleltetéssel kommunikáljanak egymással, mintha ugyanahhoz a virtuális hálózathoz csatlakoznának. A két virtuális hálózat erőforrásai közötti összes kommunikáció az Azure magánhálózaton keresztül történik. A virtuális hálózat biztonsági csoportjainak **VirtualNetwork** szolgáltatáscímkéje magában foglalja a virtuális hálózatot és a társviszonyba vett virtuális hálózatot. Ha többet szeretne tudni a hálózati biztonsági csoport szolgáltatáscímkéiről, olvassa el a [Hálózati biztonsági csoportok – áttekintés című témakört.](security-overview.md#service-tags) Válassza **a Letiltva** lehetőséget, ha nem szeretné, hogy a forgalom a társviszony-létesített virtuális hálózatra folyjon. A **Letiltva lehetőséget választhatja,** ha egy virtuális hálózatot egy másik virtuális hálózattal létesített, de időnként le szeretné tiltani a két virtuális hálózat közötti forgalmat. Előfordulhat, hogy az engedélyezés/letiltás sokkal kényelmesebb, mint a társviszony-létesítések törlése és újbóli létrehozása. Ha ez a beállítás le van tiltva, a forgalom nem áramlik a társviszony-létesített virtuális hálózatok között.
    - **Továbbított forgalom engedélyezése:** Jelölje be ezt a *jelölőnégyzetet,* ha azt szeretné, hogy a virtuális hálózatban lévő hálózati virtuális berendezés által továbbított forgalom (amely nem a virtuális hálózatból származik) egy társviszony-létesítésen keresztül továbbíthassa a virtuális hálózatot. Vegyünk például három, Küllő1, Küllő2 és Hub nevű virtuális hálózatot. Az egyes küllővirtuális hálózatok és a Hub virtuális hálózat között létezik társviszony-létesítés, de a küllővirtuális hálózatok között nem létezik társviszony-létesítés. A hálózati virtuális berendezés telepítve van a Hub virtuális hálózaton, és a felhasználó által definiált útvonalak at minden küllővirtuális hálózatra alkalmaznak, amely az alhálózatok közötti forgalmat a hálózati virtuális berendezésen keresztül irányítja. Ha ez a jelölőnégyzet nincs bejelölve az egyes küllővirtuális hálózatok és a központi virtuális hálózat közötti társviszony-létesítéshez, a forgalom nem áramlik a küllővirtuális hálózatok között, mert a hub nem továbbítja a forgalmat a virtuális hálózatok között. Bár ezt a funkciót engedélyezve lehetővé teszi a továbbított forgalmat a társviszony-létesítés, nem hoz létre felhasználó által definiált útvonalak vagy hálózati virtuális készülékek. A felhasználó által definiált útvonalak és a hálózati virtuális készülékek külön jönnek létre. További információ a [felhasználó által definiált útvonalakról.](virtual-networks-udr-overview.md#user-defined) Nem kell ellenőriznie ezt a beállítást, ha a forgalom továbbítása a virtuális hálózatok között egy Azure VPN-átjárón keresztül.
    - **Átjáró-átvitel engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha virtuális hálózati átjáró van csatlakoztatva ehhez a virtuális hálózathoz, és engedélyezni szeretné, hogy a társviszony-létesített virtuális hálózatból érkező forgalom átfolyjon az átjárón. Ez a virtuális hálózat például egy virtuális hálózati átjárón keresztül csatlakoztatható egy helyszíni hálózathoz. Az átjáró lehet ExpressRoute- vagy VPN-átjáró. Ha ezt a jelölőnégyzetet bejelöli, lehetővé teszi, hogy a társviszony-létesített virtuális hálózatból érkező forgalom a virtuális hálózathoz csatlakoztatott átjárón keresztül a helyszíni hálózatra folyjon. Ha bejelöli ezt a jelölőnégyzetet, a társviszony-létesített virtuális hálózatnem rendelkezhet átjáróval. A társviszony-létesített virtuális hálózatnak be kell jelölnie a **Távoli átjárók használata jelölőnégyzetet,** amikor a másik virtuális hálózatról a virtuális hálózatra állítja be a társviszony-létesítést. Ha ezt a mezőt bejelölve (alapértelmezett), a társviszony-létesített virtuális hálózatból érkező forgalom továbbra is erre a virtuális hálózatra áramlik, de nem tud a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül áramlani. Ha a társviszony-létesítés egy virtuális hálózat (Erőforrás-kezelő) és egy virtuális hálózat (klasszikus) között van, az átjárónak a virtuális hálózatban (Erőforrás-kezelő) kell lennie.

       A mellett, hogy a forgalmat egy helyszíni hálózatra továbbítja, a VPN-átjáró képes továbbítani a hálózati forgalmat a virtuális hálózatok között, amelyek az átjáró nvannak, anélkül, hogy a virtuális hálózatokat egymással kellene társviszonyba vonni. A VPN-átjáró használata a forgalom továbbításához akkor hasznos, ha egy hubban vpn-átjárót szeretne használni (lásd a továbbított **forgalom engedélyezése**című központi és küllős példát a virtuális hálózat közötti forgalom irányítására, amelyek nem egyenrangúak egymással. Ha többet szeretne tudni arról, hogy miként lehet átjárót használni az átvitelhez, olvassa el a [VPN-átjáró konfigurálása virtuális hálózati társviszony-létesítéshez című témakört.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ebben a forgatókönyvben olyan felhasználó által definiált útvonalakat kell implementálása, amelyek a virtuális hálózati átjárót a következő ugrástípusként határozzák meg. További információ a [felhasználó által definiált útvonalakról.](virtual-networks-udr-overview.md#user-defined) Csak egy VPN-átjárót adhat meg következő ugrástípusként egy felhasználó által definiált útvonalon, nem adhat meg ExpressRoute-átjárót a felhasználó által definiált útvonal következő ugrástípusaként.

    - **Távoli átjárók használata:** Jelölje be ezt a jelölőnégyzetet, ha azt szeretné, hogy a virtuális hálózatból érkező forgalom a társviszonyban lévő virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül folyjon. Például a virtuális hálózat, amelykel társviszonyban van, rendelkezik egy VPN-átjáró, amely lehetővé teszi a kommunikációt egy helyszíni hálózattal.  Ha bejelöli ezt a jelölőnégyzetet, lehetővé teszi, hogy a virtuális hálózatból érkező forgalom a társviszonyt létesített virtuális hálózathoz csatlakoztatott VPN-átjárón keresztül folyjon. Ha bejelöli ezt a jelölőnégyzetet, a társviszony-létesített virtuális hálózathoz virtuális hálózati átjárónak kell hozzá kapcsolódnia, és be kell jelölnie az **Átjáró tranzitjának engedélyezése** jelölőnégyzetet. Ha ezt a mezőt bejelölve (alapértelmezett), a társviszony-létesített virtuális hálózatból érkező forgalom továbbra is átfolyhat erre a virtuális hálózatra, de nem tud átfolyni a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón.
    
      A virtuális hálózathoz csak egy társviszony-létesítéshez engedélyezve lehet ez a beállítás.

      Nem használhat távoli átjárókat, ha már van konfigurálva átjáró a virtuális hálózatban. Ha többet szeretne tudni az átjáró átvitelhez való használatáról, olvassa el a [VPN-átjáró konfigurálása virtuális hálózati társviszony-létesítéshez való átvitelhez című témakört.](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Ha egy virtuális hálózati átjáró térhet el a helyszíni forgalom tranzitívan egy társviszony-létesített virtuális hálózat, a társviszony-létesített virtuális hálózat IP-tartománya a helyszíni VPN-eszköz kell állítani, hogy "érdekes" forgalmat. Ellenkező esetben a helyszíni erőforrások nem fognak tudni kommunikálni a társviszony-létesített virtuális hálózat erőforrásaival.

6. Válassza az **OK gombot,** ha hozzá szeretné adni a társviszony-létesítést a kiválasztott virtuális hálózathoz.

A virtuális hálózatok közötti társviszony-létesítés különböző előfizetésekben és telepítési modellekben történő megvalósításának lépésenkénti útmutatóját a [következő lépésekben](#next-steps)találja.

### <a name="commands"></a>Parancsok

- **Azure CLI**: [az hálózati virtuális hálózat társviszony-létesítési létrehozása](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Társviszony-létesítési beállítások megtekintése és módosítása

A társviszony-létesítés módosítása előtt ismerkedjen meg a követelményekkel, korlátozásokkal és a [szükséges engedélyekkel.](#permissions)

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt. Ne válassza a **virtuális hálózatok (klasszikus),** ha megjelenik a listában, mivel nem hozhat létre társviszony-létesítést a klasszikus telepítési modellen keresztül telepített virtuális hálózatról.
2. Jelölje ki azt a virtuális hálózatot a listában, amelynek társviszony-létesítési beállításait módosítani szeretné.
3. A **BEÁLLÍTÁSOK csoportban**válassza **a Társviszony-létesítések**lehetőséget.
4. Jelölje ki azt a társviszony-létesítést, amelynek beállításait meg szeretné tekinteni, vagy módosítani szeretné azokat.
5. Módosítsa a megfelelő beállítást. A Társviszony-létesítés létrehozása [című 5.](#add-peering)
6. Kattintson a **Mentés** gombra.

**Parancsok**

- **Azure CLI**: [az az hálózati virtuális hálózat társviszony-létesítési listája](/cli/azure/network/vnet/peering) a virtuális hálózat társviszony-létesítésének listázásához, az az hálózati virtuális hálózat [társviszony-létesítési megjelenítése](/cli/azure/network/vnet/peering) egy adott társviszony-létesítés beállításainak megjelenítéséhez, valamint az az hálózati virtuális hálózat [társviszony-létesítési frissítésének](/cli/azure/network/vnet/peering) megjelenítéséhez a társviszony-létesítési beállítások módosításához.|
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) a nézettársi kapcsolatlétesítési beállítások és [a Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) a beállítások módosításához.

## <a name="delete-a-peering"></a>Társviszony-létesítés törlése

A társviszony-létesítés törlése előtt győződjön meg arról, hogy a fiók rendelkezik a [szükséges engedélyekkel.](#permissions)

Társviszony-létesítés törlésekor a virtuális hálózatról érkező forgalom már nem áramlik a társviszony-létesített virtuális hálózatba. Ha az Erőforrás-kezelőn keresztül telepített virtuális hálózatok társviszonyban vannak, minden virtuális hálózat rendelkezik egy társviszony-létesítéssel a másik virtuális hálózattal. Bár a társviszony-létesítés törlése az egyik virtuális hálózatról letiltja a virtuális hálózatok közötti kommunikációt, nem törli a társviszony-létesítést a másik virtuális hálózatról. A másik virtuális hálózatban létező társviszony-létesítési állapota **leválasztva.** A társviszony-létesítés nem hozható létre újra, amíg újra létre nem hozza a társviszony-létesítést az első virtuális hálózatban, és mindkét virtuális hálózat társviszony-létesítési állapota *Kapcsolatra változik.*

Ha azt szeretné, hogy a virtuális hálózatok néha kommunikáljanak, de ne mindig, ahelyett, hogy egy társviszony-létesítést delegálnának, **beállíthatja** a Virtuális hálózati hozzáférés engedélyezése beállítást **Letiltva** értékre. Ennek módjáról a cikk Társviszony-létesítés ének létrehozása című szakaszának 6. A hálózati hozzáférés letiltása és engedélyezése egyszerűbb lehet, mint a társviszony-létesítések törlése és újbóli létrehozása.

1. A portál tetején lévő keresőmezőbe írja be a *virtuális hálózatokat* a keresőmezőbe. Amikor **virtuális hálózatok** jelennek meg a keresési eredmények között, jelölje ki azt. Ne válassza a **virtuális hálózatok (klasszikus),** ha megjelenik a listában, mivel nem hozhat létre társviszony-létesítést a klasszikus telepítési modellen keresztül telepített virtuális hálózatról.
2. Jelölje ki azt a virtuális hálózatot a listában, amelyhez társviszony-létesítést törölni szeretne.
3. A **BEÁLLÍTÁSOK csoportban**válassza **a Társviszony-létesítések**lehetőséget.
4. A törölni kívánt társviszony-létesítés jobb oldalán válassza a **...** lehetőséget, válassza **a Törlés**lehetőséget, majd az **Igen** lehetőséget az első virtuális hálózatból való társviszony-létesítés törléséhez.
5. Hajtsa végre az előző lépéseket a társviszony-létesítés a társviszony-létesítés másik virtuális hálózatról való törléséhez.

**Parancsok**

- **Azure CLI**: [az hálózati virtuális hálózat társviszony-létesítési törlése](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások

- <a name="cross-region"></a>A virtuális hálózatok társviszonyban ugyanabban a régióban, vagy különböző régiókban. A különböző régiókban lévő virtuális hálózatok társviszony-létesítését *globális virtuális társviszony-létesítésnek is nevezik.* 
- Globális társviszony-létesítés létrehozásakor a társviszony-létesített virtuális hálózatok bármely Azure nyilvános felhőrégióban vagy Kína felhőrégióiban vagy kormányzati felhőrégióiban létezhetnek. Nem lehet átkukucskálni a felhőkön. Például egy virtuális hálózat az Azure nyilvános felhőben nem társviszonyt létesített egy virtuális hálózat az Azure China-felhőben.
- Az egyes virtuális hálózatok erőforrásai nem kommunikálhatnak a globálisan társított virtuális hálózat alapszintű, belső terheléselosztóinak előtérbeli IP-címével. Az alapszintű terheléselosztó csak ugyanabban a régióban támogatott. A standard terheléselosztó mindkettőhöz támogatott, a virtuális hálózatok közötti és a globális virtuális hálózatok közötti társviszonyhoz is. Az alapszintű terheléselosztót használó szolgáltatásokat, amelyek nem működnek a globális virtuális hálózati társviszony-létesítés során, [itt dokumentáljuk.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Használhatja a távoli átjárók, vagy lehetővé teszi az átjáró átvitele a globálisan társviszonyozott virtuális hálózatok és a helyi társviszony-létesítési virtuális hálózatok.
- A virtuális hálózatok lehetnek ugyanabban a vagy különböző előfizetésekben. Ha különböző előfizetésekben vesz részt virtuális hálózatokban, mindkét előfizetés társítható ugyanahhoz vagy más Azure Active Directory-bérlőhöz. Ha még nem rendelkezik AD-bérlővel, [létrehozhat egyet.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant) A portálon nem érhető el a különböző Azure Active Directory-bérlőkhöz társított előfizetések virtuális hálózatok közötti társviszony-létesítése. Használhatja a CLI, PowerShell vagy sablonok.
- A társként létesített virtuális hálózatoknak nem átfedő IP-címterekkel kell rendelkezniük.
- Nem adhat hozzá címtartományokat a virtuális hálózat címteréből, illetve nem törölhetcímtartományokat, ha egy virtuális hálózat egy másik virtuális hálózattal konletársasolt. Címtartományok hozzáadásához vagy eltávolításához törölje a társviszony-létesítést, adja hozzá vagy távolítsa el a címtartományokat, majd hozza létre újra a társviszony-létesítést. Ha címtartományokat szeretne hozzáadni a virtuális hálózatokhoz, vagy el szeretné távolítani a címtartományokat a virtuális hálózatokból, olvassa el [a Virtuális hálózatok kezelése .Add](manage-virtual-network.md)address ranges to, or remove address ranges from virtual networks, see Manage virtual networks.
- Társviszony-átveheti az Erőforrás-kezelőn keresztül telepített két virtuális hálózatot, vagy az Erőforrás-kezelőn keresztül telepített virtuális hálózatot a klasszikus telepítési modellen keresztül telepített virtuális hálózattal. A klasszikus üzembe helyezési modellen keresztül létrehozott két virtuális hálózat társviszonya nem hajtható létre. Ha nem ismeri az Azure üzembe helyezési modelljeit, olvassa el az [Azure üzembe helyezési modelljeinek ismertetése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikket. A [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) használatával összeköthető két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat.
- Két, Resource Managerrel létrehozott virtuális hálózat közötti társviszony létrehozása esetén mindkét érintett virtuális hálózathoz konfigurálni kell egy társítást. A társviszony-létesítési állapothoz az alábbi típusok egyike jelenik meg: 
  - *Kezdeményezett:* Amikor az első virtuális hálózatról létrehozza a társviszony-létesítést a második virtuális hálózatra, a társviszony-létesítési állapot *megkezdve jön.* 
  - *Csatlakoztatva:* Amikor létrehozza a társviszony-létesítést a második virtuális hálózatról az első virtuális hálózatra, a társviszony-létesítési állapota *Csatlakoztatva .* Ha az első virtuális hálózat társviszony-létesítési állapotát tekinti meg, akkor az állapota *Kezdeményezettről* *Kapcsolatra*változott. A társviszony-létesítés nem sikerült, amíg a társviszony-létesítési állapot mindkét virtuális hálózati társviszony-létesítés *csatlakoztatva*van.
- Ha az Erőforrás-kezelőn keresztül létrehozott virtuális hálózatot a klasszikus központi telepítési modellen keresztül létrehozott virtuális hálózattal létesíti, csak az Erőforrás-kezelőn keresztül telepített virtuális hálózat társviszony-létesítését konfigurálja. Nem konfigurálható társviszony-létesítés egy virtuális hálózat (klasszikus), vagy két virtuális hálózatok a klasszikus telepítési modellen keresztül telepített. Amikor létrehozza a társviszony-létesítést a virtuális hálózatról (Resource Manager) a virtuális hálózatra (Klasszikus), a társviszony-létesítési állapot *frissítése*, majd hamarosan a Csatlakoztatott értékre *változik.*
- Két virtuális hálózat között létesítés jön létre. A koncpeeringek nem tranzitívak. Ha társviszony-létesítéseket hoz létre a következők között:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  A VirtualNetwork1 és a VirtualNetwork3 virtualnetwork2-en keresztül nem létesítés. Ha virtuális hálózati társviszony-létesítést szeretne létrehozni a VirtualNetwork1 és a VirtualNetwork3 között, létre kell hoznia egy társviszony-létesítést a VirtualNetwork1 és a VirtualNetwork3 között.
- A társviszony-létesített virtuális hálózatokban nem oldható fel nevek az alapértelmezett Azure-névfeloldás használatával. Más virtuális hálózatokban a nevek feloldásához [az Azure DNS-t magántartományokhoz](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egyéni DNS-kiszolgálóhoz kell használnia. A saját DNS-kiszolgáló beállításáról a [Névfeloldás a saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)című témakörben olvashat.
- Az azonos régióban lévő társviszony-létesített virtuális hálózatok erőforrásai ugyanolyan sávszélességgel és késleltetéssel kommunikálhatnak egymással, mintha ugyanabban a virtuális hálózatban lennének. Minden virtuális gép mérete saját maximális hálózati sávszélesség azonban. Ha többet szeretne megtudni a különböző virtuális gépméretek maximális hálózati sávszélességéről, olvassa el a [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép méretei.
- A virtuális hálózat társviszonyt létesíthet egy másik virtuális hálózatra, és egy Azure virtuális hálózati átjáróval egy másik virtuális hálózathoz is csatlakoztatható. Ha a virtuális hálózatok társviszony-létesítésen és átjárón keresztül is kapcsolódnak, a virtuális hálózatok közötti forgalom a társviszony-létesítési konfiguráción keresztül, nem pedig az átjárón keresztül áramlik.
- A pont-hely VPN-ügyfeleket újra le kell tölteni, miután a virtuális hálózati társviszony-létesítés sikeresen konfigurálva volt, hogy az új útvonalak le töltődjenek az ügyfélre.
- Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="permissions"></a>Engedélyek

A virtuális hálózati társviszony-létesítéshez használt fiókokat a következő szerepkörökhöz kell hozzárendelni:

- [Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Az Erőforrás-kezelőn keresztül telepített virtuális hálózat esetén.
- [Klasszikus hálózati közreműködő:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)A klasszikus üzembe helyezési modellen keresztül telepített virtuális hálózat.

Ha a fiók nincs hozzárendelve az előző szerepkörök egyikéhez, akkor azt egy [olyan egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kell hozzárendelni, amely a következő táblázatból rendeli hozzá a szükséges műveleteket:

| Műveletek                                                          | Név |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | A virtuális hálózatról a virtuális hálózatba való társviszony-létesítéshez szükséges B. Az A virtuális hálózatnak virtuális hálózatnak kell lennie (Erőforrás-kezelő)          |
| Microsoft.Network/virtualNetworks/peer/action                   | A B virtuális hálózatról (Erőforrás-kezelő) és az A virtuális hálózatra való társviszony-létesítéslétrehozásához szükséges                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | A B virtuális hálózatról (klasszikus) az A virtuális hálózatra való társviszony-létesítés létrehozásához szükséges                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Virtuális hálózati társviszony-létesítés olvasása   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Virtuális hálózati társviszony-létesítés törlése |

## <a name="next-steps"></a>További lépések

- A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

  |Azure üzembehelyezési modell             | Előfizetés  |
  |---------                          |---------|
  |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Különböző](create-peering-different-subscriptions.md)|
  |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
  |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

- Ismerje meg, hogyan hozhat létre [központi és küllős hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Virtuális hálózati társviszony-létesítés létrehozása [PowerShell-](powershell-samples.md) vagy Azure CLI-mintaparancsfájlokkal vagy Azure [Resource Manager-sablonok](template-samples.md) használatával [Azure CLI](cli-samples.md)
- [Azure-szabályzat](policy-samples.md) létrehozása és alkalmazása virtuális hálózatokra
