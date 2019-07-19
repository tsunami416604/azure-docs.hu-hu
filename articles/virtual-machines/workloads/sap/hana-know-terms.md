---
title: A SAP HANA feltételeinek ismerete az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Ismerje meg az Azure-beli SAP HANA használati feltételeit (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2564e0d34383f7c1daff5b02a871778fb90546cc
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868996"
---
# <a name="know-the-terms"></a>A feltételek megismerése

Számos gyakori definíciót széles körben használnak az architektúra és a technikai üzembe helyezési útmutatóban. Vegye figyelembe a következő kifejezéseket és azok jelentéseit:

- **IaaS**: Infrastruktúra-szolgáltatás.
- **Péter**: Szolgáltatásként szolgáló platform.
- **SaaS**: Szoftver szolgáltatásként.
- **SAP-összetevő**: Egy egyéni SAP-alkalmazás, például az ERP központi összetevő (ECC), az üzleti raktár (BW), a Solution Manager vagy a Enterprise Portal (EP). Az SAP-összetevők a hagyományos ABAP vagy Java-technológiákon, vagy egy nem NetWeaver-alapú alkalmazáson, például üzleti objektumokon alapulnak.
- **SAP-környezet**: Egy vagy több olyan SAP-összetevő, amely logikailag csoportosítva üzleti funkciók végrehajtására, például fejlesztésre, minőségbiztosításra, képzésre, vész-helyreállításra vagy éles üzemre.
- **SAP-környezet**: A teljes SAP-eszközre utal az informatikai környezetben. Az SAP-környezet az összes éles és nem éles környezetet magában foglalja.
- **SAP-System**: Az adatbázis-kezelő réteg és az alkalmazás rétegének kombinációja, például egy SAP ERP fejlesztői rendszer, egy SAP BW tesztelési rendszer és egy SAP CRM éles rendszer. Az Azure-beli üzemelő példányok nem támogatják a két réteg a helyszíni és az Azure közötti felosztását. Az SAP rendszer üzembe helyezése a helyszínen történik, vagy az Azure-ban van üzembe helyezve. Az SAP-környezetek különböző rendszereit üzembe helyezheti az Azure-ban vagy a helyszínen egyaránt. Telepítheti például az SAP CRM fejlesztési és tesztelési rendszereit az Azure-ban, miközben üzembe helyezi az SAP CRM üzemi rendszerét a helyszínen. Az Azure-beli SAP HANA (nagyméretű példányok) esetében a rendszer azt a célt szolgálja, hogy a virtuális gépek SAP-alkalmazási rétegét, illetve az Azure-beli (nagyméretű példányok) bélyegző SAP HANA egy egységéhez tartozó SAP HANA-példányt üzemelteti.
- **Nagyméretű példány bélyegzője**: Egy hardveres infrastruktúra-verem, amely SAP HANA TDI-tanúsítvánnyal rendelkezik, és az Azure-on belüli SAP HANA példányok futtatására van kijelölve.
- **SAP HANA az Azure-ban (nagyméretű példányok):** Az ajánlat hivatalos neve az Azure-ban, hogy HANA-példányokat futtasson SAP HANA TDI-tanúsítvánnyal rendelkező hardvereken, amelyeket nagyméretű példányokban helyeztek üzembe különböző Azure-régiókban. A *HANA nagyméretű példány* kapcsolódó kifejezése rövid SAP HANA az Azure-ban *(nagyméretű példányok)* , és széles körben használatos ebben a technikai üzembe helyezési útmutatóban.
- **Létesítmények közötti**: Leírja azt a forgatókönyvet, amelyben a virtuális gépeket olyan Azure-előfizetéshez telepítik, amely helyek közötti, többhelyes vagy Azure ExpressRoute kapcsolatot tartalmaz a helyszíni adatközpontok és az Azure között. A közös Azure-dokumentációban az ilyen típusú központi telepítések a létesítmények közötti forgatókönyvekben is szerepelnek. A kapcsolódás oka a helyszíni tartományok, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS kiterjesztése az Azure-ba. A helyszíni környezet az Azure-előfizetések Azure-eszközeire van kiterjesztve. Ezzel a bővítménnyel a virtuális gépek a helyszíni tartomány részei lehetnek. 

   A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és futtathatnak szolgáltatásokat ezeken a virtuális gépeken (például az adatbázis-kezelő szolgáltatásokban). A helyszíni és az Azure által üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv a legtöbb SAP-eszköz üzembe helyezésének módjára jellemző. További információ: [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és [virtuális hálózat létrehozása helyek közötti kapcsolattal a Azure Portal használatával](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Bérlő**: A HANA nagyméretű példány bélyegén üzembe helyezett ügyfelek elkülönítettek a *bérlőhöz.* A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a HANA nagyméretű példány-bélyegző szintjén. Egy ügyfél dönthet úgy is, hogy a központi telepítések különböző bérlők között vannak. A HANA nagyméretű példány Stamp szintjén azonban nincs kommunikáció a bérlők között.
- **SKU-kategória**: A HANA nagyméretű példány esetében a következő két SKU-kategóriát ajánljuk:
    - **I. típusú osztály**: S72, S72m, S96, S144, S144m, S192, S192m és S192xm
    - **Type II osztály**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, and S960m
- **Bélyegző**: A nagyméretű HANA-példányok Microsoft belső üzembe helyezési méretének meghatározása. A HANA nagyméretű példányok üzembe helyezése előtt olyan HANA nagyméretű példány bélyegzőt kell üzembe helyezni, amely a számítási, hálózati és tárolási állványokból áll. Az ilyen üzembe helyezést HANA nagyméretű példány bélyegzőnek vagy a 4. változatnak (lásd alább) a **nagyméretű példányok** időszakának helyettesítőjét használjuk.
- **Változat**: A HANA nagy példányszámú bélyegek esetében két különböző Stamp-változat létezik. Ezek különböznek az Azure-beli virtuálisgép-gazdagépek architektúrájában és közelségében
    - "3. változat" (Rev 3): az eredeti kialakítás, amely a 2016-as év közepétől lett üzembe helyezve.
    - "4. változat" (Rev 4): egy olyan új kialakítás, amely az Azure-beli virtuális gépek gazdagépei számára nagyobb közelséget biztosít, valamint az Azure-beli és a HANA nagyméretű példány-egységek közötti alacsonyabb hálózati késést. 

A felhőben számos további erőforrás érhető el a SAP-munkaterhelések üzembe helyezéséhez. Ha SAP HANA Azure-beli üzembe helyezését tervezi, akkor az Azure IaaS alapelveivel és az Azure IaaS SAP-számítási feladatainak üzembe helyezésével foglalkoznia kell. A folytatás előtt tekintse [meg az SAP-megoldások használata az Azure Virtual Machines](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) szolgáltatásban című témakört. 

**Következő lépések**
- [HLI-minősítés](hana-certification.md) megtekintése