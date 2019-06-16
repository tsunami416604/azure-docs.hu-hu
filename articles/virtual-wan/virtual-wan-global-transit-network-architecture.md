---
title: Az Azure virtuális WAN globális tranzit hálózati architektúra |} A Microsoft Docs
description: További információk a globális tranzit hálózati architektúra az virtuális WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 114d11f98c6181a03f5ce52527b5e2efea468c42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65965985"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globális tranzit hálózati architektúra és a virtuális WAN

Globális tranzit hálózati architektúra összevonni, csatlakoztatása és felügyelete a felhő-központú modern vállalati informatikai nagyvállalatok kik. Egy modern felhő-központú nagyvállalati, a hálózati forgalom nem kell lenniük a HQ backhauled. Globális tranzit hálózati architektúra jól ismert hálózatkezelési fogalmai és új fogalmakat, felhőbeli és a felhő alapú architektúrák egyedi alapul.

![architektúra](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**1. ábra: Virtuális WAN-globális átviteli hálózat**

A modern vállalkozások széles körű összekapcsoltság hyper elosztott alkalmazások, adatok és a felhasználók között a felhőben és helyszíni van szükség. Az Azure virtuális WAN lehetővé teszi, hogy egy globális tranzit hálózati architektúrát globálisan elosztott, virtuális hálózatok, webhelyek, alkalmazások és felhasználók között szabaddá tehető, bármely kapcsolat engedélyezésével. Az Azure virtuális WAN egy Microsoft által felügyelt szolgáltatást. A hálózati összetevők, amelyek a szolgáltatás áll, amelyek üzemeltetett és a Microsoft felügyeli. Virtuális WAN kapcsolatos további információkért lásd: a [virtuális WAN áttekintése](virtual-wan-about.md) cikk.

Az Azure virtuális WAN architektúra az Azure-régiók, amelyhez Ön is csatlakozhat az ágak hubs szolgál. Az ágak a csatlakozás után kihasználhatja az Azure gerinchálózatra közötti ág létrehozására és opcionálisan ág ágba irányuló kapcsolat.

Virtuális WAN egyetlen virtuális WAN hubra létrehozásával a régióban, amelyben a küllők (ág, virtuális hálózatok, a felhasználók) a legnagyobb száma, és majd csatlakozzon a küllők, amelyek a hub más régiókban is létrehozhat. Azt is megteheti Ha küllők elszórtan, is hozza létre a regionális központok és a hubs hálózatokban. A hubs az azonos virtuális WAN minden részét képezik, de azok különböző regionális szabályzatok társíthatók.

## <a name="hub"></a>Központ-küllő átvitel

A globális tranzit hálózati architektúra egy klasszikus Központ-küllő kapcsolat modell, ahol a felhőben üzemeltetett hálózati "hub" lehetővé teszi, előfordulhat, hogy elosztva a különböző típusú "küllők" végpontok közötti tranzitív kapcsolat alapján történik.
  
Ebben a modellben egy adott küllőre is lehet:

* Virtuális hálózat (Vnetek)
* Fizikai fiókiroda-helyhez
* A távoli felhasználó
* Internet

![küllős topológiájú globális átvitel diagramja](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**2. ábra: Hub-and-spoke**

2\. ábrán látható, a logikai nézetében a globális hálózat, ahol a földrajzilag elosztott felhasználók, a fizikai helyeken és a virtuális hálózatok vannak összekapcsolva egy felhőalapú hálózati központon keresztül. Ez az architektúra lehetővé teszi, hogy a hálózati végpontok közötti logikai egyugrásos átvitel kapcsolat. A küllők köti össze a hub különböző hálózati hasonló Azure-szolgáltatásokat az ExpressRoute és site-to-típusú VPN ágak fizikai, virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok és a pont – hely VPN-t a távoli felhasználók számára.

## <a name="crossregion"></a>Régiók közötti kapcsolat

A vállalati a felhőbeli erőforrás-igényű általában a fizikai jelenlét követi. A legtöbb vállalat a fizikai hely és a felhasználók legközelebb eső régióból hozzáférés a felhőhöz. A fő résztvevők globális hálózati architektúra egyik hálózati entitásokat és a végpontok közötti régiók közötti kapcsolat. A felhőbeli tárhely több régióban is kiterjedhetnek. Ez azt jelenti, hogy egy adott régióban a felhőhöz csatlakoztatott ág érkező forgalom elérje egy másik ágban vagy egy másik régióba eseményközpont-hub kapcsolat, amely jelenleg előzetes verzióban érhető el a virtuális hálózathoz.

## <a name="any"></a>Bármely kapcsolat

Globális tranzit hálózati architektúra lehetővé teszi, hogy *– bármely kapcsolat* egy központi hálózat központon keresztül. Ez az architektúra kiküszöböli, vagy csökkenti a teljes rácsot alkotó vagy részleges háló kapcsolati modellek hozhat létre és kezelhet az összetettebb szükségességét. Emellett az útválasztási vezérlőelem a Központ-küllő és a háló hálózatok is egyszerűbb, konfigurálnia és karbantartania.

Bármely kapcsolat keretén belül egy globális architektúra lehetővé teszi a vállalatok a globálisan elosztott felhasználókkal, ágak, adatközpontok, virtuális hálózatok és az alkalmazások számára az átvitel hubon keresztül csatlakozni egymáshoz. A tranzit hub rekordrendszerként globális átvitel során.

![forgalom elérési utak](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**3. ábra: Virtuális WAN-hálózati forgalom elérési utak**

Az Azure virtuális WAN támogatja a következő globális átvitel kapcsolati útvonalat. A betűk zárójelben képezze le a 3. ábra.

* Ág közötti (a)  
* Fiókiroda-ág (b)
* Távoli felhasználó közötti (c)
* Felhasználó-az-fiókirodai (d)
* A virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok (e) használata
* Az ExpressRoute globális elérhetőséggel 

### <a name="branchvnet"></a>Branch-to-VNet

Fiókirodai hálózatok az Azure virtuális WAN által támogatott elsődleges útvonal. Ezt az elérési utat ágak csatlakozhat az Azure IAAS vállalati számítási feladatokat az Azure virtuális hálózat üzembe helyezett teszi lehetővé. Ágak csatlakoztathatók a virtuális WAN ExpressRoute- vagy helyek közötti VPN-n keresztül. A virtuális hálózatokhoz kapcsolódó virtuális hálózatok közötti kapcsolatok keresztül a virtuális WAN hubs forgalom továbbítására.

### <a name="branchbranch"></a>Fiókiroda-ág

Ágak csatlakoztathatók az ExpressRoute-Kapcsolatcsoportok és/vagy helyek közötti VPN-kapcsolatok használatával az Azure virtuális WAN felé. Az ágak csatlakozhat a virtuális WAN-hubhoz, amely az ág legközelebb eső régióban található.

Ez a beállítás lehetővé teszi a vállalatok kihasználhatja az Azure gerinchálózatra ágak csatlakozni. Annak ellenére, hogy ez a képesség érhető el, az Azure virtuális WAN keresztül, és a egy privát WAN használatával kapcsolódó ágak előnyei kell mérjük.

### <a name="usertovnet"></a>Távoli felhasználó közötti kapcsolat

Közvetlen és biztonságos távoli hozzáférést az Azure-hoz pont – hely kapcsolatok egy távoli felhasználó ügyfél virtuális WAN használatával engedélyezheti. Vállalati távoli felhasználók már nem kell a vállalati VPN használatával felhőbe hairpin.

### <a name="usertobranch"></a>Távoli felhasználó-ág

A távoli felhasználó ág útvonal lehetővé teszi a távoli felhasználók által a felhőben áthaladó használó Azure hozzáférés a helyszíni számítási feladatok és alkalmazások egy pont – hely kapcsolat. Ezt az elérési utat a rugalmasságot biztosít a távoli felhasználók számára, amelyek az üzembe helyezett Azure és a helyszíni számítási feladatokat. Vállalatok központi felhőalapú biztonságos távoli hozzáférést szolgáltatás az Azure virtuális WAN engedélyezheti.

### <a name="vnetvnet"></a>Használatával a virtuális hálózatok közötti társviszony-létesítés virtuális hálózatok közötti átvitel

Egymáshoz virtuális hálózatok összekapcsolása annak érdekében, hogy támogatja a többrétegű alkalmazások, amelyek több virtuális, virtuális hálózatok közötti társviszony használja. Azure virtuális WAN keresztül a VNet – VNet átviteli forgatókönyv jelenleg nem támogatott, de az Azure tervbe van véve. Az ajánlott megoldás, csatlakoznia kell egymáshoz virtuális hálózatok virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony-Létesítésen keresztül is. [Átjárói átvitel](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (a gyorsjavítások gyökérmappájában lévő virtuális hálózatok közötti társviszony) nem kötelező a virtuális WAN, mert a virtuális WAN automatikusan lehetővé teszi, hogy átjárói átvitel.

### <a name="globalreach"></a>Az ExpressRoute globális elérhetőséggel

Az ExpressRoute egy magán- és rugalmas módja a helyszíni hálózatok csatlakoztatása a Microsoft Cloud. Az ExpressRoute globális elérhetőségű egy kiegészítő szolgáltatására, az expressroute-hoz. A globális elérhetőségű kapcsolat ExpressRoute-Kapcsolatcsoportok teszik egy magánhálózaton a helyszíni hálózat között. Azure virtuális WAN ExpressRoute használatával csatlakoztatott ágak van szükség, az ExpressRoute globális elérhetőségű kommunikálni egymással.

Ebben a modellben minden egyes fiókiroda a virtuális WAN hub, az ExpressRoute használatával csatlakoztatott virtuális hálózatok közötti ág útvonal segítségével csatlakozhat. Ág ágba irányuló forgalom nem a hub átvitel, mert ExpressRoute globális elérhetőségű lehetővé teszi, hogy egy több optimális útvonalat Azure WAN keresztül.

## <a name="security"></a>Biztonság és szabályzatok vezérlő

A virtuális hálózati kapcsolatok küllős potenciálisan látja az összes átmenő forgalom. Lehet, hogy a gazdagép központi hálózati funkciók és szolgáltatások, például ilyen egy felhőbeli útválasztás, a hálózati házirend és a biztonsági és az Internet-hozzáférés-vezérlés ezen a helyen.

## <a name="next-steps"></a>További lépések

Hozzon létre egy kapcsolatot a virtuális WAN segítségével.

* [Hely – hely kapcsolatok virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
* [Virtuális WAN használatával, a pont – hely kapcsolatokhoz](virtual-wan-point-to-site-portal.md)
* [Az ExpressRoute-kapcsolatok virtuális WAN használatával](virtual-wan-expressroute-portal.md)
