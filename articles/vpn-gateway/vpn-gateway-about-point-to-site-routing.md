---
title: Tudnivalók Azure pont-pont útválasztási |} Microsoft Docs
description: Ez a cikk segít megérteni, hogyan viselkedik pont – hely típusú VPN-útválasztást.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: d25709fb4abb1b8a35596c3dc246f7419a99419b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="about-point-to-site-vpn-routing"></a>Pont – hely típusú VPN-útválasztási információ

Ez a cikk segít megérteni, hogyan viselkedik Azure pont – hely típusú VPN-útválasztást. P2S VPN útválasztási működése függ, az ügyfél operációs rendszer, a VPN-kapcsolatot, és hogyan a virtuális hálózatokon (Vnetek) csatlakoznak egymáshoz használt protokollt.

Azure jelenleg két protokollt a távoli hozzáféréshez, az IKEv2 és SSTP. IKEv2 többek között Windows, Linux, MacOS, Android és iOS sok ügyfél operációs rendszereken támogatott. Az SSTP csak a Windows támogatott. Ha módosítja a hálózat topológiáját, és rendelkezik a Windows VPN-ügyfelek, a VPN-ügyfélcsomag, a Windows-ügyfelek kell letölti és telepíti újra a változások, az ügyfél alkalmazandó.

> [!NOTE]
> Ez a cikk csak érvényes IKEv2.
>

## <a name="diagrams"></a>A diagramok

Számos különböző diagramok ebben a cikkben. Az egyes szakaszokon a különböző topológia vagy a konfiguráció látható. Ez a cikk alkalmazásában webhelyek (közötti S2S) és VNet – VNet kapcsolatokhoz működhetnek ugyanúgy, IPsec-alagutak mindkettő. Ebben a cikkben az összes VPN-átjárók útválasztó-alapú.

## <a name="isolatedvnet"></a>Egy elkülönített hálózatok

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot egy Vnetet, amelynek nincs csatlakoztatva, vagy bármely más virtuális hálózathoz (VNet1) nincsenek társviszonyban van. Ebben a példában SSTP vagy IKEv2 használó ügyfelek VNet1 férhetnek hozzá.

![elkülönített hálózatok útválasztási](./media/vpn-gateway-about-point-to-site-routing/1.jpg "elkülönített hálózatok Útválasztás")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* -Windows alapú ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1

* Nem Windows-ügyfelek hozzáférhetnek a VNet1

## <a name="multipeered"></a>Több társviszonyban Vnetek

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 nincsenek társviszonyban, a VNet2. Virtuális hálózat 2 nincsenek társviszonyban, a VNet3. VNet1 nincsenek társviszonyban, a VNet4. Nincs nincs közvetlen társviszony-létesítés VNet1 és VNet3 között. VNet1 "Átjáró átvitel engedélyezése" pedig VNet2 "Távoli átjárók használata" engedélyezve van.

A Windows ügyfelek hozzáférhetnek a Vnetek közvetlenül társviszonyban, de a VPN-ügyfél kell újra le kell tölteni a módosítások a Vnetben társviszony-létesítés vagy a hálózati topológia esetén. Nem Windows-ügyfelek hozzáférhetnek a Vnetek közvetlenül társviszonyban. Hozzáférés nem tranzitív, és csak közvetlenül társviszonyban Vnetek korlátozódik.

![több társviszonyban Vnetek](./media/vpn-gateway-about-point-to-site-routing/2.jpg "több társviszonyban Vnetek")

### <a name="address-space"></a>Címtartomány:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* -Windows alapú ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1 VNet2 és VNet4, de a VPN-ügyfél kell újra le kell tölteni a topológia módosítások érvénybe lépéséhez.

* Nem Windows-ügyfelek hozzáférhetnek a VNet1 VNet2 és VNet4

## <a name="multis2s"></a>Az S2S VPN-nel csatlakoztatott több Vnetek

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 VNet2 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. VNet2 VNet3 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy telephelyek közötti VPN-kapcsolatot VNet1 és VNet3. Az összes hely-hely kapcsolat nem futnak BGP irányításához.

A Windows, vagy egy másik támogatott operációs rendszer, az ügyfelek csak VNet1 tudja elérni. További Vnetek eléréséhez BGP kell használni.

![több virtuális hálózatokat és S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "több virtuális hálózatokat és S2S")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek csak férhetnek hozzá VNet1

* A nem Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="multis2sbgp"></a>Csatlakozás egy S2S VPN (BGP) használatával több Vnetek

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 VNet2 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. VNet2 VNet3 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy telephelyek közötti VPN-kapcsolatot VNet1 és VNet3. Az összes hely-hely kapcsolat futnak BGP irányításához.

A Windows vagy egy másik támogatott operációs rendszer ügyfelek hozzáférhetnek a pont-pont VPN-kapcsolattal csatlakozó összes Vnetek, de csatlakoztatott Vnetek útvonalakat kell manuálisan hozzáadni a Windows-ügyfeleket.

![több virtuális hálózatokat és S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "több virtuális hálózatokat és S2S BGP")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1 VNet2 és VNet3, de az útvonalakat VNet2 és VNet3 kell kézzel felvenni.

* Nem Windows-ügyfelek hozzáférhetnek a VNet1 VNet2 és VNet3

## <a name="vnetbranch"></a>Egy VNet és egy fiókiroda

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 nem csatlakoztatott / társítottak, más virtuális hálózathoz, de egy helyszíni hely BGP nem futó telephelyek közötti VPN-kapcsolaton keresztül kapcsolódik.

Windows-ügyfelek hozzáférhetnek a VNet1 és a fiókiroda (hely1), de a hely1 útvonalakat manuálisan kell adni az ügyfélnek. Nem Windows-ügyfelek hozzáférhetnek a VNet1, valamint a helyszíni hely1.

![egy VNet és egy fiókiroda útválasztással](./media/vpn-gateway-about-point-to-site-routing/5.jpg "egy VNet és egy fiókiroda útválasztással")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* Hely1: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a csak VNet1

* A nem Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="vnetbranchbgp"></a>Egy VNet és egy fiókiroda (BGP)

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 van csatlakoztatva, vagy nem társítottak, más virtuális hálózathoz, de egy helyszíni hely (hely1) fut a BGP-webhelyek közötti VPN-kapcsolaton keresztül kapcsolódik.

Windows-ügyfelek hozzáférhetnek a virtuális hálózat és a fiókiroda (hely1), de a hely1 útvonalakat manuálisan kell adni az ügyfélnek. Nem Windows-ügyfelek hozzáférhetnek a a virtuális hálózaton, valamint a helyi fiókirodában.

![egy VNet és egy fiókiroda (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "egy VNet és egy fiókiroda")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* Hely1: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat hozzáadni a Windows-ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1 és hely1, de hely1 útvonalakat kell kézzel felvenni.

* Nem Windows-ügyfelek hozzáférhetnek a VNet1 és hely1.


## <a name="multivnets2sbranch"></a>S2S és a fiókirodák kapcsolódnak több Vnetek

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 VNet2 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. VNet2 VNet3 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy telephelyek közötti VPN-alagút a VNet1 és VNet3 hálózatok között. VNet3 egy fiókiroda (hely1) telephelyek közötti VPN-kapcsolaton keresztül kapcsolódik. Az összes VPN-kapcsolatok nem futtatja a BGP.

Minden ügyfele csak VNet1 fér hozzá.

![multi-VNet S2S és a fiókiroda office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S és a fiókiroda office")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Hely1: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat adja hozzá az ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek VNet1 csak

* A nem Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="multivnets2sbranchbgp"></a>Több Vnetek csatlakoztatott S2S és egy fiókiroda (BGP)

Ebben a példában a pont – hely típusú VPN gateway-kapcsolatot VNet1 szolgál. VNet1 VNet2 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. VNet2 VNet3 csatlakozik egy telephelyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy telephelyek közötti VPN-alagút a VNet1 és VNet3 hálózatok között. VNet3 egy fiókiroda (hely1) telephelyek közötti VPN-kapcsolaton keresztül kapcsolódik. Az összes VPN-kapcsolatok nem futtatja a BGP. Az összes VPN-kapcsolat futtatja a BGP.

A Windows ügyfelek hozzáférhetnek a Vneteket, és a telephelyek közötti VPN-kapcsolat, de VNet2, VNet3 és hely1 útvonalak használatával csatlakoztatott helyek manuálisan hozzá kell adni az ügyfélnek. Nem Windows-ügyfelek hozzáférhetnek a virtuális hálózatokat és a kézi beavatkozás nélküli webhelyek VPN-kapcsolattal csatlakoztatott helyek. A hozzáférés tranzitív, és az ügyfelek az összes kapcsolódó Vnetek és helyeken (helyszíni) erőforrásait is elérik.

![multi-VNet S2S és a fiókiroda office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S és a fiókiroda office")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Hely1: 10.101.0.0/16

### <a name="routes-added"></a>Hozzáadott útvonalak

* Útvonalakat adja hozzá az ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Útvonalak a nem Windows-alapú ügyfelek hozzá: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a VNet1, VNet2, VNet3 és hely1, de útvonalakat VNet2, VNet3 és hely1 kell manuálisan adja hozzá az ügyfelet.

* Nem Windows-ügyfelek hozzáférhetnek a VNet1, Vnet2, VNet3 és hely1.

## <a name="next-steps"></a>További lépések

Lásd: [létrehozása az Azure portál használatával P2S VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) a P2S VPN létrehozásának megkezdéséhez.
