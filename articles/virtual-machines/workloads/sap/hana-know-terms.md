---
title: Ismerje meg az SAP HANA feltételeit az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: Ismerje meg az SAP HANA feltételeit az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6785ae821f701121185f0064c6317c69d50191ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617052"
---
# <a name="know-the-terms"></a>A feltételek megismerése

Az architektúra és a műszaki telepítési útmutató ban számos gyakori definíció t használnak. Jegyezze fel a következő kifejezéseket és azok jelentését:

- **IaaS**: Infrastruktúra mint szolgáltatás.
- **PaaS**: Platform szolgáltatásként.
- **SaaS**: Szoftver, mint szolgáltatás.
- **SAP-összetevő:** Egyedi SAP-alkalmazás, például ERP központi összetevő (ECC), Üzleti raktár (BW), Solution Manager vagy Enterprise Portal (EP). Az SAP-összetevők hagyományos ABAP vagy Java technológiákon vagy nem NetWeaver alapú alkalmazásokon, például üzleti objektumokon alapulhatnak.
- **SAP-környezet:** Egy vagy több SAP-összetevő logikailag csoportosítva egy üzleti funkció, például a fejlesztés, a minőségbiztosítás, a képzés, a vész-helyreállítási vagy éles környezet.
- **SAP fekvő:** Az informatikai környezetben lévő teljes SAP-eszközökre utal. Az SAP-környezet tartalmazza az összes éles és nem éles környezetben.
- **SAP-rendszer:** A DBMS-réteg és az alkalmazásréteg kombinációja, például egy SAP ERP fejlesztési rendszer, egy SAP BW tesztrendszer és egy SAP CRM termelési rendszer kombinációja. Az Azure-telepítések nem támogatják a két réteg felosztását a helyszíni és az Azure között. Az SAP-rendszer vagy a helyszínen van telepítve, vagy az Azure-ban van telepítve. Az SAP-környezet különböző rendszereit az Azure-ba vagy a helyszíni üzembe helyezheti. Például telepítheti az SAP CRM fejlesztési és tesztelési rendszerek az Azure-ban, miközben az SAP CRM éles rendszer a helyszínen. Az Sap HANA az Azure-ban (nagy példányok) célja, hogy az SAP-rendszerek SAP-alkalmazásréteget a virtuális gépekben és a kapcsolódó SAP HANA-példány egy egységen az Azure-beli (nagy példányok) bélyegzőn az SAP HANA-ban.
- **Nagy példánybélyegző:** Sap HANA TDI-tanúsítvánnyal rendelkező és az Azure-beli SAP HANA-példányok futtatására dedikált hardverinfrastruktúra-verem.
- **SAP HANA az Azure-ban (nagy példányok):** Az azure-beli ajánlat hivatalos neve hana-példányok futtatásához az SAP HANA TDI-tanúsítvánnyal rendelkező hardveren, amely a nagypéldány-bélyegzőkben van telepítve a különböző Azure-régiókban. A kapcsolódó kifejezés *HANA nagy példány* rövid *az SAP HANA az Azure-ban (nagy példányok)* és széles körben használják ebben a technikai telepítési útmutatóban.
- **Telephelyek közötti:** Egy olyan forgatókönyvet ismertet, amelyben a virtuális gépek olyan Azure-előfizetésre vannak telepítve, amely helyek ről helyekre, több helyre vagy Azure ExpressRoute-kapcsolattal rendelkezik a helyszíni adatközpontok és az Azure között. A közös Azure-dokumentációban az ilyen típusú központi telepítések is le, mint a telephelyek közötti forgatókönyvek. A kapcsolat oka a helyszíni tartományok, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS Azure-ra való kiterjesztése. A helyszíni környezet az Azure-előfizetések Azure-eszközeire is kiterjed. Ezzel a bővítőhevel a virtuális gépek a helyszíni tartomány részét lehet tenni. 

   A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és futtathatnak szolgáltatásokat ezeken a virtuális gépeken (például a DBMS-szolgáltatásokon). A helyszínen üzembe helyezett virtuális gépek és az Azure által telepített virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv jellemző a legtöbb SAP-eszközök üzembe helyezésének módjára. További információ: [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) and [Create a virtual network with a site-to-site connection by using the Azure Portal](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Bérlő:** A HANA nagy példány bélyegzőjében üzembe helyezett ügyfél egy *bérlőbe* lesz elkülönítve. A bérlő a hálózatépítés, a tárolás és a számítási réteg más bérlőktől elkülönített. A különböző bérlőkhöz rendelt tárolási és számítási egységek nem láthatják egymást, és nem kommunikálhatnak egymással a HANA nagy példány bélyegzőszintjén. Az ügyfél dönthet úgy, hogy a központi telepítések különböző bérlőkben. Még akkor is, nincs kommunikáció a bérlők között a HANA nagy példány bélyegző szintjén.
- **Termékváltozat-kategória**: A HANA nagy példány esetén a termékváltozatok következő két kategóriája áll rendelkezésre:
    - **I. típusú osztály**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 és S224m
    - **II. típusú osztály**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm és S960m
- **Bélyegző:** A Microsoft belső központi telepítési méretét határozza meg a HANA nagy példányok. Mielőtt hana nagy példány egységek üzembe helyezése előtt, a HANA nagy példány bélyegző, amely ki a számítási, hálózati és tárolási állványok kell telepíteni egy adatközpontban helyen. Az ilyen központi telepítést HANA Large példánybélyegzőnek vagy a 4-es verzióból (lásd alább) nevezzük a **Nagy példánysor** alternatív kifejezésének
- **Verzió**: A HANA nagy példánybélyegzőinek két különböző bélyegző-módosítása van. Ezek architektúrában és az Azure virtuálisgép-üzemeltetés közelségében különböznek
    - "Revision 3" (Rev 3): az eredeti design, hogy van telepítve közepétől az év 2016
    - "4. verzió" (Rev 4): egy új kialakítás, amely közelebb tud biztosítani az Azure virtuálisgép-üzemeltetdéshez, és ezzel az alacsonyabb hálózati késéssel az Azure virtuális gépek és a HANA nagypéldány-egységek között 

Számos további erőforrás érhető el az SAP-számítási feladatok üzembe helyezéséhez a felhőben. Ha azt tervezi, hogy végrehajtja az SAP HANA üzembe helyezését az Azure-ban, tapasztaltnak kell lennie az Azure IaaS alapelveivel és az SAP-számítási feladatok azure-beli IaaS-alapú üzembe helyezésével. Mielőtt folytatna, további információkért tekintse [meg az SAP-megoldások használata az Azure virtuális gépeken](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) című témakört. 

**További lépések**
- Hivatkozzon [a HLI tanúsításra](hana-certification.md)