---
title: "Útvonalak - portál hibaelhárítása |} Microsoft Docs"
description: "Ismerje meg, az Azure Resource Manager üzembe helyezési modellel, az Azure portál használatával útvonalak hibaelhárítása."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bdd8b6dc-02fb-4057-bb23-8289caa9de89
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: e0b835f4cbad9855bfb7ddccf2d9bf5b4bf88231
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2018
---
# <a name="troubleshoot-routes-using-the-azure-portal"></a>Az Azure portál használatával útvonalak hibaelhárítása
> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-routes-troubleshoot-portal.md)
> * [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
>
>

Ha a hálózati kapcsolat hibái vagy a az Azure virtuális gép (VM) a problémát, útvonalak előfordulhat, hogy mely negatív hatással lehet a VM forgalmat. Ez a cikk áttekintést diagnosztikai képességek útvonalak további hibaelhárítás elősegítése érdekében.

Az útvonaltáblák alhálózatok tartoznak, és minden hálózati interfészen (NIC) alhálózat érvényben. A következő típusú útvonalak mindegyik hálózati interfész alkalmazhatók:

* **Rendszerútvonalak:** alapértelmezés szerint az Azure Virtual Network (VNet) létrehozott összes alhálózatot rendelkezik rendszer útvonaltábláit, amelyek lehetővé teszik a helyi VNet forgalmát, a helyszíni forgalom keresztül VPN-átjárók és internetes forgalmat. Rendszerútvonalak társviszonyban Vnetek esetében is létezik.
* **BGP-útvonalakat:** jutott el a hálózati adapterek expressroute-on vagy VPN-kapcsolatok pont-pont keresztül. Tudjon meg többet a BGP-útválasztás olvasásával a [BGP a VPN-átjárók](../vpn-gateway/vpn-gateway-bgp-overview.md) és [ExpressRoute – áttekintés](../expressroute/expressroute-introduction.md) cikkeket.
* **Felhasználó által definiált útvonalak (UDR):** hálózati virtuális készülékek vagy azok használata a kényszerített bújtatás adatforgalom érdekében egy helyi hálózaton keresztül a telephelyek közötti VPN, előfordulhat, hogy felhasználói (udr-EK) rendelt útvonalakat a alhálózati útválasztási táblázatot. Ha még nem ismeri a udr-EK, olvassa el a [felhasználó által definiált útvonalak](virtual-networks-udr-overview.md#user-defined) cikk.

Különböző felé mutató útvonalakat, amelyek egy adott hálózati csatoló alkalmazhatók azok nehéz lenne meghatározni, melyik összesített útvonalak érvényben. Az Azure Resource Manager üzembe helyezési modellel megoldhatja a virtuális gép hálózati kapcsolatot, megtekintheti a hatékony útvonalakat egy adott hálózati csatoló.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Hatékony útvonalak használata hibák elhárításához a virtuális gép forgalom áramlását
Ebben a cikkben az az alábbi forgatókönyvet mutatja be egy adott hálózati csatoló hatékony útvonalak hibaelhárítása példa:

A virtuális gép (*VM1*) csatlakoztatva a virtuális hálózat (*VNet1*, előtag: 10.9.0.0/16) nem tud kapcsolódni egy újonnan peered virtuális VM(VM3) (*VNet3*, 10.10.0.0/16 előtag). Nincsenek nem udr-EK vagy a BGP irányítja a VM1-NIC1 hálózati illesztő a virtuális Géphez kapcsolódó alkalmazza, a rendszer csak a rendszer útvonalakat alkalmazza.

Ez a cikk azt ismerteti, hogyan hatékony útvonalak funkció használata Azure Resource Manager üzembe helyezési modellel csatlakozási hiba okának megállapításához.
A példában csak a rendszerútvonalak, amíg ugyanezen lépések segítségével határozza meg a bejövő és kimenő kapcsolódási hibák útvonal bármilyen keresztül.

> [!NOTE]
> Ha a virtuális Géphez kapcsolt egynél több hálózati Adaptert, ellenőrizze a hatékony útvonalak az egyes a hálózati adapter felé és felől a virtuális gépek hálózati csatlakozási problémák diagnosztizálásához.
>
>

### <a name="view-effective-routes-for-a-virtual-machine"></a>A virtuális gépek hatékony útvonalak megtekintése
A virtuális gép által használt összesített útvonalak megtekintéséhez kövesse az alábbi lépéseket:

1. Az Azure portálon, a bejelentkezés https://portal.azure.com. Ön fiókjához társítva kell lenni a *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* műveletet a hálózati adapter. Műveletek fiókokhoz rendeléséhez, lásd: [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. Kattintson a **minden szolgáltatás**, majd kattintson a **virtuális gépek** a megjelenő listában.
3. Jelöljön ki egy virtuális Gépet, a listában megjelenő hibaelhárítása, és a beállítások egy virtuális gép panel jelenik meg.
4. Kattintson a **derítse & felmerülő problémák megoldásához** , és válassza a gyakori probléma. Ehhez a példához **nem lehet csatlakozni a windowsos virtuális Gépemhez** van kiválasztva.

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)
5. Az alábbi képen látható módon lépéseket a probléma alatt jelenik meg:

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Kattintson a *hatékony útvonalak* a javasolt lépéseket listájában.
6. A **hatékony útvonalak** panel jelenik meg, az alábbi ábrán látható módon:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Ha a virtuális gép egyetlen hálózati Adapterrel rendelkezik, az alapértelmezés szerint van kiválasztva. Ha egynél több hálózati adapter, jelölje ki a hálózati adapter, amelynek meg szeretné tekinteni a hatékony útvonalakat.

   > [!NOTE]
   > Ha a virtuális gép társított hálózati adapter nem fut, hatékony útvonalak nem jelenik meg. Csak az első 200 hatékony útvonalak megjelennek a portálon. A teljes listát kattintson **letöltése**. További szűrheti az eredményeket a letöltött .csv fájlból.
   >
   >

    Figyelje meg a következő szöveget a kimenetben:

   * **Forrás**: útvonal típusát jelöli. Rendszerútvonalak egyaránt megjelennek az helyeként *alapértelmezett*, udr-EK egyaránt megjelennek az helyeként *felhasználói* és átjáró útvonalak (statikus vagy BGP) jelennek meg *A(z)*.
   * **Állapot**: a hatékony útvonal állapotát jelzi. A lehetséges értékek: *aktív* vagy *érvénytelen*.
   * **AddressPrefixes**: határozza meg a hatékony útvonal a címelőtagot CIDR-formátumban.
   * **nextHopType**: a megadott útvonal a következő ugrás jelzi. A lehetséges értékek: *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, vagy *Null*. Érték *Null* a **nextHopType** egy UDR jelezhetik érvénytelen útvonal. Például ha **nextHopType** van *VirtualAppliance* és a hálózati virtuális készüléknek, virtuális gép nem létesített/futó állapotban van. Ha **nextHopType** van *A(z)* és nincs átjáró kiépítése/fut az adott virtuális válhat, hogy az útvonal érvénytelen.
7. Nincs útvonal a(z) látható a *WestUS-VNET3* VNet (előtag 10.10.0.0/16) az a *WestUS-VNet1* (10.9.0.0/16 előtag) a képen látható az előző lépésben. Az alábbi képen látható, a társviszony-létesítési hivatkozás szerepel a *Disconnected* állapota:

    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    A kétirányú kapcsolat esetében a társviszony megszakad, amely ismerteti, miért VM1 nem tudott kapcsolódni a VM3 a *WestUS-VNet3* virtuális hálózat.
8. Az alábbi képen látható az útvonalakat a kétirányú társviszony-létesítési hivatkozás létrehozása után:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

További hibaelhárítási forgatókönyveket a kényszerített bújtatás és útvonal értékelésre, olvassa el a [szempontok](virtual-network-routes-troubleshoot-portal.md#considerations) című szakaszát.

### <a name="view-effective-routes-for-a-network-interface"></a>Egy adott hálózati csatoló hatékony útvonalak megtekintése
Ha egy adott hálózati csatoló (NIC) kihatással van a hálózati adatforgalmat, megtekintheti hatékony útvonalak teljes listáját a hálózati adapter közvetlenül. A hálózati adapter által alkalmazott összesített útvonalak megtekintéséhez kövesse az alábbi lépéseket:

1. Az Azure portálon, a bejelentkezés https://portal.azure.com.
2. Kattintson a **minden szolgáltatás**, majd kattintson a **hálózati illesztői**
3. A hálózati adapter nevét a listában, vagy válassza ki azt a listából, amely akkor jelenik meg. Ebben a példában **VM1-NIC1** van kiválasztva.
4. Válassza ki **hatékony útvonalak** a a **hálózati illesztő** panel, az alábbi ábrán látható módon:

       ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    A **hatókör** az alapértelmezett érték a kiválasztott hálózati illesztőt.

      ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)

### <a name="view-effective-routes-for-a-route-table"></a>Egy útválasztási táblázatot hatékony útvonalak megtekintése
Ha módosítja a felhasználó által definiált útvonalak (udr-EK) a egy útválasztási táblázatot, érdemes lehet tekintse át az útvonalakat, egy adott virtuális Gépet felvenni a hatását. Lehet, hogy egy útválasztási táblázatot alhálózatok tetszőleges számú társítani. A hatékony útvonalak esetében a hálózati adapterek összes adataként megadott útvonaltábla, most már megtekintheti a megadott útvonal tábla paneljéről környezetben kapcsoló nélkül.

Az ebben a példában egy UDR (*UDRoute*) van megadva az útvonaltábla (*UDRouteTable*). Ez az útvonal elküldi az összes internetes forgalmat *Alhalozat_1* a a *WestUS-VNet1* virtuális hálózaton keresztül a hálózati virtuális készülék (NVA), a *Alhalozat_2* , ugyanazt a virtuális hálózatot. Az útvonal az alábbi képen látható:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Az összesített útvonalakat egy útválasztási táblázatot megtekintéséhez kövesse az alábbi lépéseket:

1. Az Azure portálon, a bejelentkezés https://portal.azure.com.
2. Kattintson a **minden szolgáltatás**, majd kattintson a **útvonaltáblát**
3. Keresse meg az útválasztási táblázatban, lásd: az összesítő útvonalakat, és válassza ki azt a listában. Ebben a példában **UDRouteTable** van kiválasztva. A kiválasztott útválasztási táblázatot egy panelen jelenik meg, az alábbi ábrán látható módon:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)
4. Válassza ki **hatékony útvonalak** a a **útvonaltábla** panelen. A **hatókör** a kiválasztott útválasztási táblázatot értékre van állítva.
5. Több alhálózattal is alkalmazható egy útválasztási táblázatot. Válassza ki a **alhálózati** meg szeretné tekinteni a listából. Ebben a példában **Alhalozat_1** van kiválasztva.
6. Válassza ki a **hálózati adapter**. A kijelölt alhálózathoz csatlakoztatott hálózati adapterek összes vannak felsorolva. Ebben a példában **VM1-NIC1** van kiválasztva.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

   > [!NOTE]
   > Ha a hálózati adapter nincs társítva van egy virtuális gép, nem hatékony útvonalak jelennek meg.
   >
   >

## <a name="considerations"></a>Megfontolandó szempontok
Néhány dolgot szem előtt tartani, ha a útvonalak listája adja vissza:

* Útválasztás alapján történik a leghosszabb előtag-megfeleltetés (LPM) között udr-EK, útvonalakat a BGP és a rendszer. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, majd egy útvonal elemet a következő sorrendben Kiindulás alapján:

  * Felhasználó által megadott útvonal
  * BGP-útvonal
  * Rendszerútvonal (alapértelmezett)

    Az hatékony útvonalakat is csak akkor jelenik meg, amelyek alapján a mavenen útvonalakat az LPM megfeleltetéssel hatékony útvonalak. Jelenít meg, hogyan útvonalak ténylegesen kiértékeli a megadott hálózati adapter, így sokkal könnyebben hibaelhárításához, előfordulhat, hogy mely negatív hatással lehet a virtuális gép és a kapcsolat meghatározott útvonalakat.
* Ha udr-EK és a forgalom küldi a hálózati virtuális készülék (NVA) *VirtualAppliance* , **nextHopType**, győződjön meg arról, hogy az IP-továbbítás engedélyezve van-e az NVA, hanem a forgalmat fogadó vagy eldobott csomagok.
* Ha kényszerített bújtatás engedélyezve van, minden kimenő internetforgalom a helyszínen történik. Az RDP/SSH internetről a virtuális géphez nem feltétlenül ezt a beállítást, attól függően, hogy miként kezeli a helyszíni a forgalmat.
  A kényszerített bújtatás engedélyezhető:
  * Ha használja a telephelyek közötti VPN, úgy, hogy egy felhasználó által megadott útvonal (UDR) a VPN-átjáróként nextHopType
  * Ha az alapértelmezett útvonal BGP keresztül lett hirdetve.
* A virtuális hálózat társviszony-létesítési forgalom működéséhez, a rendszer útvonal **nextHopType** *VNetPeering* a peered VNet előtag tartományban léteznie kell. Ha ilyen útvonal nem létezik, és a Vnetben társviszony-létesítési hivatkozás rákeres OK:
  * Várjon néhány másodpercet, amíg, és próbálja meg újra, ha az újonnan létrehozott társviszony-létesítési hivatkozást. Alkalmanként hosszabb ideig tart egy alhálózatot a hálózati adapterek útvonalakat terjesztése.
  * Hálózati biztonsági csoport (NSG) szabályok előfordulhat, hogy mely negatív hatással lehet a forgalom. További információkért lásd: a [hibaelhárítása hálózati biztonsági csoportok](virtual-network-nsg-troubleshoot-portal.md) cikk.
