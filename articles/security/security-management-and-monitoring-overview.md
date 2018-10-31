---
title: Az Azure security management és a teljesítményfigyelés áttekintése |} A Microsoft Docs
description: Ez a cikk az Azure által biztosított, ezzel elősegítve az a felügyeleti biztonsági funkciók és szolgáltatások áttekintése és az Azure cloud services és virtual machines figyelését.
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: 7dfc4329b338a65169c81521360264753bafa9d6
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239916"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Az Azure security management és a teljesítményfigyelés áttekintése

Azure biztonsági mechanizmusai, ezzel elősegítve az a felügyeleti és monitorozási az Azure cloud services és a virtuális gépek (VM). Ez a cikk ezek az alapvető biztonsági funkciók és szolgáltatások áttekintést nyújt. Hivatkozások, amelyek az egyes részletek adjon, hogy további cikkek állnak rendelkezésre.

A Microsoft cloud Services szolgáltatások biztonságát partneri és a egy Ön és a Microsoft között megosztott felelősségre. A Microsoft felelős az Azure platform és az adatközpontok fizikai biztonsága (védelmet biztosítanak például zárolt jelvény-bejegyzés ajtók kerítések és őrök használatával). Az Azure biztosít erős szintű felhőbeli biztonsági a szoftver rétegben, amely megfelel vevői biztonsági, adatvédelmi és megfelelőségi igényeit.

Saját adatait és identitások, a felelősség őket, a helyszíni erőforrások és felhőalapú összetevőit, amelyen vezérlőelem rendelkezik biztonságának védelme. A Microsoft biztosít, biztonsági szabályozások és intézkedések saját adatok és alkalmazások védelmére. A felelősség a biztonság mértékét felhőszolgáltatás típusa alapján.

Az alábbi ábra a Microsoft és az ügyfél felelőssége kiegyensúlyozása foglalja össze.

![Közös felelősség][1]

Biztonságkezelés kapcsolatos további információkért lásd: [biztonságkezelés az Azure-ban](azure-security-management.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-kezelés Azure-erőforrások biztosít. Az RBAC használatával biztosíthat a felhasználók csak olyan mértékű hozzáférést, amelyek a feladataik elvégzéséhez szükségük van. RBAC segítségével, győződjön meg arról, hogy személyek elhagyja a szervezetet, ha azok nem férhet hozzá, a felhőben lévő erőforrásokhoz.

További információ:

* [Az RBAC az Active Directory csapat blogja](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ral például a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky biztonságiszoftver-gyártók kártevőirtó szoftvert is használhatja. Ez a szoftver segítségével, a virtuális gépek kártékony fájlokkal, hirdetőprogramokkal és egyéb fenyegetések védelme.

A Microsoft Antimalware az Azure Cloud Services és Virtual Machines lehetővé teszi a PaaS-szerepkörök és a virtuális gépek kártevőirtó ügynök telepítéséhez. A System Center Endpoint Protection alapján, ez a funkció alkalmazható helyszíni bevált biztonsági technológia a felhőben.

Trend a mély integráció is kínálunk [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) és [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) termékeket az Azure platformon. Deep Security egy víruskereső megoldást, SecureCloud pedig egy titkosítási megoldás. Deep Security-bővítmény modellen belüli virtuális gépek van telepítve. Az Azure portal felhasználói felületén és a PowerShell használatával is használni kívánja Deep Security alatt hoz létre új virtuális gépek vagy a már telepített meglévő virtuális gépeken belül.

A Symantec Endpoint Protection (SEP) használata is támogatott az Azure-ban. Portál-integráció megadhatja, hogy szeretne-e a SEP használni egy virtuális gépen. SEP is telepíthető egy új virtuális Gépet az Azure Portalon keresztül, vagy egy meglévő virtuális Gépet a PowerShell segítségével telepíthető.

További információ:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Microsoft Antimalware az Azure Cloud Services és Virtual Machines](azure-security-antimalware.md)
* [Telepítése és konfigurálása a Trend Micro Deep Security szolgáltatásként, a Windows virtuális gép](../virtual-machines/windows/classic/install-trend.md)
* [Telepítése és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Azure-beli virtuális gépek védelmére szolgáló új kártevőirtó-beállítások](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure multi-factor Authentication a egynél több ellenőrzési módszer használatát igénylő hitelesítési módszer. Hozzáadja a kritikus fontosságú második biztonsági réteget felhasználói bejelentkezéseket és tranzakciókat. 

Multi-factor Authentication hitelesítés révén biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz, miközben a felhasználó igény szerint egy egyszerű bejelentkezési folyamat. Ellenőrzési lehetőségek (telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítési vagy ellenőrző kódot) és a harmadik féltől származó OATH jogkivonatokkal erős hitelesítést biztosít.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Az Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Az Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloud, amely megkönnyíti, amelyet egy kapcsolatszolgáltató dedikált privát kapcsolaton keresztül. Az expressroute-tal és a Microsoft felhőszolgáltatásai, például Azure, Office 365 és CRM Online kapcsolatokat létesíthet. Kapcsolatok lehetnek::

* Bármely (IP VPN) hálózat.
* A pontok közötti Ethernet-hálózaton.
* Virtuális cross-kapcsolatot egy közös elhelyezési létesítményben egy kapcsolatszolgáltatón keresztül. 

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Az interneten több megbízhatósági megbízhatóbbak, gyorsabbak, kisebb a késésük, és biztonságosabbak a szokásos internetkapcsolatoknál is kínálnak.

További információ:

* [Az ExpressRoute technikai áttekintése](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók

VPN-átjárók, más néven az Azure virtuális hálózati átjárók, a virtuális hálózatok és a helyszínek közötti hálózati adatforgalom elküldésére szolgálnak. A (hálózati) az Azure virtuális hálózatai között forgalmat küldeni azokat is használhatók. VPN-átjárók adja meg a biztonságos létesítmények közötti kapcsolatok az Azure és az infrastruktúra között.

További információ:

* [VPN-átjárókról](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure-hálózat biztonsági áttekintése](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Néha felhasználóknak el kell végezni a privilegizált műveleteket az Azure-erőforrások vagy más SaaS-alkalmazásokhoz. Ez gyakran azt jelenti, hogy szervezetek számára tegye lehetővé számukra állandó emelt szintű hozzáférés az Azure Active Directoryban (Azure AD). 

Egy egyre bővülő biztonsági kockázatot jelent a felhőben üzemeltetett erőforrásokhoz, mert a szervezetek nem elég figyelheti, mit ezek a felhasználók általi használatát az emelt szintű hozzáférés. Ha emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, ezenkívül egy megsértésének hatással lehet a szervezet teljes felhőalapú biztonsági. Az Azure AD Privileged Identity Management segít oldja meg a kockázat csökkentésének, a jogosultságok expozíciós idejének csökkentéséhez és a használati betekintést növelésével.  

Privileged Identity Management bevezeti a fogalmát egy ideiglenes rendszergazdája egy szerepkör vagy a rendszergazdai hozzáférés "csak az időben". Az ilyen típusú rendszergazdai egy felhasználónak a hozzárendelt szerepkör-aktiválási folyamat elvégzéséhez szüksége. Az aktiválási folyamat módosítja a felhasználó-hozzárendelés egy szerepkörhöz az Azure ad-ben az aktív, a megadott ideig inaktív időszak.

További információ:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Az Azure AD Privileged Identity Management – első lépések](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Az Azure AD Identity Protection biztosít nézetet jelenít meg a gyanús bejelentkezési tevékenységeket és a lehetséges biztonsági résekről, a vállalati védelme érdekében. Identity Protection észleli a gyanús tevékenységek a felhasználók és a jogosultsági szintű (rendszergazdai) identitásokat, mint jelek alapján:

* Találgatásos támadások.
* Kiszivárgott hitelesítő adatok.
* Bejelentkezések ismeretlen helyről és fertőzött eszközökről.

Értesítések és a javasolt javítás megadásával Identity Protection segít valós időben kockázatok csökkentése. Felhasználói kockázat súlyosságát számítja ki. Kockázatalapú szabályzatok érheti el a jövőbeli fenyegetésekkel szembeni védelem alkalmazás automatikusan segítségével konfigurálhatók.

További információ:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9-on: Az Azure AD és az identitás: Identity Protection előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Az Azure Security Center segít megelőzését, észlelését és háríthatja el a fenyegetéseket. A Security Center lehetővé teszi, nagyobb betekintést és szabályozhatóbbá, az Azure-erőforrások biztonságát. Integrált biztonsági monitorozást és felügyeletet biztosít az Azure-előfizetések között. Segít a fenyegetések szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni.

A Security Center segítségével optimalizálásához és monitorozásához által az Azure-erőforrások biztonságának:

* Engedélyezi, hogy az Azure-előfizetések erőforrásaihoz, a következők szerint házirendeket határozhat meg:
  * A vállalat biztonsági igényeinek.
  * Az alkalmazások és az egyes előfizetések adatainak érzékenysége típusa.
* Az Azure-beli virtuális gépek, a hálózati és az alkalmazások állapotának monitorozása.
* A rangsorolt biztonsági riasztások listája, többek között az integrált partnermegoldásoktól érkező riasztásokat biztosít. Gyors vizsgálatára támadás és javaslatok megoldásáról a szükséges információkat is tartalmaz.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)
* [Az Azure Security Centerben a biztonságos pontszám javítása](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph Microsoft termék- és a valós idejű fenyegetésvédelmet biztosít. Threat intelligence és a biztonsági adatok biztosít, amelyek a szervezeti biztonság megszilárdítása is nagy mennyiségű hivatkozó fejlett analitikai használ. A Microsoft fejlett analitikát használ – feldolgozása több mint 450 milliárd hitelesítés havonta, 400 milliárd e-mailek kártevők és adathalász vizsgálata és egy milliárd eszközön –, hogy részletesebb elemzéseket. Ezek insights segítségével észlelheti és elháríthatja a támadásokat gyorsan a szervezetben.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

