<properties
   pageTitle="A BGP és az Azure VPN Gateway együttműködésének áttekintése | Microsoft Azure"
   description="Ez a cikk bemutatja, hogyan használható a BGP az Azure VPN Gateway megoldással együtt."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/16/2016"
   ms.author="yushwang"/>

# A BGP és az Azure VPN Gateway együttműködésének áttekintése

Ez a cikk ismerteti az Azure VPN Gateway megoldásban a BGP (Border Gateway Protocol) támogatását.

## A BGP ismertetése

A BGP az interneten gyakran használt szabványos útválasztási protokoll az útválasztási és elérhetőségi információcserére két vagy több hálózat között. Azure Virtual Network-környezetben a BGP teszi lehetővé az Azure VPN Gateway átjárók és a helyszíni VPN-eszközök (más néven BGP-társak vagy -szomszédok) számára az információcserét az „útvonalakat” illetően. Ezáltal mindkét átjáró ismerni fogja az érintett átjárókon és útválasztókon áthaladó előtagok rendelkezésre állási és elérhetőségi információit. A BGP lehetővé teszi a több hálózat közötti tranzit útválasztást azon útvonalak propagálásával az összes többi BGP-társ számára, amelyeket a BGP-átjáró az egyik BGP-társtól vesz át.
 
### Miért érdemes a BGP-t használni?

A BGP egy olyan opcionális szolgáltatás, amely az Azure útvonalalapú VPN-átjáróival együtt használható. A szolgáltatás engedélyezése előtt arról is meg kell győződni, hogy a helyszíni VPN-eszközök támogatják a BGP használatát. Az Azure VPN-átjáróit és a helyszíni VPN-eszközöket a BGP alkalmazása nélkül is tovább használhatja. Mindez egyenértékű a statikus (BGP nélküli) útvonalak dinamikus (BGP-vel történő) útválasztással *szembeni* alkalmazásával a hálózatok és az Azure között.

A BGP használata számos előnyt és új képességet biztosít:

#### Az automatikus és rugalmas előtagfrissítések támogatása

A BGP használatakor kizárólag egy minimális előtag megadására van szükség egy adott BGP-társ számára, az IPsec S2S VPN-alagúton keresztül. Lehet ez olyan kicsi is, mint a helyszíni VPN-eszközhöz tartozó BGP-társ IP-címének gazdaelőtagja (/32-es előtag). Szabályozhatja az Azure-on meghirdetett helyszíni hálózati előtagok körét az Azure Virtual Network hozzáférésének biztosításához.
    
Olyan nagyobb előtagok is meghirdethetők, amelyek a virtuális hálózat címelőtagjainak bizonyos részeit tartalmazhatják, így például egy nagy privát IP-címteret (például 10.0.0.0/8). Ügyeljen rá, hogy az előtagok nem egyezhetnek meg a virtuális hálózat előtagjainak egyikével sem. A rendszer elutasítja a virtuális hálózat előtagjaival azonos útvonalakat.

>[AZURE.IMPORTANT] Jelenleg az Azure VPN Gateway átjárókhoz vezető alapértelmezett útvonal meghirdetése (0.0.0.0/0) blokkolva van. Ha ezt a lehetőséget engedélyezzük, arról egy későbbi frissítésben adunk majd tájékoztatást.

#### Többcsatornás üzemeltetés támogatása BGP-alapú automatikus feladatátvétellel, egy virtuális hálózat és egy helyszín között

Ugyanazon a helyen több kapcsolat is létesíthető az Azure virtuális hálózat és a helyszíni VPN-eszközök között. Ezen képesség révén több alagút (elérési út) létesíthető a két hálózat között, aktív-aktív konfiguráció formájában. Ha az alagutak valamelyike le van választva, az annak megfelelő útvonalak a BGP-n keresztül visszavonásra kerülnek, az adatforgalom pedig automatikusan átvált a többi alagútra.
    
A következő ábra erre a magas rendelkezésre állású beállításra mutat egyszerű példát:
    
![Több aktív elérési út](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

#### Tranzit jellegű útválasztás támogatása a helyszíni hálózatok és több Azure virtuális hálózat között

A BGP több átjáró számára biztosítja a különböző hálózatokról származó előtagok megtanulásának és propagálásának lehetőségét, és hogy azok közvetlen vagy közvetett módon csatlakoznak-e. Ezzel engedélyezhető az Azure VPN-átjárók használatával történő tranzit útválasztás a helyszínek között vagy több Azure virtuális hálózaton.
    
A következő ábra egy olyan, több elérési úttal rendelkező, többugrásos topológiára mutat példát, amely két helyszíni hálózat közötti tranzit adatátvitelre képes, Azure VPN-átjárókon keresztül, a Microsoft Networks keretein belül:

![Többugrásos átvitel](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## BGP – Gyakori kérdések


[AZURE.INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)] 




## Következő lépések

A BGP helyek és virtuális hálózatok közötti kapcsolathoz történő konfigurálásának lépéseit [A BGP használatának első lépései Azure VPN-átjárókon](./vpn-gateway-bgp-resource-manager-ps.md) című részben tekintheti meg.




<!--HONumber=sep16_HO1-->


