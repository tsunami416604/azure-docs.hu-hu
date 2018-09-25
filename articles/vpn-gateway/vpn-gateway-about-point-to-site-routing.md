---
title: Kapcsolatos Azure pont – hely Útválasztás |} A Microsoft Docs
description: Ez a cikk segít megérteni a pont – hely VPN-útválasztás működését.
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
ms.openlocfilehash: 620a2bf9221bdb7c46dc36a2b3ed23d853faff35
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031715"
---
# <a name="about-point-to-site-vpn-routing"></a>Tudnivalók a pont–hely VPN-útválasztásról

Ez a cikk segít megérteni, hogyan viselkedik Azure pont – hely VPN-útválasztás. P2S VPN-útválasztási viselkedés szolgáltatás függ az ügyfél operációs rendszer, a protokoll, a VPN-kapcsolatot, és hogyan a virtuális hálózatok (Vnetek) csatlakoznak egymáshoz.

Az Azure jelenleg két protokollt támogat a távoli hozzáféréshez, az IKEv2 és SSTP. Az IKEv2 az számos, beleértve a Windows, Linux, MacOS, Android és IOS-es ügyfél operációs rendszereken támogatott. Az SSTP csak a Windows támogatott. Ha lehet módosítani a hálózat topológiáját, és rendelkezik a Windows VPN-ügyfelek, a VPN-ügyfélcsomag Windows-ügyfelek kell letölthető, és újra telepíteni ahhoz, hogy az ügyfél a alkalmazni a módosításokat.

> [!NOTE]
> Ez a cikk csak IKEv2 vonatkozik.
>

## <a name="diagrams"></a>A diagramok

Számos különböző diagramok ebben a cikkben. Minden szakasz egy másik topológia vagy a konfiguráció látható. Ez a cikk az alkalmazásában, a Site-to-Site (S2S) és a VNet – VNet kapcsolatokhoz ugyanúgy működnek, IPsec-alagutak mindkettő. Ez a cikk az összes VPN-átjárók, az útválasztó-alapú.

## <a name="isolatedvnet"></a>Egy elkülönített virtuális hálózat

A pont – hely VPN gateway-kapcsolat ebben a példában egy virtuális hálózat címtere nem csatlakozó vagy bármely más virtuális hálózattal (VNet1) társviszonyban van. Ebben a példában az SSTP vagy IKEv2-ügyfelek hozzáférhetnek a VNet1.

![izolált, virtuális hálózatok közötti útválasztást](./media/vpn-gateway-about-point-to-site-routing/1.jpg "elszigetelt virtuális hálózatok közötti útválasztást")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1

## <a name="multipeered"></a>Több társviszonyban lévő virtuális hálózatok

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. Vnet1 felől a VNet2 társviszonyban áll. 2 virtuális hálózat társviszonyban áll a VNet3. Vnet1 felől a vnet4 felé társviszonyban áll. Nincs nincs közvetlen társviszony-létesítés VNet1 és a VNet3 között. VNet1 rendelkezik "Átjárótranzit engedélyezése" és a VNet2 "Távoli átjárók használata" engedélyezve van.

Windows-ügyfelek hozzáférhetnek közvetlenül társviszonyban lévő virtuális hálózatok, de a VPN-ügyfél kell újra le kell tölteni minden olyan módosítások, virtuális hálózatok közötti társviszony vagy a hálózati topológia esetén. Nem-Windows-ügyfelek hozzáférhetnek közvetlenül társviszonyban lévő virtuális hálózatok. Hozzáférés nem tranzitív, és csak közvetlenül társviszonyban lévő virtuális hálózatok korlátozódik.

![több virtuális hálózatok társviszonyba](./media/vpn-gateway-about-point-to-site-routing/2.jpg "több társviszonyban álló virtuális hálózatok")

### <a name="address-space"></a>Címtér:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Vnet4 felé: 10.4.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Nem Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2 és a vnet4 felé, de a VPN-ügyfél kell újra le kell tölteni a topológia módosítások érvénybe léptetéséhez.

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2 és a vnet4 felé

## <a name="multis2s"></a>Az S2S VPN-nel csatlakoztatott virtuális hálózatokat

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. Vnet1 felől a VNet2 csatlakoztatva van a Site-to-Site VPN-kapcsolat használatával. VNet2 csatlakoztatva van, és a vnet3 között helyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy Site-to-Site VPN kapcsolatot a VNet1 és a VNet3 között. Az összes hely – hely kapcsolat nem futnak a BGP-útválasztási.

A Windows vagy egy másik támogatott operációs rendszer, az ügyfelek csak tudja elérni a VNet1. További virtuális hálózatok eléréséhez BGP kell használni.

![több virtuális hálózatok és S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "több virtuális hálózatok és S2S")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek csakis azokhoz a VNet1

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="multis2sbgp"></a>Csatlakozás egy S2S VPN (BGP) használatával több virtuális hálózaton

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. Vnet1 felől a VNet2 csatlakoztatva van a Site-to-Site VPN-kapcsolat használatával. VNet2 csatlakoztatva van, és a vnet3 között helyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy Site-to-Site VPN kapcsolatot a VNet1 és a VNet3 között. Az összes hely – hely kapcsolat futtatja a BGP-útválasztási.

A Windows vagy egy másik támogatott operációs rendszer ügyfelek hozzáférhetnek a Site-to-Site VPN-kapcsolattal csatlakozó összes virtuális hálózatok, de lehet manuálisan hozzáadni a Windows-ügyfelek kell kapcsolódó virtuális hálózatok útvonalakat.

![több virtuális hálózatok és S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "több virtuális hálózatok és S2S BGP")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2 és a VNet3, de a VNet2 és a VNet3 útvonalak kell kézzel felvenni.

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2 és a VNet3

## <a name="vnetbranch"></a>Egy VNet és a egy fiókiroda

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. VNet1 nem csatlakoztatott / társviszonyban-más virtuális hálózattal, de egy helyszíni helyhez, amelyen nem fut a BGP helyek közötti VPN-kapcsolaton keresztül csatlakozik.

Windows- és nem Windows-ügyfelek csak tudja elérni a VNet1.

![útválasztás virtuális hálózat és a egy fiókiroda](./media/vpn-gateway-about-point-to-site-routing/5.jpg "útválasztás a virtuális hálózat és a egy fiókiroda")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a csak a VNet1

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="vnetbranchbgp"></a>Egy VNet és a egy fiókiroda (BGP)

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. VNet1 nincs csatlakoztatva, vagy bármely más virtuális hálózat társviszonyban, de csatlakoztatva van egy helyszíni helyhez (Site1) futó BGP helyek közötti VPN-kapcsolaton keresztül.

Windows-ügyfelek hozzáférhetnek a virtuális hálózat és a fiókiroda (Site1), de a útvonalakat Site1 manuálisan kell adni az ügyfélnek. Nem-Windows-ügyfelek hozzáférhetnek a virtuális hálózathoz, valamint a helyi fiókirodában.

![egy VNet és a egy fiókiroda (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "egy virtuális hálózat és a egy fiókiroda")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 192.168.0.0/24

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* Windows-ügyfelek hozzáférhetnek a VNet1 és Site1, de Site1 útvonalakat kell kézzel felvenni.

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1 és Site1.


## <a name="multivnets2sbranch"></a>Csatlakoztatva (S2S) és a fiókirodák használatával több virtuális hálózaton

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. Vnet1 felől a VNet2 csatlakoztatva van a Site-to-Site VPN-kapcsolat használatával. VNet2 csatlakoztatva van, és a vnet3 között helyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy helyek közötti VPN-alagutat a VNet1 és a VNet3 hálózat között. VNet3 egy fiókiroda (Site1) Site-to-Site VPN-kapcsolattal csatlakozik. Az összes VPN-kapcsolatok nem futnak a BGP.

Összes ügyfél csak VNet1 hozzáférhet.

![többszörös virtuális hálózatok közötti (S2S) és fiókirodai office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "többszörös virtuális hálózatok közötti (S2S) és fiókirodai office")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Útvonalakat, adja hozzá az ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows ügyfelek hozzáférhetnek a VNet1 csak

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1 csak

## <a name="multivnets2sbranchbgp"></a>Csatlakoztatva (S2S) és a fiókirodák (BGP) használatával több virtuális hálózaton

Ebben a példában a pont – hely VPN-átjáró kapcsolatot a VNet1 van. Vnet1 felől a VNet2 csatlakoztatva van a Site-to-Site VPN-kapcsolat használatával. VNet2 csatlakoztatva van, és a vnet3 között helyek közötti VPN-kapcsolat használatával. Nincs közvetlen társviszony-létesítés vagy helyek közötti VPN-alagutat a VNet1 és a VNet3 hálózat között. VNet3 egy fiókiroda (Site1) Site-to-Site VPN-kapcsolattal csatlakozik. Az összes VPN-kapcsolatok BGP futnak.

Windows-ügyfelek hozzáférhetnek a virtuális hálózatok és helyek közötti VPN-kapcsolatot, de a VNet2, a VNet3 és Site1 útvonalakat használatával csatlakoztatott helyek manuálisan kell hozzáadni az ügyfél. Nem-Windows-ügyfelek hozzáférhetnek a virtuális hálózatok és a manuális beavatkozás nélküli Site-to-Site VPN-kapcsolattal csatlakoztatott helyek. A hozzáférés tranzitív, és ügyfelek hozzáférhetnek az összes kapcsolódó virtuális hálózatok és helyek (helyszíni) erőforrásokhoz.

![többszörös virtuális hálózatok közötti (S2S) és fiókirodai office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "többszörös virtuális hálózatok közötti (S2S) és fiókirodai office")

### <a name="address-space"></a>Címtér

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Útvonalak hozzáadása

* Útvonalakat, adja hozzá az ügyfelek: 10.1.0.0/16, 192.168.0.0/24

* Nem-Windows-ügyfelek hozzáadott útvonalak: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Hozzáférés

* A Windows-ügyfelek hozzáférhetnek a VNet1, a VNet2, a VNet3 és Site1, de a VNet2, a VNet3 és Site1 útvonalak manuálisan kell adni az ügyfélnek.

* Nem-Windows-ügyfelek hozzáférhetnek a VNet1, a Vnet2, a VNet3 és Site1.

## <a name="next-steps"></a>További lépések

Lásd: [P2S VPN-kapcsolattal az Azure portal használatával hozzon létre](vpn-gateway-howto-point-to-site-resource-manager-portal.md) a P2S VPN létrehozásának megkezdéséhez.
