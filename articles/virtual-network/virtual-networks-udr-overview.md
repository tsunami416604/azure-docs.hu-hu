---
title: "Felhasználói útvonalak és IP-továbbítás az Azure-ban | Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhat a felhasználói útvonalakat (UDR) és az IP-továbbítást a forgalom hálózati virtuális készülékekre történő irányításához az Azure-ban."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: c39076c4-11b7-4b46-a904-817503c4b486
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 6274e0101f6fb0864c8d1efaef7fcde78b8760c3
ms.contentlocale: hu-hu
ms.lasthandoff: 05/31/2017

---
# <a name="user-defined-routes-and-ip-forwarding"></a>Felhasználó által megadott útvonalak és IP-továbbítás

Amikor az Azure-ban virtuális gépeket (VM-ek) ad hozzá egy virtuális hálózathoz (VNet), észre fogja venni, hogy a virtuális gépek automatikusan tudnak egymással kommunikálni a hálózaton keresztül. Nem kell megadni átjárót, akkor sem, ha a virtuális gépek külön alhálózatokon vannak. Ugyanez vonatkozik a virtuális gépek és a nyilvános internet közötti kommunikációra, és akár a helyszíni hálózatra is, ha jelen van egy hibrid kapcsolat az Azure és a saját adatközpont között.

A kommunikáció ilyen típusú áramlása azért lehetséges, mert az Azure rendszerútvonalak sorát használja az IP-cím adatforgalmának meghatározására. A rendszerútvonalak az alábbi helyzetekben irányítják a kommunikáció áramlását:

* Ha ugyanazon az alhálózaton belül történik.
* Két alhálózat között történik egy virtuális hálózaton belül.
* Virtuális gépek és az internet között történik.
* Két VNet között történik egy VPN-átjárón keresztül.
* Két VNet között történik Virtuális hálózatok közötti társviszony révén (Szolgáltatásláncolás).
* Egy VNet és a helyszíni hálózat között történik egy VPN-átjárón keresztül.

Az alábbi ábrán egy egyszerű beállítás látható egy virtuális hálózattal, két alhálózattal és néhány virtuális géppel, valamint azokkal a rendszerútvonalakkal, amelyek engedélyezik az IP-cím forgalmának áramlását.

![Rendszerútvonalak az Azure-ban](./media/virtual-networks-udr-overview/Figure1.png)

Bár a rendszerútvonalak használata automatikusan segíti az üzemelő példány forgalmát, vannak esetek, amikor a csomagok útválasztását érdemesebb egy virtuális készüléken keresztül irányítani. Ezt úgy teheti meg, hogy felhasználó által megadott útvonalakat hoz létre, amelyek úgy határozzák meg a következő ugrást az adott alhálózatra áramló csomagok számára, hogy azok a virtuális készülékre érkezzenek, valamint engedélyezi az IP-továbbítást a virtuális készülékként futó virtuális gép számára.

Az alábbi ábra a felhasználó által megadott útvonalakra és az IP-továbbításra mutat egy példát, amelyben az egyik alhálózatról a másikra küldött csomagokat arra kényszerítik, hogy áthaladjanak a harmadik alhálózat virtuális készülékén.

![Rendszerútvonalak az Azure-ban](./media/virtual-networks-udr-overview/Figure2.png)

> [!IMPORTANT]
> A rendszer a felhasználó által megadott útvonalakat az alhálózatot elhagyó, az alhálózatban lévő bármely erőforrásról (például a virtuális gépekhez csatolt hálózati adapterekről) származó forgalomra alkalmazza. Nem hozhat létre útvonalakat például azért, hogy meghatározza, milyen úton érkezik a forgalom az internetről egy alhálózatba. Az a készülék, amelyre a forgalmat továbbítja, nem lehet ugyanabban az alhálózatban, ahonnan a forgalom származik. Mindig hozzon létre egy külön alhálózatot a készülékeinek. 
> 
> 

## <a name="route-resource"></a>Útvonal-erőforrás
A csomagok útválasztása a TCP/IP-hálózatban egy útválasztási táblázaton alapul, ami a fizikai hálózat minden csomópontján meg van határozva. Az útválasztási táblázat külön útvonalak gyűjteménye, amelyet arra használnak, hogy az IP-célcím alapján eldöntsék, a rendszer hova továbbítsa a csomagokat. Az útvonalak a következőket tartalmazzák:

| Tulajdonság | Leírás | Korlátozások | Megfontolások |
| --- | --- | --- | --- |
| Címelőtag |A cél CIDR, amelyre az útvonal vonatkozik, például 10.1.0.0/16. |Érvényes CIDR tartománynak kell lennie, ami a nyilvános interneten, az Azure virtuális hálózatban vagy a helyszíni adatközpontban található címeket jelöli. |Győződjön meg arról, hogy a **címelőtag** nem **a következő ugrás címét** tartalmazza, különben a csomagok bekerülnek egy hurokba, és a forrásuktól a következő ugráshoz kerülnek anélkül, hogy valaha is elérnék a céljukat. |
| A következő ugrás típusa |Az Azure ugrás típusa, amellyel a csomagot küldeni kell. |A következő értékek egyikének kell lennie: <br/> **Virtuális hálózat**. A helyi virtuális hálózatot jelöli. Ha például ugyanabban a virtuális hálózatban két alhálózat van, (10.1.0.0/16 és 10.2.0.0/16) az útválasztási táblázatban az alhálózatok útvonalának következő ugrás értéke *Virtuális hálózat* lesz. <br/> **Virtuális hálózati átjáró**. Egy Azure S2S VPN Gateway átjárót jelöl. <br/> **Internet**. Az Azure infrastruktúra által biztosított alapértelmezett internetes átjárót jelöli. <br/> **Virtuális készülék**. Az Azure Virtual Networkhöz hozzáadott virtuális készüléket jelöli. <br/> **Nincs**. Egy fekete lyukat jelöl. A fekete lyukakba továbbított csomagok nem lesznek továbbítva. |Fontolja meg **Virtuális készülék** használatát az adatforgalom átirányításához egy virtuális gépre vagy az Azure Load Balancer belső IP-címére.  Ezzel a típussal megadható egy IP-cím, az alábbiakban ismertetettek szerint. Érdemes a **Nincs** típust használni, ha nem szeretné, hogy a csomagok elérjék a megadott céljukat. |
| A következő ugrás címe |A következő ugrás címe azt az IP-címet tartalmazza, ahová a csomagokat továbbítani kell. A következő ugrás értékei csak az olyan útvonalaknál engedélyezettek, ahol a következő ugrás típusa *Virtuális készülék*. |Egy olyan IP-címnek kell lennie, ami elérhető azon virtuális hálózaton belül, ahol a felhasználó által megadott útvonal van alkalmazva anélkül, hogy áthaladna a **virtuális hálózati átjárón**. Az IP-címnek egy társított virtuális hálózaton, vagy ugyanazon a virtuális hálózaton kell lennie, ahol alkalmazva van. |Ha az IP-cím egy virtuális gépet jelöl, győződjön meg arról, hogy engedélyezi az [IP-továbbítást](#IP-forwarding) a virtuális gép számára az Azure-ban. Ha az IP-cím az Azure Load Balancer belső IP-címét jelöli, győződjön meg arról, hogy minden egyes porthoz, amelyen terheléselosztást kíván végrehajtani, hozzá van rendelve egy terheléselosztási szabály.|

Az Azure PowerShellben néhány „NextHopType” értéknek különböző neve van:

* A virtuális hálózat a VnetLocal
* A virtuális hálózati átjáró a VirtualNetworkGateway
* A virtuális készülék a VirtualAppliance
* Az internet az Internet
* A nincs a Nincs

### <a name="system-routes"></a>Rendszerútvonalak
A rendszer a virtuális hálózatban létrehozott összes alhálózatot automatikusan hozzárendeli egy útválasztási táblázathoz, ami az alábbi útválasztási szabályokat tartalmazza:

* **Helyi Vnet szabály**: Ez a szabály a virtuális hálózat miden alhálózatában automatikusan létrejön. Meghatározza, hogy a virtuális hálózatban a virtuális gépek között közvetlen kapcsolat van, és nincs köztes ugrás.
* **Helyszíni szabály**: Ez a szabály a helyszíni címtartományba irányuló teljes forgalomra érvényes, és VPN-átjárót használ a következő ugrási célként.
* **Internet szabály**: Ez a szabály kezeli a nyilvános internet felé irányuló teljes forgalmat (címelőtag 0.0.0.0/0) , és az internetre irányuló forgalomnál az infrastruktúra internetes átjáróját használja következő ugrásként.

### <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak
A legtöbb környezetben csak az Azure által meghatározott rendszerútvonalakra van szükség. Lehetséges azonban, hogy létre kell hoznia egy útválasztási táblázatot, amelyhez bizonyos esetekben hozzá kell adnia egy vagy több útvonalat, ilyenek például:

* Kényszerített bújtatás az internetre a helyszíni hálózaton keresztül.
* Virtuális készülékek használata Azure környezetben.

A fenti helyzetekben létre kell hoznia egy útválasztási táblázatot, és hozzá kell adnia felhasználó által meghatározott útvonalakat. Egyszerre több útválasztási táblázattal is rendelkezhet, és ugyanazt az útválasztási táblázatot egy vagy több alhálózathoz is hozzá lehet rendelni. És minden alhálózathoz csak egy útválasztási táblázatot lehet hozzárendelni. Az alhálózat minden virtuális gépe és felhőszolgáltatása ugyanazt az alhálózathoz hozzárendelt útválasztási táblázatot használja.

Az alhálózatok egészen addig rendszerútvonalakra támaszkodnak, amíg hozzá nem rendelnek egy útválasztási táblázatot az alhálózathoz. Miután egy hozzárendelés létrejött, az útválasztás a felhasználó által megadott útvonalaknál és a rendszerútvonalaknál is a leghosszabb előtag-megfeleltetés (LPM) alapján történik. Ha egynél több útvonal rendelkezik ugyanazzal az LPM megfeleltetéssel, akkor a rendszer az útvonalat a kiindulás alapján választja ki, az alábbi sorrendben:

1. Felhasználó által megadott útvonal
2. BGP-útvonal (ExpressRoute használatánál)
3. Rendszerútvonal

A felhasználó által megadott útvonalak létrehozásával kapcsolatban tekintse meg a [How to Create Routes and Enable IP Forwarding in Azure](virtual-network-create-udr-arm-template.md) (Hogyan hozhat létre útvonalakat, és engedélyezheti az IP-továbbítást az Azure-ban) című cikket.

> [!IMPORTANT]
> A rendszer a felhasználó által megadott útvonalakat csak az Azure virtuális gépekre és felhőszolgáltatásokra alkalmazza. Ha például virtuális készülékként egy tűzfalat szeretne beiktatni a helyszíni hálózat és az Azure közé, létre kell hoznia egy felhasználó által megadott útvonalat az Azure útválasztási táblázatban, amely a helyszíni címtérre irányuló teljes forgalmat a virtuális készülékre továbbítja. Hozzáadhat egy felhasználó által megadott útvonalat a GatewaySubnet alhálózathoz, így az összes helyszíni forgalmat továbbíthatja az Azure-ba a virtuális készüléken keresztül. Ez a lehetőség a közelmúltban lett hozzáadva.
> 
> 

### <a name="bgp-routes"></a>BGP-útvonalak
Ha a helyszíni hálózat és az Azure között ExpressRoute kapcsolat van, akkor engedélyezni lehet a BGP-t, hogy az útvonalakat a helyszíni hálózatból az Azure-ba terjessze. A BGP-útvonalakat minden Azure alhálózatban ugyanúgy kell használni, mint a rendszerútvonalakat és a felhasználó által megadott útvonalakat. További információ: [ExpressRoute Introduction](../expressroute/expressroute-introduction.md) (Az ExpressRoute bemutatása).

> [!IMPORTANT]
> Az Azure környezetet a helyszíni hálózaton keresztül lehet úgy konfigurálni, hogy kényszerített bújtatást használjon. Ehhez létre kell hozni egy felhasználó által megadott útvonalat a 0.0.0.0/0 alhálózat számára, amely a VPN-átjárót használja következő ugrásként. Ez azonban csak akkor működik, ha VPN-átjárót használ, nem ExpressRoute-ot. Az ExpressRoute-nál a kényszerített bújtatás konfigurálása a BGP-n keresztül történik.
> 
> 

## <a name="ip-forwarding"></a>IP-továbbítás
A fent leírtak szerint a felhasználó által megadott útvonal létrehozásának egyik fő oka a forgalom virtuális készülékre történő továbbítása. A virtuális készülék nem más, mint egy virtuális gép, amelyen egy olyan alkalmazás fut, ami a hálózati forgalmat kezeli valamilyen módon, például tűzfallal vagy NAT-eszközzel.

A virtuális készüléknek képesnek kell lennie fogadni a nem neki címzett bejövő forgalmat. Ahhoz, hogy egy virtuális gép számára engedélyezze a más célhelyre irányított forgalom fogadását, először engedélyeznie kell a virtuális gép számára az IP-továbbítást. Ez egy Azure beállítás, nem a vendég operációs rendszer beállítása.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [hozhat létre útvonalakat a Resource Manager üzembe helyezési modellben](virtual-network-create-udr-arm-template.md), és hogyan rendelheti őket hozzá az alhálózatokhoz. 
* Ismerje meg, hogyan [hozhat létre útvonalakat a klasszikus üzembe helyezési modellben](virtual-network-create-udr-classic-ps.md), és hogyan rendelheti őket hozzá az alhálózatokhoz.


