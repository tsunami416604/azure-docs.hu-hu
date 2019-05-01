---
title: Biztonságos számítástechnikai architektúrájának Azure
description: Ez a referenciaarchitektúra egy vállalati szintű DMZ architektúrához, hálózati virtuális berendezések és más eszközök használatával. Ez az architektúra úgy lett kialakítva, a védelmi Minisztérium a biztonságos felhőalapú számítástechnikai architektúra működési követelmények teljesítéséhez. Azonban azt is javítható minden vállalat számára. Ez a hivatkozás a Citrix vagy F5 készülékeket használó két automatizált lehetőségeket tartalmazza.
author: jahender
ms.author: jahender
ms.date: 4/9/2019
ms.topic: article
ms.service: security
ms.openlocfilehash: f2e3d72db3f29dbc6d03b3259acb18daf684fb12
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917592"
---
# <a name="secure-azure-computing-architecture"></a>Biztonságos számítástechnikai architektúrájának Azure

Útmutatást nyújt a biztonságos virtuális hálózatok beállítása és konfigurálása a biztonsági eszközök és a DoD szabványok és eljárások által meghatározott szolgáltatások egyre növekvő több számítási feladatok üzembe helyezése az Azure-bA DoD-ügyféllel igényelne. Közzétett DISA a [biztonságos felhőalapú számítástechnikai architektúra (SCCA) működési követelmények dokumentum](https://iasecontent.disa.mil/stigs/pdf/SCCA_FRD_v2-9.pdf) 2017-ben. SCCA ismerteti a működési célkitűzések biztonságossá tételéhez a Defense Information System hálózat (DISN), és kereskedelmi Felhőszolgáltató kapcsolat mutat, és hogyan üzleti szempontból alapvető tulajdonosok biztonságos felhőalapú alkalmazások, a kapcsolati határt. Teljes kielégítését, hogy minden, amely kapcsolódik a kereskedelmi felhőben DoD entitás követi a szoftverterméket a SCCA FRD irányelveket.
 
Nincsenek a SCCA négy összetevője. A határ felhőalapú hozzáférési pont (BCAP), a Virtual Datacenter Security szolgáltatáskészlet (VDSS), a virtuális adatközpont által felügyelt szolgáltatásokon (VDMS), és megbízható felhő hitelesítőadat-kezelő (TCCM). A Microsoft olyan megoldás, amely megfelel a SCCA követelményeknek IL4 és a IL5 számítási feladatok Azure-ban futó fejlesztett ki. Az adott Azure-megoldás biztonságos Azure számítástechnikai architektúra (SACÁBÓL) nevezzük. Az üzembe helyezett SACÁBÓL megfelelnek-e a SCCA FRD lesz, és lehetővé teszi az ügyfeleknek védelmi Minisztériuma – a számítási feladatok áthelyezése egyszer a csatlakoztatott Azure-bA. 

SCCA útmutatást és architektúrákat adott DoD-ügyfelek számára, amelyek a SACÁBÓL legújabb módosításait akkor is hasznos polgári vevőinek a megbízható internetes kapcsolaton (TIC) útmutatást, valamint a Kereskedelmi ügyfeleink számára, akik szeretné implementálhat, biztonságos DMZ-t az azure-környezetek védelmét.


## <a name="secure-cloud-computing-architecture-components"></a>Biztonságos felhő-számítástechnikai architektúra összetevői

**BCAP**

A BCAP célja a DISN elleni támadások a felhőalapú környezetben. BCAP fog hajtsa végre a behatolásérzékelő és -megelőző, valamint kiszűrhetők a jogosulatlan forgalom. Lehet, hogy ez az összetevő a SCCA a más összetevőkkel közös elhelyezésű. Azt javasoljuk, hogy ez az összetevő helyezünk üzembe fizikai hardveren. Az alábbiakban megtalálja a BCAP biztonsági követelmények listáját.

***BCAP biztonsági követelmények***

![BCAP követelmények – mátrix](media/bcapreqs.png)


**VDSS**

A VDSS célja védelmét a védelmi Minisztérium alapvető tulajdonos alkalmazásokhoz, amelyek az Azure-ban. VDSS a biztonsági műveletek tömeges a SCCA hajt végre. Ez a következő parancsmagsorozattal adatforgalom vizsgálatának az Azure-ban futó alkalmazások védelmére. Ez az összetevő belül az Azure-környezet adható meg.

***VDSS biztonsági követelmények***

![VDSS követelmények – mátrix](media/vdssreqs.png)

**VDMS**

VDMS célja, hogy a gazdagép a biztonságot, valamint megosztott data center-szolgáltatásokat. VDMS funkcióit futtathatja a hubon, az SCCA, vagy az üzletmenet szempontjából tulajdonosa a saját konkrét Azure-előfizetésében lévő darabok, üzembe helyezhet. Ez az összetevő belül az Azure-környezet adható meg.

***VDMS biztonsági követelmények***

![VDMS követelmények – mátrix](media/vdmsreqs.png)


**TCCM**

TCCM Ez egy üzleti szerepkör. Ez a személy lesz a SCCA kezeléséért. Feladataik közé tartozik a csomagok és a felhőalapú környezetét, identitás biztosítása a fiók hozzáférési szabályzatai létrehozó és hozzáférés-kezelés megfelelő működését, és a Felhőbeli hitelesítő adat felügyeleti csomag fenntartásához. Ez a személy hivatalos Authorizing nevezi. A BCAP VDSS és VDMS biztosít a képességeket a TCCM függvényeket feladat végrehajtásához szükséges.

***TCCM biztonsági követelmények***

![TCCM követelmények – mátrix](media/tccmreqs.png) 

## <a name="saca-components-and-planning-considerations"></a>SACÁBÓL összetevők és tervezési szempontok 

A SACÁBÓL referenciaarchitektúra üzembe helyezése az Azure-ban a VDSS és VDMS összetevők, valamint a TCCM engedélyezése célja. Ez az architektúra olyan moduláris, ami azt jelenti, hogy az összes VDSS és VDMS darabjait is élő az információs központ vagy az egyes vezérlőelemeket érheti el az alapvető tulajdonos helyet vagy akár a helyszíni. A Microsoft csapatunk javaslat, hogy közös elhelyezése a VDSS és VDMS összetevőket, a központi virtuális hálózati keresztül kapcsolódhatnak az összes alapvető tulajdonos. Az alábbi ábra mutatja be a javasolt architektúra. 


![SACÁBÓL architekturális diagramja](media/sacav2generic.png)

A SCCA előírásainak való megfelelést stratégia és a technikai architektúrára tervezésekor rengeteg dolgot figyelembe venni. Fontos, hogy a következő témakörök az elejétől figyelembe venni, minden ügyfél e lesz szükségük. Az alábbi témakörök a problémákat, amelyek valós DoD ügyfelekkel merülnek fel, és általában a lelassíthatja a tervezési és végrehajtási törölték. 

- Melyik BCAP fogja használni a szervezet?
    - DISA BCAP
        - DISA két működési BCAPs ötszög és tábor Roberts hitelesítésszolgáltató, a harmadik hamarosan online van. 
        - DISA a BCAPs minden rendelkezik ExpressRoute-Kapcsolatcsoportok az Azure-ba, amelyek DoD-ügyfél a hálózati kapcsolatot. 
        - DISA már van egy vállalati szintű Microsoft-Peering munkamenet DoD vásárlóknak, akik az előfizetést a Microsoft SaaS-eszközök, például az Office 365-höz. DISA BCAP használatával engedélyezheti a kapcsolat és a társviszony-létesítés a SACÁBÓL példányra. 
    - A saját BCAP összeállítása
        - Ehhez arra lenne szükség, hogy egy közös helyen tárolt Center terület címbérlet, és állítson be egy ExpressRoute-kapcsolatcsoporttal az Azure-bA. 
        - Ez a beállítás további jóváhagyás lesz szükséges 
        - További jóváhagyást, és a egy fizikai build ki, mert ez a beállítás a legtöbb időt vesz igénybe. 
    - Microsoft azt javasolja, hogy kihasználhassák a DISA BCAP. Ez a beállítás könnyen elérhető, rendelkezik beépített redundancia, és már rendelkezik az éles környezetben még ma, működő ügyfelek.
- Védelmi Minisztérium irányítható IP-címtér
    - A peremen DoD irányítható IP-címterület használatára kell adnia. A NAT ezeket az Azure-ban magánhálózati IP-címtér, lehetőség érhető el.  
    - Lépjen kapcsolatba a védelmi Minisztérium hálózati adapter IP-címtér, beszerzése a beépülő modul beküldött DISA-részeként lesz szükség. 
    - Ha azt tervezi, NAT, privát címtér az Azure-ban, szüksége lesz legalább egy/24 alhálózati címtartomány hozzárendelt hálózati adapter tervez üzembe helyezni a SACÁBÓL mindegyik régióhoz. 
- Redundancia 
    - A Microsoft azt javasolja, hogy a SACÁBÓL példány legalább két régióban is üzembe helyezéséhez. Felhőben DoD Ez azt jelentené, helyezi üzembe azt DoD mindkét régióban. 
    - Ajánlott továbbá, hogy legalább két BCAPs külön az ExpressRoute-Kapcsolatcsoportok-n keresztül kapcsolódni. Express útvonalak majd minden egyes régió SACÁBÓL példány lehet kapcsolódni. 
- Védelmi Minisztérium összetevő-specifikus követelmények
    - A szervezet rendelkezik a SCCA követelmények kívül különleges követelményeket? (Egyes szervezetek követelményei adott IP-CÍMEK)
- SACÁBÓL egy moduláris architektúra  
    - Használja a környezetnek szüksége csak összetevőit. 
        - Az nva-k üzembe egyetlen vagy több rétegből
        - Az integrált IP-CÍMEK vagy a saját IP-CÍMEK használata
- Védelmi Minisztérium hatásszint az alkalmazások és adatok
    - Ha a IL5 régiókban futó alkalmazások lehetőségét, ajánlott a SACÁBÓL példány IL5 megoldásokat. A példány IL4 alkalmazásokat is IL5 is használható. De IL5 alkalmazás elé IL4 SACÁBÓL példány valószínűleg nem fog kapni akkreditáció. 
- Mely hálózati virtuális berendezés gyártójához fog használni a VDSS?
    - Ahogy korábban említettük, a SACÁBÓL hivatkozást felépíthető több különböző készülékeket és az Azure-szolgáltatások használatával. Automatizált megoldást-sablonok üzembe helyezése a SACÁBÓL architektúra az F5 billentyűt és a Citrix rendelkezünk. Ezek a megoldások az alábbi részletesen tárgyalja. 
- Mely Azure-szolgáltatásokat használni?
    - Nincsenek Azure-szolgáltatásokat, amely megfelel a log analytics, gazdagép-alapú védelmet és Azonosítók funkcionális követelményeket. Azt azonban nem lehetséges, hogy bizonyos szolgáltatások nem általánosan elérhető IL5 régiónkban. Ez a vezethet szükség egyes, a 3. fél eszközök használatára, ha az Azure-szolgáltatások nem felel meg a követelménynek. Nézze meg mi-eszközök segítségével magabiztosan, és az Azure natív azokat az eszközöket a megvalósíthatósági kell. 
    - Célszerű a Microsoft azt javasolja, hogy tetszőleges számú Azure natív az eszközöket használhatja a lehető épülnek, és a cloud security szem előtt, és a többi, az Azure platform zökkenőmentes integrálásával. Alább az Azure-SCCA különböző követelményeinek jól használható natív eszközök listája. 
        - [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview )
        - [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 
        - [A Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) 
        - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) 
        - [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
        - [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview)
        - [Az Azure-tűzfal](https://docs.microsoft.com/azure/firewall/overview) 
        - [Az Azure bejárati ajtajának](https://docs.microsoft.com/azure/frontdoor/front-door-overview)
        - [Biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview)
        - [Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)
        - [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)
- Méretezés
    - Egy méretezési gyakorlatban kell végrehajtani. Tekintse meg az egyidejű kapcsolatok száma szükség lehet a SACÁBÓL példány, valamint a hálózati átviteli sebességet megkövetelő keresztül kell. 
    - Ez a kritikus fontosságú lépés, azt fogja a Súgó gombra a virtuális gépek mérete, valamint segít azonosítani a SACÁBÓL példányban fog használni a különböző szállítóktól származó szükséges licencet. 
    - Egy jó költségek elemzése nem sikerült végrehajtani az nélkül méretezési ebben a gyakorlatban is fontos mindent van megfelelően méretezve, hogy a legjobb teljesítmény érdekében. 


## <a name="most-common-deployment-scenario"></a>Leggyakoribb üzembe helyezési forgatókönyv

A Microsoft rendelkezik a különböző ügyfelek, akik már elvégezte a teljes üzembe helyezési vagy legalább tervezési fázisában a SACÁBÓL környezetek. Ez lehetővé számunkra, hogy a leggyakoribb üzembe helyezési forgatókönyv információkhoz juthat. Az alábbi ábrán a leggyakrabban használt architektúrát ábrázolja. 


![SACÁBÓL architekturális diagramja](media/sacav2commonscenario.png) 


Az ábrán látható, általában DoD ügyfelek előfizetési két a DISA BCAPs, egy ezeket a nyugati parton életét és keleti partján életét. Az ExpressRoute privát partnert engedélyezve van az Azure-ban minden egyes DISA BCAP helyen. Ezek az ExpressRoute-partnerek majd kapcsolódnak a virtuális hálózati átjáró a védelmi Minisztériuma – keleti és védelmi Minisztériuma – középső Azure-régiók. A SACÁBÓL példány van üzembe helyezve, a védelmi Minisztériuma – keleti és védelmi Minisztériuma – középső Azure-régióban és az összes bejövő és kimenő adatforgalmi folyamatokat keresztül, és az Express Route-kapcsolat, a DISA BCAP onnan. 

Mission alkalmazások majd vásárlásom az Azure általuk fog üzembe helyezett alkalmazásaikat a tulajdonos és virtuális hálózatok közötti Társviszony használja a saját-alkalmazás csatlakoztatása a virtuális hálózat a SACÁBÓL virtuális hálózathoz. A kényszerített bújtatás a forgalmukat a VDSS-példány. 

Ez az architektúra erősen ajánlott a Microsoft, SCCA követelmények ül, magas rendelkezésre állású, rugalmasan skálázható legyen, és egyszerűbbé teszi az üzembe helyezési és kezelési.

## <a name="automated-saca-deployment-options"></a>Automatizált SACÁBÓL üzembe helyezési beállítások

 Korábban említettük, hogy a Microsoft alakított ki a két megoldás hozhat létre automatizált SACÁBÓL infrastruktúra-sablon használatával. Mindkét sablonok fog üzembe helyezni a következő Azure-összetevőket. 

- SACA Virtual Network
    - Felügyeleti alhálózat
        - Ha felügyeleti virtuális gépek és szolgáltatások vannak telepítve (jump mezők)
        - VDMS Subnet
            - Amennyiben a használt virtuális gépek és szolgáltatások telepítve vannak-e a VDMS
        - Nem megbízható és megbízható alhálózatok 
            - Hol vannak telepítve a virtuális készülékek
        - Átjáró-alhálózat
            - Az ExpressRoute-átjárót telepíti
- Felügyeleti helyettesítő be virtuális gépek
    - Sávon kívüli felügyeleti környezet használja.
- Hálózati virtuális berendezések
    - A Citrix vagy az F5, attól függően, melyik sablon üzembe helyezése.
- Nyilvános IP-címek
    - Használja az előtérbeli mindaddig, amíg az ExpressRoute online állapotba kerül. Ezek az IP-címek, a háttérkiszolgáló az Azure privát címterét lefordítja
- Útvonaltáblák 
    - Során alkalmazott automation, ezek útválasztási táblázatok-alagút kényszerítése minden forgalmat a virtuális berendezésen keresztül
- Azure-Terheléselosztók – Standard Termékváltozat
    - Ezek használhatók adatforgalom elosztását a berendezések
- Network Security Groups (Hálózati biztonsági csoportok)
    - Milyen típusú forgalom bizonyos végpontokra továbbítható vezérlésére használt


**A Citrix SACÁBÓL üzembe helyezés**

A Citrix hozott létre, amely magas rendelkezésre állású Citrix ADC készülékek két réteg telepíti a központi telepítési sablont. Ez az architektúra megfelel-e a VDSS követelményeinek. 

![A Citrix SACÁBÓL diagramja](media/citrixsaca.png)


A Citrix dokumentáció és a telepítési parancsfájl található [itt.](https://github.com/citrix/netscaler-azure-templates/tree/master/templates/saca)


 **F5 SACÁBÓL üzembe helyezés**

F5 lefedő két különböző architektúra két különböző üzembe helyezési sablonokat hozott létre. Már csak egy réteg F5 berendezések magas rendelkezésre állású aktív-aktív konfigurációban. Ez az architektúra megfelel a követelményeinek VDSS. A második beállítása a magas rendelkezésre állású aktív – aktív-F5s hozzáadja. Ez a második réteg célja, hogy az ügyfelek számára a saját IP-CÍMEK külön az F5 billentyűt az F5 rétegek között. Nem minden DoD-összetevőket kell használatának előírt, konkrét IP-CÍMEK. Ha ez a helyzet, F5 berendezések egyetlen réteg esetében működnek legtöbb óta, hogy architektúra tartalmazza az IP-CÍMEK az F5 eszközökön.  

![A Citrix SACÁBÓL diagramja](media/f5saca.png)

F5 dokumentáció és a telepítési parancsfájl található [itt.](https://github.com/f5devcentral/f5-azure-saca) 












