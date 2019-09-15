---
title: Az Azure számítástechnikai architektúrájának védelme
description: A vállalati szintű DMZ-architektúrához tartozó viszonyítási architektúra hálózati virtuális berendezéseket és más eszközöket használ. Ez az architektúra úgy lett kialakítva, hogy megfeleljen a védelmi részlegnek a biztonságos felhő-számítástechnikai architektúra működési követelményeinek. Bármely szervezet számára is használható. Ez a hivatkozás két, Citrix vagy F5 rendszerű készüléket használó automatizált beállítást tartalmaz.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: a12f5643c96b855d07bd038fcc96100a87f1252d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001876"
---
# <a name="secure-azure-computing-architecture"></a>Az Azure számítástechnikai architektúrájának védelme

USA A védelmi minisztérium (DoD) azon ügyfelei, akik munkaterheléseket telepítenek az Azure-ba, útmutatást kértek a biztonságos virtuális hálózatok beállításához és a DoD szabványok és gyakorlat által meghatározott biztonsági eszközök és szolgáltatások konfigurálásához. 

A védelmi információs rendszer Ügynöksége (DISA) 2017-ben közzétette a [biztonságos Cloud Computing Architecture (SCCA) funkcionális követelményekkel foglalkozó dokumentumot (FRD)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/SCCA_FRD_v2-9.pdf) . A SCCA leírja a védelmi információk rendszerhálózata (DISN) és a kereskedelmi felhőalapú szolgáltatói kapcsolatok biztonságossá tételének funkcionális célkitűzéseit. A SCCA azt is leírja, hogyan biztosítják a feladatok tulajdonosai a felhőalapú alkalmazásokat a kapcsolatok határain. Minden, a kereskedelmi felhőhöz kapcsolódó DoD-entitásnak követnie kell a SCCA-FRD meghatározott irányelveket.
 
A SCCA négy összetevőből áll:
 
- Határos Felhőbeli hozzáférési pont (BCAP)
- Virtuális adatközpont biztonsági verem (VDSS)
- Virtuális adatközpont által felügyelt szolgáltatás (VDM)
- Megbízható Felhőbeli Hitelesítőadat-kezelő (TCCM) 

A Microsoft kifejlesztett egy megoldást, amely megfelel az Azure-ban futó IL4-és IL5-számítási feladatok SCCA követelményeinek. Ezt az Azure-specifikus megoldást biztonságos Azure számítástechnikai architektúrának (Enikő) nevezzük. A Enikő-t telepítő ügyfelek megfelelnek a SCCA-FRD. Lehetővé tehetik a DoD-ügyfelek számára a munkaterhelések áthelyezését az Azure-ba, miután csatlakoztak.

A SCCA útmutatása és architektúrái a DoD-ügyfelekre vonatkoznak, de a Enikő legújabb változatainak segítségével a civil ügyfelek betartják a megbízható internetkapcsolat (TIC) útmutatását. A legújabb változatok azt is segítik a kereskedelmi ügyfeleket, hogy az Azure-környezetek védelme érdekében biztonságos DMZ-t szeretnének megvalósítani.


## <a name="secure-cloud-computing-architecture-components"></a>Biztonságos felhő-számítástechnikai architektúra összetevői

### <a name="bcap"></a>BCAP

A BCAP célja, hogy megvédje a DISN a felhőalapú környezetből származó támadásokkal szemben. A BCAP a behatolás észlelését és megelőzését végzi. kiszűri a jogosulatlan forgalmat is. Ez az összetevő a SCCA egyéb összetevőivel együtt is elhelyezhető. Javasoljuk, hogy a fizikai hardver használatával telepítse ezt az összetevőt. A BCAP biztonsági követelményei a következő táblázatban láthatók.

#### <a name="bcap-security-requirements"></a>BCAP biztonsági követelmények

![BCAP-követelmények mátrixa](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

A VDSS célja, hogy megvédje az Azure-ban üzemeltetett DoD-tulajdonosi alkalmazásokat. A VDSS a SCCA biztonsági műveleteinek nagy részét hajtja végre. A forgalom vizsgálatával gondoskodik az Azure-ban futó alkalmazások védelméről. Ez az összetevő az Azure-környezetben is elérhető.

#### <a name="vdss-security-requirements"></a>VDSS biztonsági követelmények

![VDSS-követelmények mátrixa](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

A VDM célja a gazdagép-biztonság és a megosztott adatközpont-szolgáltatások biztosítása. A VDM funkciói futhatnak a SCCA központjában, vagy a feladat tulajdonosa a saját Azure-előfizetésében is üzembe helyezheti azt. Ez az összetevő az Azure-környezetben is elérhető.

#### <a name="vdms-security-requirements"></a>VDM biztonsági követelmények

![VDM-követelmények mátrixa](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

A TCCM egy üzleti szerepkör. Ez a személy felelős a SCCA kezeléséért. Feladatai a következők: 

- Terveket és szabályzatokat hozhat létre a felhőalapú környezethez való hozzáféréshez. 
- Győződjön meg arról, hogy az identitás-és hozzáférés-kezelés megfelelően működik. 
- A Felhőbeli hitelesítőadat-kezelési terv fenntartása. 

Ezt a személyt az engedélyező tisztviselő nevezi ki. A BCAP, a VDSS és a VDM biztosítja azokat a képességeket, amelyeket a TCCM el kell végeznie a feladatuk végrehajtásához.

#### <a name="tccm-security-requirements"></a>TCCM biztonsági követelmények

![TCCM-követelmények mátrixa](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>Enikő-összetevők és tervezési szempontok 

A Enikő-referenciák architektúrája a VDSS-és VDM-összetevők üzembe helyezésére szolgál az Azure-ban, és lehetővé teszi a TCCM. Ez az architektúra moduláris. A VDSS és a VDM összes darabja központosított hubhoz is képes. Néhány vezérlő a feladat-tulajdonosi térben vagy akár a helyszínen is teljesíthető. A Microsoft azt javasolja, hogy a VDSS és a VDM összetevőket egy központi virtuális hálózatba helyezze, amelyet az összes feladat tulajdonosa tud a kapcsolaton keresztül. Az alábbi ábrán az architektúra látható: 


![Enikő hivatkozási architektúra diagramja](media/sacav2generic.png)

Amikor megtervezi a SCCA kompatibilitás-stratégiáját és műszaki architektúráját, vegye figyelembe a következő témákat az elejétől, mivel azok minden ügyfelet érintenek. A következő problémák léptek fel a DoD-ügyfelekkel, és általában lelassítják a tervezést és a végrehajtást. 

#### <a name="which-bcap-will-your-organization-use"></a>Melyik BCAP fogja használni a szervezete?
   - DISA BCAP:
        - A DISA két operatív BCAPs rendelkezik a Pentagonban és a Camp Roberts-ben, a CA-ban. A harmadik célja, hogy hamarosan online állapotba kerüljön. 
        - A DISA BCAPs az Azure-ba, amelyet a DoD-ügyfelek is használhatnak a kapcsolódáshoz. 
        - A DISA vállalati szintű Microsoft-partneri munkamenettel rendelkezik a DoD-ügyfelek számára, akik a Microsoft szoftveres (SaaS) eszközökre, például az Office 365-re kívánnak előfizetni. A DISA-BCAP használatával engedélyezheti a kapcsolódást és a Enikő-példányhoz való csatlakozást. 
    - Saját BCAP létrehozása:
        - Ehhez a lehetőséghez egy közös elhelyezésű adatközpontban kell kibérelni a helyet, és be kell állítania egy ExpressRoute áramkört az Azure-ba. 
        - Ehhez a beállításhoz további jóváhagyásra van szükség. 
        - A további jóváhagyás és a fizikai kiépítés miatt ez a lehetőség a legtöbb időt veszi igénybe. 
    - Javasoljuk, hogy használja a DISA BCAP. Ez a lehetőség könnyen elérhető, beépített redundanciával rendelkezik, és olyan ügyfelekkel rendelkezik, amelyek ma már üzem közben működnek.
- DoD irányítható IP-terület:
    - A DoD irányítható IP-helyet kell használnia a peremén. Az Azure-ban elérhető hálózati címfordítási lehetőséggel a szóközöket magánhálózati IP-területhez lehet kapcsolni.
    - Az IP-tárterület beszerzéséhez forduljon a DoD Network Information Center (NIC) szolgáltatáshoz. Szüksége lesz rá a System/Network jóváhagyási folyamat (SNAP) beküldésének részeként a DISA használatával. 
    - Ha azt tervezi, hogy NAT-kapcsolatot használ a magánhálózati címtartomány az Azure-ban való összekapcsolásához, akkor a hálózati adapter minden olyan régiójához, ahol a Enikő üzembe helyezését tervezi, legalább egy/24 alhálózatot kell rendelnie.
- Redundancia:
    - Helyezzen üzembe egy Enikő-példányt legalább két régióban. A DoD-felhőben telepítheti azt mindkét elérhető DoD-régióban.
    - Kapcsolódjon legalább két BCAPs külön ExpressRoute-áramkörök használatával. Ezután mindkét ExpressRoute-kapcsolat társítható az egyes régiók Enikő-példányaihoz. 
- A DoD összetevőre vonatkozó követelmények:
    - A szervezete rendelkezik-e a SCCA-követelményeken kívüli konkrét követelményekkel? Egyes szervezetek konkrét IP-címekre vonatkozó követelményekkel rendelkeznek.
- A Enikő egy moduláris architektúra:
    - Csak a környezetéhez szükséges összetevőket használja. 
        - Hálózati virtuális berendezések üzembe helyezése egy vagy több rétegben.
        - Használjon integrált IP-címeket vagy saját IP-címeket.
- Az alkalmazások és az adataik DoD Impact szintje:
    - Ha bármilyen lehetőség van a Microsoft IL5-régiókban futó alkalmazások számára, hozza létre a Enikő-példányt a IL5-ben. A példány használható a IL4-alkalmazások és IL5 előtt. Egy IL5-alkalmazás előtt egy IL4 Enikő-példány valószínűleg nem kap akkreditációt.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Melyik hálózati virtuális készülék gyártóját fogja használni a VDSS?
Ahogy azt korábban említettük, a Enikő-referenciát számos készülék és Azure-szolgáltatás segítségével hozhatja létre. A Microsoft automatizált megoldási sablonokkal rendelkezik az Enikő architektúra az F5 és a Citrix használatával történő üzembe helyezéséhez. Ezeket a megoldásokat a következő szakasz tartalmazza.

#### <a name="which-azure-services-will-you-use"></a>Mely Azure-szolgáltatásokat fogja használni?
- Vannak olyan Azure-szolgáltatások, amelyek képesek megfelelni a log Analytics, a gazdagép-alapú védelem és az AZONOSÍTÓk funkcióinak. Lehetséges, hogy egyes szolgáltatások nem érhetők el általánosan a Microsoft IL5 régióiban. Ebben az esetben előfordulhat, hogy külső gyártótól származó eszközöket kell használnia, ha ezek az Azure-szolgáltatások nem felelnek meg a követelményeknek. Tekintse meg a kényelmes eszközöket, valamint az Azure natív eszközök használatának megvalósíthatóságát.
- Javasoljuk, hogy a lehető legtöbb Azure natív eszközt használja. A Felhőbeli biztonsággal készültek, és zökkenőmentesen integrálhatók az Azure-platform többi részébe. Az alábbi listában szereplő Azure Native Tools segítségével teljesítheti a SCCA vonatkozó különböző követelményeket:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Azure Firewall](https://docs.microsoft.com/azure/firewall/overview) 
    - [Azure bejárati ajtó](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Azure biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Méretezés
    - A méretezési gyakorlatot végre kell hajtani. Tekintse meg az egyidejű kapcsolatok számát a Enikő-példányon, valamint a hálózati átviteli sebességre vonatkozó követelményeket. 
    - Ez a lépés kritikus fontosságú. Ez segít a virtuális gépek méretének méretezésében és a Enikő-példányban használt különböző gyártók által igényelt licencek azonosításában. 
    - Ez a méretezési gyakorlat nélkül nem végezhető el jó ár elemzése. A megfelelő méretezés a legjobb teljesítmény érdekében is lehetővé teszi. 


## <a name="most-common-deployment-scenario"></a>Leggyakoribb telepítési forgatókönyv

 Számos Microsoft-ügyfél ment keresztül a teljes üzembe helyezésen, vagy a Enikő-környezetek tervezési szakaszain. Tapasztalataink betekintést nyertek a leggyakoribb üzembe helyezési forgatókönyvbe. Az alábbi ábrán a leggyakoribb architektúra látható: 


![Enikő hivatkozási architektúra diagramja](media/sacav2commonscenario.png) 


Ahogy az ábrán látható, a DoD-ügyfelek általában két DISA-BCAPs előfizethetnek. Ezek egyike a nyugati parton, a másik pedig a keleti parton él. Az ExpressRoute-alapú privát társak minden egyes DISA BCAP-helyen engedélyezve vannak az Azure-ban. Ezek a ExpressRoute-partnerek a DoD East és a DoD Central Azure-régióban található virtuális hálózati átjáróhoz kapcsolódnak. A Enikő-példány a DoD East és a DoD Central Azure-régiókban van üzembe helyezve. Az összes bejövő és kimenő forgalom átáramlik a ExpressRoute és a DISA BCAP közötti kapcsolatban.

A tulajdonosi alkalmazások közül válassza ki azokat az Azure-régiókat, amelyekben telepíteni szeretnék az alkalmazásaikat. A virtuális hálózatok közötti kapcsolatot használják az alkalmazás virtuális hálózatának a Enikő virtuális hálózathoz való összekapcsolásához. Ezután a VDSS-példányon keresztül kényszerítik az alagutat az összes forgalomra.

Ezt az architektúrát javasoljuk, mert megfelel a SCCA követelményeinek. Kiválóan elérhető és könnyen méretezhető. Emellett leegyszerűsíti az üzembe helyezést és a felügyeletet.

## <a name="automated-saca-deployment-options"></a>Automatikus Enikő üzembe helyezési lehetőségei

 Ahogy korábban említettük, a Microsoft két szállítóval együttműködve létrehoz egy automatizált Enikő-infrastruktúra-sablont. Mindkét sablon a következő Azure-összetevőket telepíti: 

- Enikő virtuális hálózat
    - Felügyeleti alhálózat
        - Ebben az alhálózatban a felügyeleti virtuális gépek és szolgáltatások üzembe helyezése történik, más néven Jump Box-ként.
        - VDM alhálózat
            - Ez az alhálózat a VDM használt virtuális gépek és szolgáltatások üzembe helyezése.
        - Nem megbízható és megbízható alhálózatok
            - Ezek az alhálózatok a virtuális berendezések üzembe helyezésének helyét jelentik.
        - Átjáróalhálózat
            - Ebben az alhálózatban van a ExpressRoute-átjáró üzembe helyezése.
- Felügyeleti Jump Box – virtuális gépek
    - A környezet sávon kívüli felügyeletéhez használatosak.
- Hálózati virtuális berendezések
    - Az üzembe helyezett sablon alapján a Citrix vagy az F5 függvényt használja.
- Nyilvános IP-címek
    - Ezeket a rendszer a ExpressRoute online állapotba való bekerülés előtt használja. Ezek az IP-címek a háttérbeli Azure-beli magánhálózati címtartomány felé fordíthatók le.
- Útvonaltáblák 
    - Az automatizálás során alkalmazott útválasztási táblázatok az összes forgalmat a virtuális készüléken keresztül kényszerítik.
- Azure Load Balancer – szabványos SKU
    - A rendszer a készülékek közötti forgalom elosztására szolgál.
- Network security groups (Hálózati biztonsági csoportok)
    - Ezek segítségével szabályozható, hogy milyen típusú forgalom haladhat át bizonyos végpontokon.


### <a name="citrix-saca-deployment"></a>Citrix Enikő üzembe helyezése

A Citrix telepítési sablon két réteget telepít a rendelkezésre álló, a Citrix ADC-eszközökhöz. Ez az architektúra megfelel a VDSS követelményeinek. 

![Citrix Enikő diagram](media/citrixsaca.png)


A Citrix dokumentáció és üzembe helyezési parancsfájl esetében tekintse meg [ezt a GitHub-hivatkozást](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>F5 Enikő üzembe helyezése

Két különálló F5 telepítési sablon két különböző architektúrára kiterjed. Az első sablonban csak egy réteg F5 készülék található aktív-aktív, magasan elérhető konfigurációban. Ez az architektúra megfelel a VDSS követelményeinek. A második sablon egy második réteget ad az aktív-aktív, magasan elérhető F5s. Ez a második réteg lehetővé teszi, hogy az ügyfelek a saját IP-címeit az F5-rétegek között az F5-től eltérő módon adja hozzá. A DoD-összetevők nem rendelkeznek a használatra előírt konkrét IP-címekkel. Ha ez a helyzet, az F5 rendszerű készülékek egyetlen rétege a legtöbb esetben működik, mert az architektúra az F5-eszközök IP-címeit is tartalmazza.

![F5 Enikő diagram](media/f5saca.png)

Az F5 dokumentáció és üzembe helyezési parancsfájl esetében tekintse meg [ezt a GitHub-hivatkozást](https://github.com/f5devcentral/f5-azure-saca).












