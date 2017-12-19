---
title: "Útvonalak - PowerShell hibaelhárítása |} Microsoft Docs"
description: "Ismerje meg, az Azure Resource Manager üzembe helyezési modellel, az Azure PowerShell útvonalak hibaelhárítása."
services: virtual-network
documentationcenter: na
author: AnithaAdusumilli
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: bf7dc5e7-9399-460e-8e0d-8992dbed98a6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: anithaa
ms.openlocfilehash: ac7f8ddaf84ba94075a9c9c3195bd57534c6821b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="troubleshoot-routes-using-azure-powershell"></a>Az Azure PowerShell útvonalak hibaelhárítása
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

### <a name="view-effective-routes-for-a-network-interface"></a>Egy adott hálózati csatoló hatékony útvonalak megtekintése
A hálózati illesztő alkalmazott összesített útvonalak megtekintéséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure PowerShell-munkamenetet és a bejelentkezés az Azure-bA. Ha nem ismeri az Azure PowerShell, olvassa el a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) cikk. Ön fiókjához társítva kell lenni a *Microsoft.Network/networkInterfaces/effectiveRouteTable/action* műveletet a hálózati adapter. Műveletek fiókokhoz rendeléséhez, lásd: [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#actions).
2. A következő parancsot adja vissza az összes útvonal nevű hálózati illesztő alkalmazott *VM1-NIC1* erőforráscsoportban *RG1*.
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
   
   > [!TIP]
   > Ha egy hálózati kapcsolat neve nem tudja, írja be a következő parancs futtatásával olvassa be az erőforrás-group.* minden hálózati interfészen
   > 
   > 
   
       Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name
   
   A következő kimeneti hasonlít-e a kimenet az egyes útvonalak alkalmazva a hálózati adapter csatlakozik:
   
       Name :
       State : Active
       AddressPrefix : {10.9.0.0/16}
       NextHopType : VNetLocal
       NextHopIpAddress : {}
   
       Name :
       State : Active
       AddressPrefix : {0.0.0.0/16}
       NextHopType : Internet
       NextHopIpAddress : {}
   
   Figyelje meg a következő szöveget a kimenetben:
   
   * **Név**: hatékony útvonal neve üres, lehet, kivéve, ha explicit módon megadott, felhasználó által definiált útvonalak. 
   * **Állapot**: a hatékony útvonal állapotát jelzi. Lehetséges értékek: "Active" vagy "Érvénytelen"
   * **AddressPrefixes**: határozza meg a hatékony útvonal a címelőtagot CIDR-formátumban. 
   * **nextHopType**: a megadott útvonal a következő ugrás jelzi. A lehetséges értékek: *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*, vagy *Null*. Érték *Null* a **nextHopType** egy UDR jelezhetik érvénytelen útvonal. Például ha **nextHopType** van *VirtualAppliance* és a hálózati virtuális készüléknek, virtuális gép nem létesített/futó állapotban van. Ha **nextHopType** van *A(z)* és nincs átjáró kiépítése/fut az adott virtuális válhat, hogy az útvonal érvénytelen.
   * **NextHopIpAddress**: a hatékony útvonal a következő ugrás IP-címe.
   
   A következő parancsot egy egyszerűbb megtekintéséhez a tábla a útvonalak adja vissza:
   
       Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table
   
   A következő kimenete a kimenet a fentiekben említett eset kapott egy részénél:
   
       Name State AddressPrefix NextHopType NextHopIpAddress
       ---- ----- ------------- ----------- ----------------
       Active {10.9.0.0/16} VnetLocal {}
       Active {0.0.0.0/0} Internet {}
3. Nincs látható útvonal a *WestUS-VNet3* VNet (a 10.10.0.0/16)** előtag *WestUS-VNet1* (10.9.0.0/16 előtag) a kimenet az előző lépésben. Ahogy az alábbi képen is látható, és a Vnetben társviszony összekapcsolásához a *WestUS-VNet3* virtuális hálózat szerepel a *Disconnected* állapotát.
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)
   
    A kétirányú kapcsolat esetében a társviszony megszakad, amely ismerteti, miért VM1 nem tudott kapcsolódni a VM3 a *WestUS-VNet3* virtuális hálózat. A telepítő egy kétirányú VNet társviszony-létesítési hivatkozást újra a *WestUS-VNet1* és *WestUS-VNet3* Vnetek. A kimeneti adott vissza a Vnetben társviszony-létesítési hivatkozás megfelelően létrejötte után a következőképpen:
   
        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
   
    Ha a probléma megállapításához hozzáadása, távolítsa el, vagy módosítsa az útvonalak és útvonaltáblát. Írja be az ehhez használt parancsok listájának megtekintéséhez a következő parancsot:
   
        Get-Help *-AzureRmRouteConfig

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
  * Hálózati biztonsági csoport (NSG) szabályok előfordulhat, hogy mely negatív hatással lehet a forgalom. További információkért lásd: a [hibaelhárítása hálózati biztonsági csoportok](virtual-network-nsg-troubleshoot-powershell.md) cikk.

