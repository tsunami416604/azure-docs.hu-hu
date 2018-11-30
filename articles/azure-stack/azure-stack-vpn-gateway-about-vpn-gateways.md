---
title: Tudnivalók a VPN gatewayről az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, és konfigurálja a VPN-átjárók használhatja az Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 860f1181a84ef87fe0e66c2746b76b800a326354
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496198"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Tudnivalók az Azure Stackhez VPN-átjáró

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Hálózati forgalom küldése az Azure virtuális hálózat és a helyszíni hely között, előtt a virtuális hálózat egy virtuális hálózati átjárót kell létrehoznia.

A VPN-átjáró a virtuális hálózati átjárók egy olyan típusa, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN-átjárók segítségével biztonságosan a virtuális hálózat az Azure Stackben és a egy virtuális hálózat között forgalmat küldeni az Azure-ban. Biztonságosan egy virtuális hálózat és a egy VPN-eszközhöz van csatlakoztatva egy másik hálózat közötti forgalmat is küldhet.

Virtuális hálózati átjáró létrehozásakor megadja a létrehozni kívánt átjárótípust. Az Azure Stack támogatja a virtuális hálózati átjáró, egyfajta a **Vpn** típusa.

Mindegyik virtuális hálózat kettő virtuális hálózati átjáróval rendelkezhet, de típusonként csak eggyel. A kiválasztott beállításoktól függően több kapcsolatot is létrehozhat egyetlen VPN-átjáróhoz. Ilyen például, a többhelyes csatlakozás konfiguráció.

Létrehozása, és az Azure Stackhez VPN-átjárók konfigurálása előtt tekintse át a [Azure Stack hálózati szempontjai](user/azure-stack-network-differences.md) megtudhatja, hogyan Azure stack-konfigurációk különböznek egymástól, az Azure-ból.

>[!NOTE]
>Az Azure-ban a VPN Gateway SKU úgy dönt, a sávszélesség átviteli sebesség az átjáró csatlakoztatott összes kapcsolatot szét lehetnek osztva. De az Azure Stackben, a sávszélesség a VPN Gateway Termékváltozat értéken egyes kapcsolati erőforrás, amely az átjáró csatlakozik.
>
> Példa:
> * Az Azure-ban az alapszintű VPN Gateway Termékváltozat körülbelül 100 MB/s összesített átviteli sebességre képes kezelni. Létrehozhat két adott VPN-átjáró kapcsolatot, és a egy kapcsolat használ 50 MB/s sávszélesség, majd 50 MB/s-e a többi kapcsolat számára elérhető.
> * Az Azure Stackben *egyes* az alapszintű VPN Gateway-Termékváltozat a kapcsolat le egyet azon 100 MB/s átviteli sebesség.

## <a name="configuring-a-vpn-gateway"></a>VPN Gateway átjáró konfigurálása

VPN gateway-kapcsolat meghatározott beállításokkal konfigurált számos erőforrás támaszkodik. Ezek az erőforrások többsége külön konfigurálható, de bizonyos esetekben kell ezeket konfigurálni a meghatározott sorrendben.

### <a name="settings"></a>Beállítások

A beállításokat az egyes erőforrások létfontosságúak a sikeres kapcsolat létrehozásához.

Egyéni erőforrások és a VPN Gateway beállításaival kapcsolatos további információkért lásd: [kapcsolatos VPN gateway beállításairól az Azure Stack](azure-stack-vpn-gateway-settings.md). Ez a cikk segít megérteni:

* Átjáró VPN-típusok és kapcsolattípusok.
* Átjáró-alhálózatokkal, a helyi hálózati átjárót és a többi erőforrás-beállítások, amelyeket érdemes figyelembe venni.

### <a name="deployment-tools"></a>Üzembe helyezési eszközök

Létrehozhat és konfigurálhat erőforrásokat egy konfigurációs eszközt, például az Azure portal használatával. Később, előfordulhat, hogy váltson egy másik eszköz, például a PowerShell használatával a további erőforrások konfigurálásához, vagy adott esetekben a létező erőforrások módosításához. Jelenleg nem lehet a minden erőforrást és erőforrás-beállítást az Azure Portalon konfigurálni. Az egyes kapcsolati topológiákhoz tartozó cikkekben lévő utasítások egyértelműsítik, hogy mikor van szükség egy speciális konfigurációs eszközre.

## <a name="connection-topology-diagrams"></a>Kapcsolati topológia-diagramok

Fontos szem előtt tartani, hogy a VPN-átjárós kapcsolatokhoz különböző konfigurációk érhetők el. Határozza meg, melyik konfiguráció felel meg a legjobban az igényeinek. Az alábbiakban tekintheti meg a következő VPN gateway-kapcsolatok információt és topológia-diagramokat:

* Elérhető üzemi modell
* Elérhető konfigurációs eszközök
* Hivatkozások, amelyek közvetlenül egy cikkre mutatnak, ha van ilyen

Az ábrák és leírások az alábbi szakaszok segítségével válassza ki az igényeinek megfelelő kapcsolati topológiát. Az ábrák bemutatják a fő alapvető topológiákat, de lehetséges összetettebb konfigurációk használata a diagramokat útmutatásként hozhat létre.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Helyek közötti és többhelyes (IPsec/IKE VPN-alagút)

### <a name="site-to-site"></a>Helyek közötti kapcsolat

A helyek közötti (Site-to-Site, S2S) VPN Gateway-kapcsolat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztüli kapcsolat. Ez a kapcsolattípus egy VPN-eszköz, amely a helyszínen található, és hozzá van rendelve a nyilvános IP-cím szükséges. Ez az eszköz nem található a NAT mögötti. A helyek közötti kapcsolatok létesítmények közötti és hibrid konfigurációk esetében is alkalmazhatók.

![Site-to-site VPN kapcsolat konfigurációja – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Többhelyes kapcsolat

Ez a típusú kapcsolat a helyek közötti kapcsolat egy változata. A virtuális hálózati átjáróról több VPN-kapcsolatot hoz létre, amelyek általában több helyszíni helyhez csatlakoznak. Ha több kapcsolattal dolgozik, Útvonalalapú VPN-típust (más néven dinamikus átjáró klasszikus virtuális hálózatok használata során.) kell használnia Mivel minden virtuális hálózat csak egy VPN-átjáróval rendelkezhet, az átjárón keresztüli összes kapcsolat osztozik a rendelkezésre álló sávszélességen. Ezt gyakran „többhelyes” kapcsolatnak nevezik.

![Azure VPN Gateway többhelyes kapcsolat – példa](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway termékváltozatok

Az Azure stack-beli virtuális hálózati átjáró létrehozásakor meg kell adnia a használni kívánt Termékváltozatot. VPN gateway a következő termékváltozatok támogatottak:

* Alapszintű
* Standard
* HighPerformance

Amikor kiválaszt egy átjáró magasabb szintű Termékváltozatát, mint például az alapszintű Standard vagy HighPerformance Standard vagy Basic keresztül, az átjáró kiosztott több processzort és hálózati sávszélességet. Ennek eredményeképpen az átjáró nagyobb hálózati átviteli sebesség a virtuális hálózat is támogatja.

Az Azure Stack nem támogatja az UltraPerformance átjáró-Termékváltozat kizárólag az Express Route használt.

Termékváltozat kiválasztásakor, vegye figyelembe a következőket:

* Az Azure Stack nem támogatja a házirendalapú átjárók.
* Az alapszintű termékváltozat nem támogatott a Border Gateway Protocol (BGP).
* Az ExpressRoute-VPN-átjárók egyszerre konfigurált konfigurációk nem támogatottak az Azure Stackben.
* Aktív/aktív módú S2S VPN Gateway-kapcsolatokat csak a nagy teljesítményű termékváltozaton lehet konfigurálni.

## <a name="estimated-aggregate-throughput-by-sku"></a>A termékváltozat becsült összesített átviteli sebessége

Az alábbi táblázatban az átjárótípusok és azok becsült összesített átviteli sebessége tekinthető meg az átjárók termékváltozata alapján.

|   | VPN Gateway teljesítménye *(1)* | VPN Gateway maximális IPsec-alagútjainak *(2)* |
|-------|-------|-------|
|**Alapszintű Termékváltozat** ***(3)***    | 100 Mbps  | 10    |
|**A standard Termékváltozat**       | 100 Mbps  | 10    |
|**Nagy teljesítményű Termékváltozat** | 200 Mbps    | 5 |

**Tábla megjegyzések:**

*Megjegyzés: (1)* – VPN átviteli sebesség nem egy garantált átviteli létesítmények közötti kapcsolatok az interneten keresztül. Legyen a maximális mérhető teljesítmény.  
*Megjegyzés (2)* -alagutak maximális száma az összes összes előfizetéshez tartozó Azure Stack üzemelő példányonként.  
*Megjegyzés (3)* – az alapszintű Termékváltozat esetén a BGP-útválasztás nem támogatott.

>[!NOTE]
>Csak egy helyek közötti VPN-kapcsolat hozható létre az Azure Stack két telepítése között.  A platform, amely csak egyetlen VPN-kapcsolat az azonos IP-cím lehetővé teszi egy korlátozás miatt nem lehetséges.  Mivel az Azure Stack kihasználja a több-bérlős átjáró, amely az összes VPN-átjárók egyetlen nyilvános IP-cím az Azure Stack rendszerben használja, lehet csak egy VPN-kapcsolatot két Azure Stack rendszerek között.  Ez a korlátozás is vonatkozik csatlakozik egynél több helyek közötti VPN-kapcsolatok bármely VPN-átjáró, amely egyetlen IP-címet használ.  Az Azure Stack nem teszi lehetővé ugyanazon IP-cím használatával hozható létre egynél több helyi hálózati átjáró erőforrás.


## <a name="next-steps"></a>További lépések

[VPN gateway konfigurációs beállításairól az Azure Stackhez](azure-stack-vpn-gateway-settings.md)
