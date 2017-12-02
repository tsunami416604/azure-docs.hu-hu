---
title: "VPN-átjáró Azure verem kapcsolatos |} Microsoft Docs"
description: "További információk, és konfigurálja a VPN-átjárók használata Azure verem."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ba9642d8c51f57623aded44b84d7127334806bc1
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="about-vpn-gateway-for-azure-stack"></a>VPN-átjáró Azure verem kapcsolatos
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*


Az Azure virtuális hálózat és a helyszíni hely közötti hálózati forgalom küldése, előtt létre kell hoznia a virtuális hálózati átjáró a virtuális hálózat.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN-átjárók használatával biztonságosan verem Azure virtuális hálózat és az Azure virtuális hálózat között, vagy egy virtuális hálózatot és a VPN-eszközhöz van csatlakoztatva egy másik hálózat közötti forgalmat küldeni.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Az Azure verem támogat egy olyan virtuális hálózati átjáró: a 'VPN-' típus.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Erre egy példa a Többhelyes csatlakozás konfiguráció.

> [!NOTE]
> Az Azure VPN Gateway SKU úgy dönt, a sávszélesség adatátviteli sebességét, amelyek csatlakoznak az összes kapcsolat szét lehetnek osztva.  Azure-vermet a VPN Gateway SKU sávszélesség értéke alkalmazzák minden kapcsolati erőforrást, amely csatlakozik a hozzá.     

> Például az Azure, az alapszintű VPN Gateway SKU rendezhetők körülbelül 100 MB/s összesített.  Ha az adott VPN-átjáró két kapcsolatokat létrehozni, és egy kapcsolat 50 MB/s sávszélességet használ, a másik kapcsolat rendelkezésére áll majd 50 MB/s.   

> Azure-készletben *minden* kapcsolatot az alapszintű VPN Gateway SKU lekérdezi lefoglalt 100 MB/s átviteli.

## <a name="configuring-a-vpn-gateway"></a>VPN Gateway átjáró konfigurálása
A VPN-átjárós kapcsolatok több erőforrást használnak, amelyek speciális beállításokkal konfigurálhatók. Az erőforrások többsége külön konfigurálható, néhány esetben azonban egy bizonyos sorrendben kell őket konfigurálni.

### <a name="settings"></a>Beállítások
Az egyes erőforrások megfelelő beállítása kritikus fontosságú a sikeres kapcsolat létrehozásához. További információ az egyes erőforrások és a VPN-átjáró beállításainak: [kapcsolatos VPN-átjáró beállítások Azure verem](azure-stack-vpn-gateway-settings.md). Tekinthet meg információkat, amelyekkel jobban megértheti az átjáró, VPN és rugalmasságtípusokat, kapcsolattípusokat, átjáró-alhálózatot, helyi hálózati átjáró, valamint különböző más erőforrás-beállítások érdemes figyelembe venni.

### <a name="deployment-tools"></a>Üzembe helyezési eszközök
Az erőforrások létrehozásának és konfigurálásának megkezdéséhez használjon egy konfigurációs eszközt, például az Azure Portalt. Később átválthat egy másik eszközre, például a PowerShellre, a további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="connection-topology-diagrams"></a>Kapcsolat topológiai diagramot
Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Önnek kell eldöntenie, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbi szakaszokban a következő VPN-átjárós kapcsolatokról találhat információt és topológia-diagramokat: Az alábbi szakaszokban található táblázatok a következőket tartalmazzák:

- Elérhető üzemi modell
- Elérhető konfigurációs eszközök
- Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

A diagramok és a leírása a következő szakaszok segítségével válassza ki a kapcsolat topológiát saját igényeinek. Az ábrák bemutatják a fő alapvető topológiákat, az ábrákat útmutatásként használva azonban lehetséges összetettebb konfigurációk létrehozása is.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)
### <a name="site-to-site"></a>Helyek közötti kapcsolat
A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. Ehhez a típusú kapcsolathoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy nyilvános IP-cím, és nem NAT mögött helyezkedik el. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.    
![Pont-pont VPN-kapcsolat konfigurációs példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Többhelyes kapcsolat
Ez a típusú kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolatot használ, RouteBased (útvonalalapú) VPN-típust kell alkalmaznia (klasszikus virtuális hálózatok használatakor ezt dinamikus átjárónak nevezik). Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt gyakran „többhelyes” kapcsolatnak nevezik.   
![Azure VPN Gateway többhelyes kapcsolat – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>Gateway termékváltozatok
A virtuális hálózati átjáró Azure verem létrehozásakor meg kell adnia az átjáró, amely a használni kívánt Termékváltozat. A következő VPN gateway SKU-n támogatottak:
- Basic
- Standard
- HighPerformance

Kiválasztásakor egy magasabb gateway SKU, például a Standard keresztül Basic vagy HighPerformance Standard vagy a Basic, további processzorok és a hálózati sávszélesség az átjáró számára kiosztott. Ennek eredményeképpen az átjáró támogathatja a nagyobb hálózati teljesítményt, a virtuális hálózathoz.

Az Azure verem nem támogatja a UltraPerformance gateway SKU, amely kizárólag az Express Route.

A termékváltozat kiválasztásakor vegye figyelembe az alábbiakat:
- Az Azure verem nem támogatja a csoportházirend-alapú átjárók.
- Border Gateway Protocol (BGP) az alapszintű Termékváltozat nem támogatott.
- Az ExpressRoute-VPN-átjáró lehet konfigurációk nem támogatottak a Azure verem
- Aktív/aktív módú S2S VPN Gateway-kapcsolatokat csak a nagy teljesítményű termékváltozaton lehet konfigurálni.

## <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége
Az alábbi táblázatban az átjárótípusok és azok becsült összesített átviteli sebessége tekinthető meg az átjárók termékváltozata alapján.

|   | VPN Gateway teljesítménye *(1)* |VPN-átjáró maximális IPsec-alagutak |
|-------|-------|-------|
|**Alapszintű Termékváltozat** ***(2)***    | 100 Mbps  | 10    |
|**Standard Termékváltozat**       | 100 Mbps  | 10    |
|**Nagy teljesítményű Termékváltozat** | 200 Mbps    | 30    |
***(1)***  A VPN esetén átviteli sebesség nem garantált átviteli sebességgel létesítmények közötti kapcsolatok az interneten keresztül. Ez a maximális mérhető teljesítmény.  
***(2)***  Az alapszintű termékváltozat nem támogatott a BGP.

## <a name="next-steps"></a>Következő lépések
További tudnivalók [VPN-átjárók beállításainak](azure-stack-vpn-gateway-settings.md) Azure verem.
