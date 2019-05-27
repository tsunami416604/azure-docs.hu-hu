---
title: Az Azure számítástechnikai architektúrájának védelme
description: Ez a referenciaarchitektúra egy vállalati szintű DMZ-architektúra a hálózati virtuális berendezések és egyéb eszközöket használ. Ez az architektúra úgy lett kialakítva, a védelmi Minisztérium a biztonságos felhőalapú számítástechnikai architektúra működési követelmények teljesítéséhez. Azt is használható minden olyan szervezet számára. Ezt a hivatkozást használja a Citrix vagy F5 berendezéseit két automatizált lehetőségeket tartalmazza.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: 017a26d5672f666d4d8eaf629a0f53fe0cfe517f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963229"
---
# <a name="secure-azure-computing-architecture"></a>Az Azure számítástechnikai architektúrájának védelme

USA Védelmi Minisztérium (DoD) azon ügyfelek, akik az Azure-bA a számítási feladatok üzembe helyezéséhez kapcsolatos útmutató a biztonságos virtuális hálózatok és a biztonsági eszközöket és szolgáltatásokat, amelyek a védelmi Minisztérium szabványok és eljárások alapján állapítják beállítása kérte. 

A Defense Information System Ügynökség (DISA) közzé a [biztonságos felhőalapú számítástechnikai architektúra (SCCA) működési követelmények dokumentum (FRD)](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017-ben. SCCA ismerteti a működési célkitűzések a Defense Information System hálózat (DISN) és a kereskedelmi felhőben szolgáltató csatlakozási pontok biztonságossá tételéhez. SCCA azt is leírja, biztonságos alapvető tulajdonosai a felhő-alkalmazásokkal a kapcsolati határt. Minden, amely kapcsolódik a kereskedelmi felhőben DoD entitás a szoftverterméket a SCCA FRD irányelveket kell követnie.
 
A SCCA négy részből áll:
 
- Határ felhőalapú hozzáférési pont (BCAP)
- Virtual Datacenter Security szolgáltatáskészlet (VDSS)
- Virtual Datacenter felügyelt szolgáltatás (VDMS)
- Megbízható felhőalapú hitelesítőadat-kezelő (TCCM) 

A Microsoft kifejlesztett egy megoldás, amely megfelel a SCCA IL4 és IL5 is az Azure-ban futó számítási feladatokat. Az Azure-ra vonatkozó megoldást a biztonságos Azure számítástechnikai architektúra (SACÁBÓL) nevezzük. Az üzembe helyezett SACÁBÓL megfeleljenek a SCCA FRD. Számítási feladatok áthelyezése az Azure-ba, miután csatlakozott, a védelmi Minisztérium ügyfelek lehetővé teszik.

SCCA útmutatást és architektúrákat adott DoD-ügyfelek számára, de a legújabb verziók SACÁBÓL súgó polgári ügyfelek számára megbízható internetes kapcsolaton (TIC) útmutatást ahhoz. A legújabb változata, amely egy biztonságos szegélyhálózat (DMZ) az Azure-környezetek védelmét is érdemes kereskedelmi ügyfelek is segítenek.


## <a name="secure-cloud-computing-architecture-components"></a>Biztonságos felhő-számítástechnikai architektúra összetevői

### <a name="bcap"></a>BCAP

A BCAP célja a DISN elleni támadásokat, amelyek a felhőalapú környezetben származnak. BCAP behatolásérzékelő és -megelőző hajt végre. azt is kiszűri a nem engedélyezett forgalmat. Lehet, hogy ez az összetevő a SCCA a más összetevőkkel közös elhelyezésű. Azt javasoljuk, hogy fizikai hardver segítségével telepítheti ezt az összetevőt. Az alábbi táblázatban felsorolt BCAP biztonsági követelményeknek.

#### <a name="bcap-security-requirements"></a>BCAP biztonsági követelmények

![BCAP követelmények – mátrix](media/bcapreqs.png)


### <a name="vdss"></a>VDSS

A VDSS célja védelmét a védelmi Minisztérium alapvető tulajdonosi alkalmazásokhoz, amelyek az Azure-ban. VDSS a biztonsági műveletek tömeges a SCCA hajt végre. Az Azure-ban futó alkalmazások védelmére adatforgalom vizsgálatának végez. Ez az összetevő belül az Azure-környezet adható meg.

#### <a name="vdss-security-requirements"></a>VDSS biztonsági követelmények

![VDSS követelmények – mátrix](media/vdssreqs.png)

### <a name="vdms"></a>VDMS

VDMS célja, hogy a gazdagép a biztonságot, és a megosztott data center-szolgáltatásokat. VDMS funkcióit futtathatja a hubon, az SCCA, vagy az üzletmenet szempontjából tulajdonosa a saját konkrét Azure-előfizetésében lévő darabok, üzembe helyezhet. Ez az összetevő belül az Azure-környezet adható meg.

#### <a name="vdms-security-requirements"></a>VDMS biztonsági követelmények

![VDMS követelmények – mátrix](media/vdmsreqs.png)


### <a name="tccm"></a>TCCM

TCCM Ez egy üzleti szerepkör. Ez a személy feladata a SCCA kezelése. A feladataik a következők: 

- Csomagok és a felhőalapú környezet fiók elérésére vonatkozó szabályzatokat hoz létre. 
- Győződjön meg arról, hogy identitás és hozzáférés-kezelést megfelelően működik-e. 
- A Felhőbeli hitelesítő adat felügyeleti csomag fenntartásához. 

Ez a személy a engedélyező hivatalos nevezi. A BCAP VDSS és VDMS adja meg az a funkciók köre a TCCM van szüksége a feladat végrehajtásához.

#### <a name="tccm-security-requirements"></a>TCCM biztonsági követelmények

![TCCM követelmények – mátrix](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACÁBÓL összetevők és tervezési szempontok 

Célja, hogy a SACÁBÓL referenciaarchitektúra üzembe helyezése az Azure-ban a VDSS és VDMS összetevők és a TCCM engedélyezése. Ez az architektúra olyan moduláris. Az összes VDSS és VDMS darabjait is élő az információs központ. Egyes vezérlőelemeket az alapvető tulajdonosi helyet vagy akár a helyszíni érheti el. A Microsoft azt javasolja, hogy Ön közös elhelyezése a a VDSS és VDMS összetevőket a központi virtuális hálózatban, amely az összes alapvető tulajdonosok csatlakozhatnak. Az alábbi ábrán látható, ez az architektúra: 


![SACÁBÓL architekturális diagramja](media/sacav2generic.png)

A SCCA előírásainak való megfelelést stratégia és a technikai architektúrára tervezésekor vegye figyelembe a következő témakörök az elejéről, mert azok befolyásolják, hogy minden ügyfél. A következő problémák DoD ügyfelekkel merülnek fel, és általában a tervezési és végrehajtási lelassulnak. 

#### <a name="which-bcap-will-your-organization-use"></a>Melyik BCAP fogja használni a szervezet?
   - DISA BCAP:
        - DISA két működési BCAPs rendelkezik, a ötszög és tábor Roberts, CA. A harmadik tervezett hamarosan online állapotba. 
        - DISA a BCAPs minden Azure-ban, amely kapcsolatot DoD-ügyfelek által használható Azure ExpressRoute-kapcsolatcsoportokat rendelkezik. 
        - DISA rendelkezik egy vállalati szintű Microsoft társviszony-létesítési munkamenetet DoD-ügyfelek, akik a szoftverszolgáltatás (SaaS-) eszközök, például az Office 365, Microsoft-szoftverek előfizetést. A DISA BCAP használatával engedélyezheti a kapcsolat és a társviszony-létesítés a SACÁBÓL példányra. 
    - A saját BCAP buildu:
        - Ez a beállítás megköveteli, hogy egy közös helyen tárolt Center terület címbérlet, és állítsa be az Azure ExpressRoute-kapcsolatcsoport. 
        - Ez a beállítás további jóváhagyást igényel. 
        - A további jóváhagyás és a egy fizikai kiépítése miatt ez a beállítás a legtöbb időt vesz igénybe. 
    - Azt javasoljuk, hogy használja-e a DISA BCAP. Ez a beállítás azonnal elérhető, beépített redundanciát, és ügyfelek számára jelenleg működik rajta éles környezetben.
- Védelmi Minisztérium irányítható IP terület:
    - A peremen DoD irányítható IP-címtér kell használnia. A NAT használatával ezeket a szóközöket csatlakozhat az Azure-ban magánhálózati IP-címtér lehetőség érhető el.
    - Lépjen kapcsolatba a védelmi Minisztérium hálózati információkat Center (NIC) IP-terület beszerzése. A rendszer és a hálózati jóváhagyási folyamat (BEÉPÜLŐ) beküldött DISA-részeként van szükség. 
    - Ha azt tervezi, a NAT használatával csatlakozzon az Azure-beli privát címterét, szüksége van-e legalább egy/24 alhálózati címtartomány hozzárendelt hálózati adapter minden olyan régióhoz, amelyben tervez üzembe helyezni a SACÁBÓL.
- Redundancia:
    - A SACÁBÓL példány üzembe helyezéséhez legalább két régióban is. A felhőben DoD helyezi üzembe azt DoD mindkét régióban.
    - Legalább két BCAPs külön az ExpressRoute-Kapcsolatcsoportok keresztül csatlakozhat. Mindkét ExpressRoute-kapcsolatok majd minden egyes régió SACÁBÓL példány lehet kapcsolódni. 
- Védelmi Minisztérium összetevő-specifikus követelmények:
    - A szervezet rendelkezik a SCCA követelmények kívül különleges követelményeket? Egyes szervezetek adott IP-CÍMEK követelményei.
- SACÁBÓL moduláris architektúra:
    - Csak a környezetében szükséges összetevőket használja. 
        - Egyetlen vagy több rétegből álló virtuális hálózati berendezések üzembe helyezése.
        - Az integrált IP-CÍMEK vagy az IP-CÍMEK bring-your-own.
- Az alkalmazások és adatok védelmi Minisztérium a hatás szintje:
    - Ha nincs Microsoft IL5 régiókban futó alkalmazások lehetőségét, hozhat létre a SACÁBÓL példány IL5. A példány IL4 alkalmazásokat és IL5 is használható. Nagy valószínűséggel IL5 alkalmazás elé IL4 SACÁBÓL példány akkreditáció nem kap.

#### <a name="which-network-virtual-appliance-vendor-will-you-use-for-vdss"></a>Mely hálózati virtuális berendezés gyártójához fog használni a VDSS?
Ahogy korábban említettük, a SACÁBÓL hivatkozást hozhat létre különböző készülékeket és az Azure-szolgáltatások használatával. A Microsoft rendelkezik automatikus megoldássablonok a SACÁBÓL architektúra az F5 billentyűt és a Citrix történő üzembe helyezéséhez. Ezek a megoldások terjed ki a következő szakaszban.

#### <a name="which-azure-services-will-you-use"></a>Mely Azure-szolgáltatásokat használni?
- Nincsenek Azure-szolgáltatások, amelyek a log analytics, a gazdagép-alapú védelmi és Azonosítók funkció követelményeinek is. Egyes szolgáltatások nem általánosan érhetők el a Microsoft IL5 régiókban lehetőség. Ebben az esetben előfordulhat, hogy kell használnia a harmadik felektől származó eszközök Ha az Azure-szolgáltatások nem megfelelnek az elvárásainak. Tekintse meg az eszközök már magabiztosan kezeli a és a megvalósíthatósági natív Azure eszközkészlet használata.
- Azt javasoljuk, hogy a lehető tetszőleges számú Azure natív eszközöket használja. Ezek a felhőbeli biztonság szem vagyunk épül, és a többi, az Azure platform zökkenőmentes integrálásával. Az alábbi listában a natív Azure-eszközök használatával SCCA különböző követelményeinek:

    - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
    - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
    - [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
    - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
    - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
    - [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
    - [Az Azure-tűzfal](https://docs.microsoft.com/azure/firewall/overview) 
    - [Az Azure bejárati ajtajának](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
    - [Az Azure biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview)
    - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
    - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Méretezés
    - Egy méretezési gyakorlatban kell végrehajtani. Tekintse meg a lehetséges, hogy a SACÁBÓL példány és a hálózati átviteli sebességet megkövetelő egyidejű kapcsolatok száma. 
    - Ez a lépés elengedhetetlen. Méretezheti a virtuális gépeket, és azonosíthatja a licenceket, amelyek szükségesek a SACÁBÓL példányt használ a különböző szállítóktól származó segít. 
    - Egy jó költségelemzés méretezési gyakorlathoz nélkül nem hajtható végre. Megfelelő méretezés is lehetővé teszi, hogy a legjobb teljesítmény érdekében. 


## <a name="most-common-deployment-scenario"></a>Leggyakoribb üzembe helyezési forgatókönyv

 Számos Microsoft-ügyfelek ment keresztül a teljes telepítés vagy a SACÁBÓL környezetek legalább tervezési fázisában. Személyre tárt fel a leggyakoribb üzembe helyezési forgatókönyv betekintést. Az alábbi ábrán a leggyakoribb architektúrája látható: 


![SACÁBÓL architekturális diagramja](media/sacav2commonscenario.png) 


Ahogy az ábrán látható, DoD-ügyfelek általában két a DISA BCAPs fizessen elő. Ezek közül az egyik abban, ahol a nyugati parton és a többi életét keleti partján. Az ExpressRoute privát partnert engedélyezve van az Azure-ban minden egyes DISA BCAP helyen. Ezek az ExpressRoute-partnerek majd kapcsolódnak a virtuális hálózati átjáró a védelmi Minisztériuma – keleti és védelmi Minisztériuma – középső Azure-régiókban. A SACÁBÓL példány van üzembe helyezve a védelmi Minisztériuma – keleti és védelmi Minisztériuma – középső Azure-régiókban. Az összes bejövő és kimenő forgalomra végig, és az ExpressRoute-kapcsolaton a DISA BCAP onnan.

Alapvető tulajdonosi alkalmazások majd megtervezése az alkalmazások üzembe helyezése az Azure-régióban. Virtuális hálózati társviszony-létesítést úgy, hogy az alkalmazás virtuális hálózat csatlakoztatása a SACÁBÓL virtuális hálózatot használják. Ezután ezek kényszerített bújtatás minden forgalmukat az VDSS-példány.

Ez az architektúra azt javasoljuk, mert megfelelnek-e SCCA követelményeknek. Magas rendelkezésre állású és rugalmasan skálázható legyen. Üzembe helyezését és felügyeletét is leegyszerűsíti.

## <a name="automated-saca-deployment-options"></a>Automatizált SACÁBÓL üzembe helyezési beállítások

 Mint már említettük, a Microsoft partneri kapcsolatot alakított két megoldás hozhat létre automatizált SACÁBÓL infrastruktúra-sablon használatával. Mindkét sablonok üzembe helyezése a következő Azure-összetevőket: 

- Virtuális hálózat SACÁBÓL
    - Felügyeleti alhálózat
        - Ez az alhálózat, felügyeleti virtuális gépek és szolgáltatások vannak telepítve, más néven a jump mezőkbe.
        - VDMS subnet
            - Ez az alhálózat, virtuális gépek és szolgáltatások VDMS használt telepítve vannak.
        - Nem megbízható és megbízható alhálózatok
            - Ezen alhálózatok, ahol a virtuális készülékek üzembe vannak helyezve.
        - Átjáróalhálózat
            - Ez az alhálózat, ahol az ExpressRoute-átjárót telepíti.
- Felügyeleti helyettesítő be virtuális gépek
    - A környezet sávon kívüli felügyelethez használhatók.
- Hálózati virtuális berendezések
    - Vagy a Citrix használ, vagy az F5 alapú, mely a sablon üzembe helyezése.
- Nyilvános IP-címek
    - Használhatók front end mindaddig, amíg az ExpressRoute online állapotba kerül. Ezek az IP-címek Azure privát címterét háttérbeli fordítani.
- Útvonaltáblák 
    - Automation során alkalmazott, ezek táblák alagút kényszerítése minden forgalmat irányítson a virtuális készüléken keresztül.
- Azure-terheléselosztók – Standard Termékváltozat
    - Adatforgalom elosztását a berendezések használhatók.
- Hálózati biztonsági csoportok
    - Szabályozhatja, hogy mely típusú forgalom bizonyos végpontokra továbbítható használhatók.


### <a name="citrix-saca-deployment"></a>A Citrix SACÁBÓL üzembe helyezés

A Citrix a központi telepítési sablont magas rendelkezésre állású Citrix ADC készülékek két réteg helyez üzembe. Ez az architektúra megfelel-e a VDSS követelményeinek. 

![A Citrix SACÁBÓL diagramja](media/citrixsaca.png)


A Citrix dokumentációja és üzembe helyezési parancsfájlt: [a GitHub-hivatkozás](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca).


 ### <a name="f5-saca-deployment"></a>F5 SACÁBÓL üzembe helyezés

Két külön F5 központi telepítési sablonok két különböző architektúra terjed ki. Az első sablon csak egy réteg F5 berendezések magas rendelkezésre állású aktív-aktív konfigurációban rendelkezik. Ez az architektúra megfelel a követelményeinek VDSS. A második sablon beállítása a magas rendelkezésre állású aktív – aktív-F5s hozzáadja. Ez a második réteg lehetővé teszi, hogy az ügyfelek számára a saját IP-CÍMEK külön az F5 billentyűt az F5 rétegek között. Nem minden DoD-összetevőket kell használatának előírt, konkrét IP-CÍMEK. Ha ez a helyzet, F5 berendezések egyetlen réteg a legtöbb működik, mivel az architektúra az IP-CÍMEK az F5 eszközökön is tartalmaz.

![F5 SACÁBÓL diagramja](media/f5saca.png)

Az F5 dokumentációja és üzembe helyezési parancsfájlt: [a GitHub-hivatkozás](https://github.com/f5devcentral/f5-azure-saca).












