---
title: Azure Virtual WAN globális tranzit hálózati architektúra | Microsoft Docs
description: Ismerje meg a Virtual WAN-hoz készült globális átviteli hálózati architektúrát
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695266"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globális átviteli hálózati architektúra és virtuális WAN

A vállalatok a globális átviteli hálózati architektúrát a felhőalapú modern vállalati IT-lábnyom megszilárdítására, összekapcsolására és szabályozására használják. A modern, felhőalapú nagyvállalati hálózatok esetében nem szükséges a hálózati forgalom backhauled a HQ számára. A globális átviteli hálózati architektúra a jól ismert hálózatkezelési fogalmakon, valamint a Felhőbeli és a felhőalapú architektúrákkal kapcsolatos új fogalmakon alapul.

![architektúra](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**1. ábra: Globális átviteli hálózat virtuális WAN @ no__t-0

A modern vállalatok a felhőben és a helyszíni környezetekben a Hyper-elosztott alkalmazások, az adatközpontok és a felhasználók közötti mindennapos kapcsolatokat igénylik. Az Azure Virtual WAN a globálisan elosztott virtuális hálózatok,-helyek,-alkalmazások és-felhasználók közötti, mindenütt elérhető, bármilyen típusú kapcsolat engedélyezésével lehetővé teszi a globális átviteli hálózati architektúrát. Az Azure Virtual WAN egy Microsoft által felügyelt szolgáltatás. A szolgáltatás által alkotott összes hálózati összetevőt a Microsoft üzemelteti és kezeli. A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című cikkben talál.

Az Azure-beli virtuális WAN-architektúrában az Azure-régiók olyan hubokként szolgálnak, amelyekhez az ágakat összekapcsolhatjuk. Az ágak csatlakoztatása után kihasználhatja az Azure gerincét, hogy VNet és opcionálisan fiókirodák közötti kapcsolatot hozzon létre.

Létrehozhat egy virtuális WAN-t úgy, hogy létrehoz egy virtuális WAN-központot a régiójában, amely a legnagyobb számú küllőt (ágakat, virtuális hálózatok, felhasználókat) és a más régiókban lévő küllőket csatlakoztatja a hubhoz. Ha a küllők földrajzilag vannak elosztva, a regionális hubokat is létrehozhatja és összekapcsolhatja a hubokat. A hubok ugyanahhoz a virtuális WAN-hoz tartoznak, de különböző regionális házirendekhez társíthatók.

## <a name="hub"></a>Sugaras átvitel

A globális átviteli hálózat architektúrája egy olyan klasszikus sugaras kapcsolati modellen alapul, ahol a felhőben üzemeltetett hálózat "hub" lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között terjeszthetők.
  
Ebben a modellben a küllő a következő lehet:

* Virtuális hálózat (virtuális hálózatok)
* Fizikai ág helye
* Távoli felhasználó
* Internet

![központi és küllős globális átviteli diagram](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**2. ábra: Hub-and-spoke**

A 2. ábrán a globális hálózat logikai nézete látható, ahol a földrajzilag elosztott felhasználók, fizikai helyek és virtuális hálózatok a felhőben üzemeltetett hálózati elosztón keresztül kapcsolódnak egymáshoz. Ez az architektúra lehetővé teszi a logikai egyugrásos átviteli kapcsolatok használatát a hálózati végpontok között. A küllők különböző Azure hálózati szolgáltatások, például a ExpressRoute vagy a helyek közötti VPN fizikai ágakhoz való csatlakoztatásához, a virtuális hálózatok VNet kapcsolataihoz és pont – hely VPN-hez kapcsolódnak a távoli felhasználók számára.

## <a name="crossregion"></a>Régiók közötti kapcsolat

A nagyvállalatok esetében a Felhőbeli lábnyom általában a fizikai lábnyomot követi. A legtöbb vállalat a felhőt a fizikai helyükhöz és a felhasználókhoz legközelebb eső régióból éri el. A globális hálózati architektúra egyik legfontosabb rendszerbiztonsági tagja a régiók közötti kapcsolat engedélyezése a hálózati entitások és a végpontok között. A Felhőbeli lábnyom több régióra is kiterjedhet. Ez azt jelenti, hogy egy adott régióban a felhőhöz csatlakoztatott ágakból érkező forgalom egy másik régióban vagy egy másik régió VNet is elérheti a hub – hub kapcsolat használatát, amely jelenleg az ütemterven látható.

## <a name="any"></a>Bármilyen kapcsolat

A globális átviteli hálózati architektúra *bármely – bármely kapcsolódást* lehetővé tesz egy központi hálózati hub használatával. Ez az architektúra kiküszöböli vagy csökkenti a teljes szembőséget vagy a részleges rácsvonal-kapcsolati modelleket, amelyek összetettebbek a létrehozáshoz és a karbantartáshoz. Emellett a hub-és küllős és a mesh hálózatok útválasztási vezérlése könnyebben konfigurálható és kezelhető.

Bármely – bármely kapcsolat – globális architektúra kontextusában lehetővé teszi, hogy a vállalat globálisan elosztott felhasználókkal, ágakkal, adatközpontokkal, virtuális hálózatok és alkalmazásokkal kapcsolódjon egymáshoz az árutovábbítási központban. Az átviteli központ globális árutovábbítási rendszerrel működik.

![forgalmi útvonalak](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**3. ábra: Virtuális WAN-forgalom elérési útjai @ no__t-0

Az Azure Virtual WAN a következő globális átviteli csatlakozási útvonalakat támogatja. A zárójelben lévő betűk a 3. ábrán láthatók.

* Ág – VNet (a)  
* Ág – ág (b)
* Távoli felhasználó – VNet (c)
* Távoli felhasználó – ág (d)
* VNet – VNet az VNet-társítás használatával (e)
* Az ExpressRoute Global Reach 

### <a name="branchvnet"></a>Ág – VNet

A VNet az Azure Virtual WAN által támogatott elsődleges elérési út. Ez az elérési út lehetővé teszi, hogy az Azure virtuális hálózatok üzembe helyezett Azure IAAS Enterprise-munkaterhelésekhez csatlakozhasson ágakat. Az ágak a ExpressRoute vagy a helyek közötti VPN használatával csatlakoztathatók a virtuális WAN-hoz. A VNet-kapcsolatokon keresztül a virtuális WAN-központokhoz csatlakozó virtuális hálózatok áthaladó forgalom. A virtuális WAN esetében nem szükséges az [átjáró átvitele](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) , mert a virtuális WAN automatikusan engedélyezi az átjárók számára az ágakba való átvitelt.

### <a name="branchbranch"></a>Ág – ág

Az ágak ExpressRoute áramkörök és/vagy helyek közötti VPN-kapcsolatok használatával csatlakoztathatók egy Azure-beli virtuális WAN-hubhoz. Az ágakat csatlakoztathatja a virtuális WAN-hubhoz, amely az ág legközelebb eső régiójában található.

Ez a beállítás lehetővé teszi, hogy a vállalatok az Azure gerincet használják az ágak összekapcsolásához. Bár ez a funkció elérhető, érdemes mérlegelni a fiókirodák Azure-beli virtuális WAN-kapcsolaton keresztül történő csatlakoztatásának előnyeit, valamint a privát WAN használatát.

### <a name="usertovnet"></a>Távoli felhasználó – VNet

Engedélyezheti a közvetlen, biztonságos távoli hozzáférést az Azure-hoz pont – hely kapcsolatok használatával egy távoli felhasználói ügyfélről egy virtuális WAN-ra. A vállalati távoli felhasználóknak már nem kell hajtű a felhőbe a vállalati VPN használatával.

### <a name="usertobranch"></a>Távoli felhasználó – ág

A távoli felhasználó – ág elérési út lehetővé teszi, hogy a távoli felhasználók, akik pont – hely kapcsolattal csatlakoznak az Azure-hoz a helyszíni számítási feladatokhoz és alkalmazásokhoz a felhőn keresztül történő átvitelsel. Ez az elérési út biztosítja a távoli felhasználók számára a rugalmasságot az Azure-ban és a helyszínen üzembe helyezett munkaterhelések eléréséhez. A vállalatok engedélyezhetik a központi felhőalapú biztonságos távelérés szolgáltatást az Azure Virtual WAN-ban.

### <a name="vnetvnet"></a>VNet-VNet átvitel a VNet-társítással

A virtuális hálózatok egymáshoz való összekapcsolásához a több virtuális hálózatok megvalósított többrétegű alkalmazások támogatásához használja a VNet-társítást. Az Azure Virtual WAN-on keresztüli VNet-VNet továbbítási forgatókönyv jelenleg nem támogatott, de az Azure-útitervben van. A virtuális hálózatok VNet-közvetítésen keresztüli csatlakoztatása a virtuális hálózatok ajánlott megoldása, amelynek csatlakoznia kell egymáshoz. 

### <a name="globalreach"></a>ExpressRoute Global Reach

A ExpressRoute egy privát és rugalmas módszer a helyi hálózatok Microsoft Cloudhoz való összekapcsolására. A ExpressRoute Global Reach a ExpressRoute kiegészítő szolgáltatása. A Global Reach segítségével összekapcsolhatja a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között. Azok az ágak, amelyek az Azure Virtual WAN-hoz csatlakoznak a ExpressRoute használatával, az ExpressRoute Global Reach kell kommunikálni egymással.

Ebben a modellben minden olyan ág, amely a ExpressRoute használatával csatlakozik a virtuális WAN-hubhoz, a virtuális hálózatok a VNet útvonalon keresztül csatlakozhat. Az elágazási forgalom nem kerül át a központba, mert az ExpressRoute Global Reach az Azure WAN-on keresztüli optimális elérési utat tesz lehetővé.

## <a name="security"></a>Biztonság és házirend-vezérlés

A virtuális hálózati hub csatlakozik egymáshoz, és potenciálisan látja az összes átviteli forgalmat. A központi hálózatkezelési funkciók és szolgáltatások, például a Felhőbeli útválasztás, a hálózati házirend és a biztonság, valamint az Internet-hozzáférés-vezérlés üzemeltetése is lehetséges.

## <a name="next-steps"></a>További lépések

Kapcsolat létrehozása virtuális WAN használatával.

* [Helyek közötti kapcsolatok virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-kapcsolatok virtuális WAN használatával](virtual-wan-expressroute-portal.md)
