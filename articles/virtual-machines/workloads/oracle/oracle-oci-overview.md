---
title: A Microsoft Azure integrálása az Oracle-felhő-infrastruktúra |} A Microsoft Docs
description: Az adatbázisok Oracle Felhőbeli infrastruktúrát (OCI) a Microsoft Azure-ban futtatott Oracle-alkalmazások integrált megoldások tudnivalók.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 5a60e41d3195c0f7d88fd3ba14336d693d2f528e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446680"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Oracle-alkalmazási megoldások integrálása a Microsoft Azure és az Oracle-felhő-infrastruktúra (előzetes verzió)

A Microsoft és az Oracle szállíthatnak alacsony késleltetésű, nagy átviteli sebességet a felhőbe irányuló kapcsolatot, így mindkét felhőben a legjobb kihasználásához. 

A több felhőre kiterjedő kapcsolat használata esetén particionáló egy többrétegű alkalmazás futtatása az adatbázisszint az Oracle Felhőbeli infrastruktúrát (OCI), és a az alkalmazás és az egyéb szintek, a Microsoft Azure-ban. A felhasználói élményt a teljes megoldás verem egy egyetlen felhőben futó hasonlít. 

> [!IMPORTANT]
> Ez a több felhőre kiterjedő funkció jelenleg előzetes verzióban érhető el, és néhány [korlátozások érvényesek a](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

Ha érdekli az Oracle-megoldások teljes egészében az Azure-infrastruktúra telepítése, lásd: [Oracle Virtuálisgép-rendszerképek és a Microsoft Azure-on üzembe helyezésükben](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A felhőbe irányuló kapcsolat Oracle-szolgáltatás piacvezető alkalmazásokat és saját egyéni alkalmazások futtatását az Azure virtuális gépeken, miközben OCI lévő üzemeltetett adatbázis szolgáltatások előnyeit élvező megoldást kínál. 

Alkalmazások futtatása a több felhőre kiterjedő konfigurációban a következők:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Kiskereskedelmi Oracle-alkalmazásokhoz
* Oracle Hyperion pénzügyi adatok kezelésére

Az alábbi ábrán az a csatlakoztatott megoldás magas szintű áttekintése. Az egyszerűség kedvéért a diagram csak egy alkalmazásrétegbe és a egy adatrétegbeli jeleníti meg. Az alkalmazásarchitektúra függően a megoldás további szinten a webes szint például lehetnek az Azure-ban. További információkért tekintse meg a következő szakaszok.

![Az Azure OCI megoldás áttekintése](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

* Előzetes verzióban érhető el a felhőbe irányuló kapcsolat az Azure keleti régiója (USA keleti régiója) régiójában és a OCI Ashburn (USA-ashburn-1) régióban korlátozódik.

## <a name="networking"></a>Hálózat

Nagyvállalati ügyfeleink gyakran diversify és számítási feladatot helyezhet üzembe több, különböző üzleti és működési okokból felhők felett válassza ki. Diversify, hogy az ügyfelek követelő felhőalapú hálózatokhoz az interneten, IPSec VPN-, vagy pedig a felhőbeli szolgáltató közvetlen kapcsolódás megoldást a helyszíni hálózaton keresztül. Felhőalapú hálózatokhoz biztosítva az időt, költséget takaríthat meg, tervezési, beszerzési, telepítési, tesztelése és operations megkövetelheti a jelentős befektetéseket. 

Ezen ügyfelek fontos kihívásaira, az Oracle és a Microsoft engedélyezte többfelhős integrált megoldást. Felhők közötti kapcsolódás létrejött, csatlakozzon egy [ExpressRoute](../../../expressroute/expressroute-introduction.md) kapcsolatcsoport az Microsoft Azure-ban egy [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) OCI a kapcsolatcsoportot. A kapcsolat akkor lehetséges, egy Azure ExpressRoute-társviszony-létesítési helyszínen közel kerülhet a vagy a OCI FastConnect társviszony-létesítési ugyanazon a helyen. Ez a beállítás lehetővé teszi, hogy egy köztes szolgáltatót nélkül a két felhők közötti biztonságos és gyors kapcsolat.

Használja az ExpressRoute- és FastConnect, ügyfelek társviszonyt OCI, a felhőbeli virtuális hálózattal egy virtuális hálózatot az Azure-ban feltéve, hogy a magánhálózati IP-címtér nem fedik át egymást. A két hálózat közötti társviszony lehetővé teszi, hogy egy erőforrást a virtuális hálózatban a OCI felhőalapú virtuális hálózati erőforrás közli, ha mindkettő ugyanazon a hálózaton.

## <a name="network-security"></a>Hálózati biztonság

Hálózati biztonság felhőtermékekkel minden vállalati alkalmazás, és ez a megoldás több felhő központi. Minden forgalmat az ExpressRoute és FastConnect magánhálózaton keresztül továbbítja. Ez a konfiguráció lehetővé teszi, hogy az Azure-beli virtuális hálózathoz, és az Oracle felhőbeli virtuális hálózat közötti biztonságos kommunikációhoz. Nem kell minden olyan virtuális gépek az Azure-ban egy nyilvános IP-címet adjon meg. Hasonlóképpen nem kell egy OCI az internetes átjárónkhoz. Minden kommunikáció a gépek magánhálózati IP-cím-n keresztül történik.

Emellett beállíthat [biztonsági listáival](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) a OCI felhőalapú virtuális hálózati és biztonsági szabályok (az Azure-hoz csatlakoztatott [hálózati biztonsági csoportok](../../../virtual-network/security-overview.md)). Ezek a szabályok segítségével szabályozhatja a virtuális hálózat közötti forgalom vezérléséhez. Hálózati biztonsági szabályok a gépek szintjén, egy alhálózat szintjén, valamint a virtuális hálózat szintjén adhatók hozzá.
 
## <a name="identity"></a>Identitás

Identitás egyike a Microsoft és az Oracle közti partneri kapcsolatról core területei legyenek elérhetők. Jelentős mennyiségű munkával jár végeztük el integrálásához [Oracle felhőalapú Identitásszolgáltatás](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) rendelkező [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). Az Azure AD a Microsoft felhőalapú identitás- és hozzáférés felügyeleti szolgáltatása. Ez segít a felhasználók bejelentkezniük és elérniük különböző erőforrásokat. Az Azure AD lehetővé teszi a felhasználók és engedélyeik felügyelete.

Jelenleg ez az integráció lehetővé teszi, hogy egyetlen központi helyen, amely az Azure Active Directory kezeléséhez. Azure ad-ben a directory módosításainak szinkronizálása a megfelelő Oracle-könyvtárat, és egyszeri bejelentkezést a több felhőre kiterjedő Oracle-megoldásokról szolgál.

## <a name="next-steps"></a>További lépések

Első lépések a [a felhőbe irányuló hálózati](configure-azure-oci-networking.md) Azure és a OCI között. 

További információkért és OCI kapcsolatos tanulmányok, tekintse meg a [Oracle Felhőbeli](https://docs.cloud.oracle.com/iaas/Content/home.htm) dokumentációját.
