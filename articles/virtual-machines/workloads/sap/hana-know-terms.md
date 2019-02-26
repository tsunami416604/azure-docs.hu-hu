---
title: SAP HANA az Azure-ban (nagyméretű példányok) a használati tudja |} A Microsoft Docs
description: Hogy az SAP HANA az Azure-ban (nagyméretű példányok) feltételeit.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8131bc953c2aba3c8d33f866cbbe9b1e232e168
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819088"
---
# <a name="know-the-terms"></a>A használati tudja

Több közös definíciók széles körben használják az architektúra és üzembe helyezési útmutatót. Vegye figyelembe a következő kifejezéseket és azok jelentését ismerteti:

- **IaaS**: Infrastruktúra-szolgáltatás.
- **PaaS**: Szolgáltatásként nyújtott platformon.
- **SaaS**: Szolgáltatott szoftver.
- **Az SAP-összetevő**: Egyedi SAP alkalmazások, például az ERP központi összetevő (ECC), a Business Warehouse (BW), a megoldás Manager vagy a vállalati portál (EP). Az SAP-összetevők hagyományos ABAP és Java-technológiák vagy egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok alapulhat.
- **Az SAP-környezet**: Egy vagy több SAP összetevő logikusan való alkalmasság vállalati, például fejlesztési, minőségbiztosítási, képzés, vész-helyreállítási vagy éles környezetben.
- **SAP-rendszeren**: Hivatkozik az egész SAP-eszközök az informatikai környezet változásaihoz. Az SAP-rendszeren, tartalmazza az összes éles környezetben, és nem éles környezetekben.
- **SAP-rendszerhez**: Az adatbázis-kezelő réteg és a alkalmazásréteg, például az SAP ERP fejlesztési rendszer együttes használata az SAP BW tesztelése, és az SAP CRM éles rendszer. Azure-környezetek nem támogatják a értékkel való osztásának e két réteget a helyszíni és az Azure között. Egy SAP-rendszerrel, üzembe helyezett helyszíni vagy annak az Azure-ban üzembe helyezett. A különböző rendszerek, az SAP-rendszeren az Azure-ban vagy a helyszíni telepítheti. Például telepítheti az SAP CRM fejlesztési és rendszerek tesztelése az Azure-ban, miközben az SAP CRM rendszert a helyi környezetben telepíti. Az SAP Hana az Azure-ban (nagyméretű példányok) célja, hogy az SAP alkalmazásréteg-alapú virtuális gépeken futó SAP-rendszereit és a egy egységet az SAP HANA az Azure-ban (nagyméretű példányok) stamp a kapcsolódó SAP HANA-példány üzemeltetése.
- **Nagyméretű szolgáltatáspéldányban**: Egy, az SAP HANA TDI-tanúsítvánnyal és futtatásához az Azure-beli SAP HANA-példányok dedikált hardver infrastruktúra stack.
- **Az SAP HANA az Azure-ban (nagyméretű példányok):** Az ajánlat az Azure-ban a HANA-példányok futtathatók az SAP HANA TDI-tanúsítvánnyal rendelkező hardverek nagyméretű példány stampek különböző Azure-régióban üzembe helyezett hivatalos neve. A kapcsolódó kifejezés *nagyméretű HANA-példány* rövid a *SAP HANA az Azure-ban (nagyméretű példányok)* és széles körben használatos a műszaki telepítési útmutatóban.
- **Létesítmények közötti**: Ismerteti egy olyan forgatókönyvet, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes vagy Azure ExpressRoute-kapcsolat a helyszíni adatközpontok és az Azure közötti üzembe. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS az Azure-bA. Az Azure-előfizetések az Azure-objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény a virtuális gépeket a helyszíni tartomány része lehet. 

   Tartományi felhasználókat a helyszíni tartomány elérheti a kiszolgálók és szolgáltatások futtatása a virtuális gépeken (például adatbázis-kezelő szolgáltatások). Virtuális gépek közötti kommunikációt és a névfeloldás üzembe helyezte a helyszínen és az Azure által telepített virtuális gépek lehetséges. Ebben a forgatókönyvben jellemző, amelyben a legtöbb SAP-eszközök vannak üzembe helyezve módja. További információkért lásd: [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [virtuális hálózat létrehozása helyek közötti kapcsolattal az Azure portal használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Bérlő**: A HANA nagyméretű szolgáltatáspéldányban telepített ügyfél lekérdezi izolált be egy *bérlő.* Egy bérlő van különítve a hálózati, tárolási és számítási rétegben, a többi bérlőtől. A különböző bérlők rendelt tárolási és számítási egységek nem tekintse meg egymással, és nem kommunikálnak egymással, nagyméretű HANA-példány stamp szint. Egy ügyfél lehet váltani, más bérlők üzemelő rendelkezik. Ezután még nem folyik kommunikáció bérlők HANA nagyméretű példányok stamp szint között.
- **Termékváltozat kategória**: A nagyméretű HANA-példányt a következő két kategória SKU-k érhetők el:
    - **I. osztály típus**: S72, S72m, S96, S144, S144m, S192, S192m és S192xm
    - **Írja be a II osztály**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, and S960m


Számos további erőforrás az SAP számítási feladatok felhőbeli üzembe helyezése a érhetők el. Ha azt tervezi, hajtsa végre az SAP HANA üzembe helyezése az Azure-ban, szüksége lehet hasznosítani és az Azure IaaS és a központi telepítés az SAP számítási feladatok az Azure IaaS tisztában. A folytatás előtt tekintse meg a [használata SAP-megoldások az Azure-beli virtuális gépek](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) további információt. 

**Következő lépések**
- Tekintse meg [HLI minősítés](hana-certification.md)