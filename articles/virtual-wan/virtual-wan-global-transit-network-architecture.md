---
title: 'Architektúra: Globális tranzithálózati architektúra'
titleSuffix: Azure Virtual WAN
description: Tudnivalók a Virtual WAN globális tranzithálózati architektúrájáról
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 9515058bc78a2d56dc1734c046dac5d5b04f68d9
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113167"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globális tranzithálózati architektúra és virtuális WAN

A modern vállalatoknak mindenütt szüksége van a hiperelosztott alkalmazások, adatok és felhasználók közötti kapcsolatra a felhőben és a helyszínen. A globális tranzithálózati architektúrát a vállalatok a felhőközpontú, modern, globális vállalati informatikai lábnyom konszolidálására, összekapcsolására és szabályozására alkalmazzák.

A globális tranzithálózati architektúra egy klasszikus hub-and-spoke kapcsolódási modellen alapul, ahol a felhőben üzemeltetett hálózati "hub" lehetővé teszi a végpontok közötti tranzitív kapcsolatot, amely különböző típusú "küllők" között terjeszthető.

Ebben a modellben a küllő lehet:
* Virtuális hálózat (Virtuális hálózatok)
* Fizikai elágazási hely
* Távoli felhasználó
* Internet

![hub és küllő](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**1. ábra: Globális tranzit-hub-and-spoke hálózat**

1. ábra a globális tranzithálózat logikai nézetét mutatja be, ahol a földrajzilag elosztott felhasználók, a fizikai helyek és a virtuális hálózatok a felhőben üzemeltetett hálózati hubon keresztül kapcsolódnak egymáshoz. Ez az architektúra lehetővé teszi a logikai egyugrásos tranzit kapcsolatot a hálózati végpontok között.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globális tranzithálózat virtuális WAN-nal

Az Azure Virtual WAN a Microsoft által felügyelt felhőhálózati szolgáltatás. A szolgáltatás összes hálózati összetevőjét a Microsoft üzemelteti és kezeli. A Virtual WAN-ról további információt a [Virtual WAN Áttekintés](virtual-wan-about.md) című cikkben talál.

Az Azure Virtual WAN lehetővé teszi a globális tranzithálózati architektúrát azáltal, hogy minden kapcsolatot lehetővé tesz a virtuális hálózatok, a fiókhelyek, az SaaS- és a PaaS-alkalmazások és a felhasználók globálisan elosztott felhőalapú számítási feladatai között.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**2. ábra: Globális tranzithálózat és virtuális WAN**

Az Azure Virtual WAN architektúrában a virtuális WAN-központok azure-régiókban vannak kiépítve, amelyhez az ágak, virtuális hálózatok és távoli felhasználók csatlakoztatása lehet. A fizikai elágazási helyeket a Premium ExpressRoute vagy a webhely-HELY VPN, a virtuális hálózatok virtuális hálózati kapcsolatok, és a távoli felhasználók közvetlenül csatlakozhatnak a hub felhasználói VPN (point-to-site VPN). A Virtual WAN régióközi virtuálishálózat-kapcsolatot is támogat, ahol az egyik régióban lévő virtuális hálózat egy másik régióban lévő virtuális WAN-elosztóhoz csatlakoztatható.

Virtuális WAN-t úgy hozhat létre, hogy egyetlen virtuális WAN-központot hoz létre a régióban, amely a legtöbb küllővel (ágak, virtuális hálózatok, felhasználók) rendelkezik, majd a más régiókban lévő küllők csatlakoztatása a hubhoz. Ez egy jó lehetőség, ha a vállalati lábnyom többnyire egy régióban néhány távoli küllők.  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>Hub-to-hub kapcsolat (előzetes verzió)

A nagyvállalati felhőbeli lábnyom több felhőrégióra is kiterjedhet, és optimális (késés-bölcs) a felhő eléréséhez a fizikai webhelyükhöz és a felhasználókhoz legközelebb eső régióból. A globális tranzithálózati architektúra egyik alapelve, hogy lehetővé tegye a régiók közötti kapcsolatot az összes felhőbeli és helyszíni hálózati végpont között. Ez azt jelenti, hogy az egyik régióban a felhőhöz kapcsolódó ágból érkező forgalom az [Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/)által engedélyezett hub-to-hub kapcsolat használatával elérheti az egyik régióban lévő másik ágat vagy virtuális hálózatot.

![régiók közötti](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**3. ábra: Virtual WAN régióközi kapcsolat**

Ha egyetlen virtuális WAN-ban több hub van engedélyezve, a hubok automatikusan összekapcsolódnak a hubok közötti kapcsolatokon keresztül, így lehetővé teszik a globális kapcsolatot a több régióban elosztott ágak és virtuális hálózatok között. 

Emellett a hubok, amelyek mind ugyanahhoz a virtuális WAN-hoz tartoznak, különböző regionális hozzáférési és biztonsági házirendekhez társíthatók. További információt a cikk [biztonság- és házirend-vezérlése](#security) című témakörben talál.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Bármilyen kapcsolat

A globális tranzithálózati architektúra lehetővé teszi, hogy bármilyen kapcsolat a virtuális WAN hubokon keresztül. Ez az architektúra kiküszöböli vagy csökkenti a küllők közötti teljes háló- vagy részleges hálókapcsolat szükségességét, amelynek kiépítése és karbantartása összetettebb. Ezenkívül a hub-and-spoke és a mesh hálózatok útválasztási vezérlése könnyebben konfigurálható és karbantartható.

Bármely kapcsolat (a globális architektúra környezetében) lehetővé teszi, hogy a globálisan elosztott felhasználókkal, ágakkal, adatközpontokkal, virtuális hálózatokkal és alkalmazásokkal rendelkező vállalatok a "tranzit" hub(ok)on keresztül csatlakozzanak egymáshoz. Az Azure Virtual WAN globális tranzitrendszerként működik.

![bármely bármely](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**4. ábra: Virtuális WAN közlekedési útvonalak**

Az Azure Virtual WAN a következő globális tranzitkapcsolati útvonalakat támogatja. A zárójelben lévő betűk a 4.

* Fiók-vnet (a)
* Ág-ág (b)
  * ExpressRoute globális elérés és virtuális WAN
* Távoli felhasználó-virtuális hálózat (c)
* Távoli felhasználó-ágancika (d)
* Virtuális hálózat és virtuális hálózat (e)
* Ág-hub-hub-to-Branch (f)
* Ág-hub-hub-to-VNet (g)
* Virtuális hálózat-hub-hub-to-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Ág-vnet (a) és a fiók-to-VNet régiók közötti (g)

A Branch-to-VNet az Azure Virtual WAN által támogatott elsődleges elérési út. Ez az elérési út lehetővé teszi, hogy ágakat csatlakoztasson az Azure IAAS vállalati számítási feladatokhoz, amelyek az Azure virtuális hálózatokban vannak telepítve. Az ágak az ExpressRoute vagy a helyek közötti VPN-en keresztül csatlakoztathatók a virtuális WAN-hoz. A forgalom virtuális hálózatokhoz, amelyek virtuális hálózatkapcsolatokon keresztül csatlakoznak a virtuális WAN-csomópontokhoz. Explicit [átjáró átvitele](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) nem szükséges a Virtual WAN, mert a Virtual WAN automatikusan engedélyezi az átjáró átvitelét a telephelyre. Tekintse meg a [Virtual WAN Partners](virtual-wan-configure-automation-providers.md) cikkét az SD-WAN CPE virtual WAN-hoz való csatlakoztatásáról.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute globális elérés és virtuális WAN

Az ExpressRoute egy privát és rugalmas módja annak, hogy a helyszíni hálózatokat a Microsoft Cloud-hoz csatlakoztassa. A Virtual WAN támogatja az Express Route áramköri kapcsolatokat. A telephely virtual WAN-hoz való csatlakoztatásához express route-on 1) Prémium áramkör 2) Áramkör kell a Globális elérés engedélyezett helyen.

Az ExpressRoute Global Reach az ExpressRoute egyik kiegészítő szolgáltatása. A Global Reach segítségével összekapcsolhatja az ExpressRoute-áramköröket, hogy magánhálózatot hozlétre a helyszíni hálózatok között. Az ExpressRoute használatával az Azure Virtual WAN-hoz kapcsolódó ágak megkövetelik, hogy az ExpressRoute globális elérése kommunikáljon egymással.

Ebben a modellben minden ág, amely az ExpressRoute használatával csatlakozik a virtuális WAN-hubhoz, csatlakozhat a virtuális hálózatokhoz az ág-virtuális hálózat elérési útján. Az ág-ággal való forgalom nem halad át a hubon, mert az ExpressRoute Globális elérés optimálisabb elérési utat tesz lehetővé az Azure WAN-on keresztül.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ágközi (b) és ágközi régiók (f)

Az ágak ExpressRoute-áramkörök és/vagy helyek közötti VPN-kapcsolatok használatával csatlakoztathatók egy Azure virtuális WAN-hubhoz. Az ágakat csatlakoztathatja az ághoz legközelebb eső virtuális WAN hubhoz.

Ez a beállítás lehetővé teszi, hogy a vállalatok az Azure gerinchálózatát kihasználva csatlakoztassa az ágakat. Annak ellenére, hogy ez a képesség elérhető, mérlegelnie kell az ágak azure-beli Virtuális WAN-on keresztüli csatlakoztatásának előnyeit, szemben egy privát WAN használatával.  

### <a name="remote-user-to-vnet-c"></a>Távoli felhasználó-virtuális hálózat (c)

Közvetlen, biztonságos távoli hozzáférést engedélyezhet az Azure-hoz a távoli felhasználói ügyfél és a virtuális WAN közötti pont-hely kapcsolat használatával. A vállalati távoli felhasználóknak már nem kell a felhőbe hajtással a felhőbe hajtással vállalati VPN-t használva.

### <a name="remote-user-to-branch-d"></a>Távoli felhasználó-ágancika (d)

A távoli felhasználó-ágelérési út lehetővé teszi a távoli felhasználók, akik egy pont-hely kapcsolat az Azure-hoz hozzáférés helyszíni számítási feladatok és alkalmazások a felhőn keresztül. Ez az elérési út rugalmasságot biztosít a távoli felhasználók számára az Azure-ban és a helyszíni üzembe helyezett számítási feladatok eléréséhez. A vállalatok lehetővé tehetik a központi felhőalapú biztonságos távelérés-szolgáltatást az Azure Virtual WAN-ban.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>Virtuális hálózat és virtuális hálózat közötti átvitel (e) és virtuális hálózat közötti régióközi (h)

A virtuális hálózat-virtuális hálózat közötti átvitel lehetővé teszi, hogy a virtuális hálózatok egymáshoz való csatlakozásérdekében többrétegű alkalmazások, amelyek több virtuális hálózaton keresztül valósítják meg. Opcionálisan virtuális hálózatok at is csatlakoztathat egymáshoz a virtuális hálózat társviszony-létesítés, és ez alkalmas lehet bizonyos forgatókönyvek, ahol a VWAN hubon keresztül történő átvitel nem szükséges.

## <a name="security-and-policy-control"></a><a name="security"></a>Biztonság és házirend-ellenőrzés

Az Azure Virtual WAN-központok összekapcsolják az összes hálózati végpontot a hibrid hálózaton keresztül, és potenciálisan láthatják az összes átviteli hálózati forgalmat. A virtuális WAN-elosztók biztonságos virtuális elosztókká alakíthatók az Azure Tűzfal VWAN-központokon belüli telepítésével a felhőalapú biztonság, a hozzáférés és a házirend-vezérlés engedélyezéséhez. Az Azure firewalls vezénylése a virtuális WAN-központokban az Azure Firewall Manager által végezhető el.

[Az Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) a globális tranzithálózatok biztonságának kezeléséhez és méretezéséhez szükséges lehetőségeket biztosítja. Az Azure Firewall Manager lehetővé teszi az útválasztás, a globális házirend-kezelés, a fejlett internetes biztonsági szolgáltatások központi kezelését harmadik félen keresztül az Azure tűzfallal együtt.

![biztonságos virtuális központ az Azure Firewall tűzfallal](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**5. ábra: Biztonságos virtuális központ az Azure tűzfallal**

Az Azure Firewall a virtuális WAN támogatja a következő globális biztonságos tranzit kapcsolatelérési utak. A zárójelben lévő betűk az 5.

* Virtuális hálózat és virtuális hálózat között biztonságos átvitel (e)
* Virtuális hálózat–internet vagy külső biztonsági szolgáltatás (i)
* Internetkapcsolat vagy külső biztonsági szolgáltatás (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Virtuális hálózat és virtuális hálózat között biztonságos átvitel (e)

A virtuális hálózat-virtuális hálózat biztonságos átvitel lehetővé teszi, hogy a virtuális hálózatok csatlakozni egymáshoz az Azure tűzfalon keresztül a virtuális WAN hub.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>Virtuális hálózat–internet vagy külső biztonsági szolgáltatás (i)

A virtuális hálózat-internet vagy harmadik fél által védett átvitel lehetővé teszi a virtuális hálózatok számára, hogy csatlakozzanak az internethez vagy egy támogatott külső biztonsági szolgáltatásokhoz az Azure tűzfalon keresztül a virtuális WAN hubon.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Internetkapcsolat vagy külső biztonsági szolgáltatás (j)
A fiók-to-Internet vagy külső biztonságos átvitel lehetővé teszi az ágak számára, hogy csatlakozzanak az internethez vagy egy támogatott külső biztonsági szolgáltatások az Azure tűzfalon keresztül a virtuális WAN hub.

## <a name="next-steps"></a>További lépések

Hozzon létre egy kapcsolatot a Virtual WAN használatával, és telepítse az Azure tűzfalat a VWAN hub(ok)on.

* [Helyek közötti kapcsolatok a Virtual WAN használatával](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-kapcsolatok virtuális WAN használatával](virtual-wan-expressroute-portal.md)
* [Az Azure Firewall Manager az Azure FW üzembe helyezéséhez a VWAN-ben](https://go.microsoft.com/fwlink/?linkid=2107683)
