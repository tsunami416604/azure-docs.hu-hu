---
title: 'Azure VPN-átjáró: A P2S-útválasztás'
description: Ez a cikk segít megérteni, hogyan viselkedik a point-to-site VPN-útválasztás.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239751"
---
# <a name="about-point-to-site-vpn-routing"></a>Tudnivalók a pont–hely VPN-útválasztásról

Ez a cikk segít megérteni, hogyan viselkedik az Azure Point-to-Site VPN-útválasztás. A P2S VPN-útválasztási viselkedés az ügyfél operációs rendszerétől, a VPN-kapcsolathoz használt protokolltól, valamint a virtuális hálózatok (virtuális hálózatok) egymáshoz való csatlakozásától függ.

Az Azure jelenleg két protokollt támogat a táveléréshez, az IKEv2-t és az SSTP-t. Az IKEv2 számos ügyféloperációs rendszeren támogatott, beleértve a Windows, Linux, MacOS, Android és iOS rendszereket. Az SSTP csak Windows rendszeren támogatott. Ha módosítja a hálózat topológiáját, és windowsos VPN-ügyfelekkel rendelkezik, a Windows-ügyfelekhez való VPN-ügyfélcsomagot újra le kell tölteni és újra telepíteni kell ahhoz, hogy a módosítások at alkalmazzanak az ügyfélre.

> [!NOTE]
> Ez a cikk csak az IKEv2-re vonatkozik.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>A diagramok

Ebben a cikkben számos különböző diagram található. Minden szakasz más-más topológiát vagy konfigurációt jelenít meg. A cikk alkalmazásában a helyek közötti (S2S) és a virtuális hálózat–virtuális hálózat kapcsolatok ugyanúgy működnek, mint mindkettő IPsec-alagút. A cikkben szereplő összes VPN-átjáró útvonalalapú.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Egy elkülönített virtuális hálózat

Ebben a példában a point-site VPN átjáró kapcsolat a virtuális hálózat, amely nem csatlakozik, vagy társviszonyba más virtuális hálózat (VNet1). Ebben a példában az ügyfelek hozzáférhetnek a VNet1.

![elkülönített virtuális hálózat útválasztása](./media/vpn-gateway-about-point-to-site-routing/1.jpg "elkülönített virtuális hálózat útválasztása")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a VNet1 szolgáltatáshoz

* A nem Windows rendszerű ügyfelek hozzáférhetnek a Virtuális hálózathoz1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Több társviszony-létesített virtuális hálózat

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 társviszonyban van a VNet2-vel. A VNet 2 társviszonyban van a VNet3-mal. A VNet1 társviszonyban van a VNet4-vel. A Virtuális hálózat1 és a Virtuálishálózat3 között nincs közvetlen társviszony-létesítés. A VNet1 rendelkezik "Átjáró-átvitel engedélyezése" és a VNet2 és a VNet4 "Távoli átjárók használata" engedélyezve van.

A Windows rendszert használó ügyfelek közvetlenül hozzáférhetnek a társviszony-létesítéshez, de a VPN-ügyfelet újra le kell tölteni, ha a virtuális hálózat társviszony-létesítése vagy a hálózati topológia módosul. A nem Windows-ügyfelek közvetlenül társviszonyban álló virtuális hálózatokat érhetnek el. A hozzáférés nem tranzitív, és csak közvetlenül társviszonyban szereplő virtuális hálózatokra korlátozódik.

![több társviszony-létesített virtuális hálózat](./media/vpn-gateway-about-point-to-site-routing/2.jpg "több társviszony-létesített virtuális hálózat")

### <a name="address-space"></a>Címterület:

* Virtuális hálózat1: 10.1.0.0/16

* Virtuális hálózat2: 10.2.0.0/16

* Virtuális hálózat3: 10.3.0.0/16

* Virtuális hálózat4: 10.4.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a Virtuális hálózathoz1, a VNet2 és a VNet4, de a VPN-ügyfelet újra le kell tölteni ahhoz, hogy a topológia módosításai érvénybe lépjenek.

* A nem Windows-ügyfelek hozzáférhetnek a VNet1, vNet2 és VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Több virtuális hálózat csatlakoztatva S2S VPN-nel

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 a VNet2-hez kapcsolódik a helyek közötti VPN-kapcsolaton keresztül. A VNet2 a virtuális hálózathoz csatlakozik egy helyek közötti VPN-kapcsolaton keresztül. Nincs közvetlen társviszony-létesítési vagy helyek közötti VPN-kapcsolat a Virtuális hálózat1 és a VNet3 között. A helyek közötti kapcsolatok nem futnak BGP-t útválasztáshoz.

A Windows vagy más támogatott operációs rendszert használó ügyfelek csak a Virtuális hálózat1 szolgáltatáshoz férhetnek hozzá. További virtuális hálózatok eléréséhez bgp kell használni.

![több virtuális hálózat és S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "több virtuális hálózat és S2S")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* Virtuális hálózat2: 10.2.0.0/16

* Virtuális hálózat3: 10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek csak a Virtuális hálózathoz tudnak hozzáférni1

* A nem Windows-ügyfelek csak a VNet1-hez férhetnek hozzá

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Több virtuális hálózat csatlakoztatva S2S VPN-nel (BGP)

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 a VNet2-hez kapcsolódik a helyek közötti VPN-kapcsolaton keresztül. A VNet2 a virtuális hálózathoz csatlakozik egy helyek közötti VPN-kapcsolaton keresztül. Nincs közvetlen társviszony-létesítési vagy helyek közötti VPN-kapcsolat a Virtuális hálózat1 és a VNet3 között. Minden hely közötti kapcsolat bgp-t futtat az útválasztáshoz.

A Windows vagy más támogatott operációs rendszert használó ügyfelek hozzáférhetnek a helyek közötti VPN-kapcsolaton keresztül csatlakoztatott összes virtuális hálózathoz, de a csatlakoztatott virtuális hálózatokhoz való útvonalakat manuálisan hozzá kell adni a Windows-ügyfelekhez.

![több virtuális hálózat és S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "több virtuális hálózat és S2S BGP")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* Virtuális hálózat2: 10.2.0.0/16

* Virtuális hálózat3: 10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a Virtuálishálózat1, a VNet2 és a VNet3, de a Virtuálishálózat2 és a VNet3 útvonalakmanuálisan hozzá kell adni.

* A nem Windows-ügyfelek hozzáférhetnek a VNet1, vNet2 és VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Egy virtuális hálózat és egy fiókiroda

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 nem csatlakozik/társviszonyba áll más virtuális hálózattal, hanem egy olyan helyek közötti VPN-kapcsolaton keresztül csatlakozik egy helyszíni helyhez, amely nem BGP-t futtat.

A Windows és a nem Windows-ügyfelek csak a Virtuális hálózathoz 1 férhetnek hozzá.

![útválasztás virtuális hálózattal és fiókirodával](./media/vpn-gateway-about-point-to-site-routing/5.jpg "útválasztás virtuális hálózattal és fiókirodával")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* 1. oldal: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek csak a Virtuális hálózathoz érhetők el1

* A nem Windows-ügyfelek csak a VNet1-hez férhetnek hozzá

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Egy virtuális hálózat és egy fiókiroda (BGP)

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 nem csatlakozik más virtuális hálózathoz, hanem egy helyszíni helyhez (Site1) csatlakozik a BGP-t futtató helyek közötti VPN-kapcsolaton keresztül.

A Windows-ügyfelek hozzáférhetnek a virtuális hálózathoz és a fiókirodához (Site1), de a Site1 helyre vezető útvonalakat manuálisan hozzá kell adni az ügyfélhez. A nem Windows-ügyfelek hozzáférhetnek a virtuális hálózathoz, valamint a helyszíni fiókirodához.

![egy virtuális hálózat és egy fiókiroda (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "egy virtuális hálózat és egy fiókiroda")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* 1. oldal: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a Virtuális hálózathoz1 és a Site1 webhelyhez, de az 1.

* A nem Windows-ügyfelek hozzáférhetnek a VNet1 és a Site1 webhelyhez.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Több virtuális hálózat csatlakoztatva s2s-sel és fiókirodával

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 a VNet2-hez kapcsolódik a helyek közötti VPN-kapcsolaton keresztül. A VNet2 a virtuális hálózathoz csatlakozik egy helyek közötti VPN-kapcsolaton keresztül. A VNet1 és a VNet3 hálózatok között nincs közvetlen társviszony-létesítés i vagy helyek közötti VPN-alagút. A VNet3 egy fiókirodához (Site1) csatlakozik a helyek közötti VPN-kapcsolaton keresztül. Nem minden VPN-kapcsolat on-ra bgp fut.

Az összes ügyfél csak a Virtuális hálózathoz fér hozzá.

![multi-VNet S2S és fiókiroda](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S és fiókiroda")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* Virtuális hálózat2: 10.2.0.0/16

* Virtuális hálózat3: 10.3.0.0/16

* 1. oldal: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek csak a VNet1-et érhetik el

* A nem Windows-ügyfelek csak a VNet1-hez férhetnek hozzá

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Több virtuális hálózat csatlakoztatva S2S-sel és fiókirodával (BGP)

Ebben a példában a point-to-site VPN átjáró kapcsolat a VNet1. A VNet1 a VNet2-hez kapcsolódik a helyek közötti VPN-kapcsolaton keresztül. A VNet2 a virtuális hálózathoz csatlakozik egy helyek közötti VPN-kapcsolaton keresztül. A VNet1 és a VNet3 hálózatok között nincs közvetlen társviszony-létesítés i vagy helyek közötti VPN-alagút. A VNet3 egy fiókirodához (Site1) csatlakozik a helyek közötti VPN-kapcsolaton keresztül. Minden VPN-kapcsolat BGP-t futtat.

A Windows rendszert használó ügyfelek elérhetik a helyek közötti VPN-kapcsolaton keresztül összekapcsolt virtuális hálózatokat és helyeket, de a Virtuálishálózat2, a VNet3 és a Site1 útvonalakat manuálisan hozzá kell adni az ügyfélhez. A nem Windows-ügyfelek manuális beavatkozás nélkül férhetnek hozzá a helyek közötti VPN-kapcsolaton keresztül csatlakoztatott virtuális hálózatokhoz és helyekhez. A hozzáférés tranzitív, és az ügyfelek hozzáférhetnek az erőforrásokhoz az összes csatlakoztatott virtuális hálózaton és helyen (a helyszínen).

![multi-VNet S2S és fiókiroda](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S és fiókiroda")

### <a name="address-space"></a>Címtér

* Virtuális hálózat1: 10.1.0.0/16

* Virtuális hálózat2: 10.2.0.0/16

* Virtuális hálózat3: 10.3.0.0/16

* 1. oldal: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* A Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelekhez hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a Virtuálishálózat1, VNet2, VNet3 és Site1, de útvonalak VNet2, VNet3 és Site1 manuálisan hozzá kell adni az ügyfélhez.

* A nem Windows-ügyfelek hozzáférhetnek a VNet1, vnet2, VNet3 és Site1.

## <a name="next-steps"></a>További lépések

Lásd: [P2S VPN létrehozása az Azure Portalon a](vpn-gateway-howto-point-to-site-resource-manager-portal.md) P2S VPN létrehozásának megkezdéséhez.
