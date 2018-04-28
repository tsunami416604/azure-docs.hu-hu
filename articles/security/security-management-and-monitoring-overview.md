---
title: Az Azure biztonsági kezelési és figyelési áttekintés |} Microsoft Docs
description: " Azure biztonsági mechanizmusai a kezelési és figyelési Azure felhőszolgáltatások és virtuális gépek a segítése érdekében.  Ez a cikk áttekintése ezeket az alapvető biztonsági funkciókat és szolgáltatásokat. "
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
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 66a3a31574778bb1238498b523d51dc0d32ac22f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="azure-security-management-and-monitoring-overview"></a>Az Azure biztonsági kezelési és figyelési áttekintés
Azure biztonsági mechanizmusai a kezelési és figyelési Azure felhőszolgáltatások és virtuális gépek a segítése érdekében. Ez a cikk áttekintése ezeket az alapvető biztonsági funkciókat és szolgáltatásokat. Adja meg az egyes az adatait, így további cikkek hivatkozásokkal.

A Microsoft más felhőszolgáltatásaival biztonsági szintje egy partneri kapcsolat áll fenn, és a megosztott felelősség és a Microsoft között. Megosztott felelőssége azt jelenti, hogy a Microsoft a Microsoft Azure és a fizikai biztonságát adatközpontjaihoz felelős (például zárolt jelvény bejegyzés kerítések, vagy megóvja a biztonsági védelmet használatával). Ezenkívül az Azure biztosít erős szintű felhőalapú biztonságot kibővített ügyfelei biztonsági, adatvédelmi és megfelelőségi igényeinek megfelelő szoftver rétegben.

Saját adatait és identitások, a felelősséget védi őket, a helyszíni erőforrások biztonsági és a biztonsági felhő összetevők, amelyben az informatikai részleg ellenőrzése. Microsoft biztosít Önnek biztonsági vezérlők és az adatok és alkalmazások védelméhez képességeket. A bizonyos fokú biztonsági felelősséget alapul a felhőalapú szolgáltatás.

Az alábbi táblázat a Microsoft és az ügyfél felelősségi egyenleg foglalja össze.

![Közös felelősség][1]

A biztonsági felügyeletbe mélyebb bemutatója, lásd: [biztonságkezelés az Azure-ban](azure-security-management.md).

Az alábbiakban az alapvető szolgáltatásokat, a cikkben szerepelnek:

* Szerepköralapú hozzáférés-vezérlés
* Kártevőirtó
* Többtényezős hitelesítés
* ExpressRoute
* Virtuális hálózati átjárók
* Privileged identity management
* Identity Protection
* Security Center

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés (RBAC) biztosít a részletes hozzáféréskezelést az Azure-erőforrások. Szerepalapú használ, biztosíthat a felhasználók csak olyan mértékű hozzáférést a munkája elvégzéséhez szükséges.  Az RBAC segítségével győződjön meg arról, hogy ha személyek elhagyják a munkahelyet megszakad a hozzáférés a felhőben található erőforrásokhoz.

További információ:

* [Szerepalapú az Active Directory csapat blogja](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kártevőirtó
Az Azure-ral például a Microsoft, a Symantec, Trend Micro, McAfee és Kaspersky fő biztonsági szállítóktól származó kártevőirtó szoftver segítségével a virtuális gépek védelme rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben.

A Microsoft Antimalware lehetővé teszi a PaaS szerepkörök és a virtuális gépek kártevőirtó ügynök telepítéséhez. A System Center Endpoint Protection alapján, ez a funkció számos lehetőséget kínál helyszíni bevált biztonsági technológia a felhőbe.

Szoros integrációja trend meg is fel [mély biztonsági](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ és [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ termékek az Azure platformon. DeepSecurity víruskereső megoldás és SecureCloud egy titkosítási megoldás. DeepSecurity egy bővítmény modellt használó virtuális gépeken belül van telepítve. A portál felhasználói felület és a PowerShell használatával, dönthet úgy, új virtuális gépek láthatók, amelyek alatt hoz létre, vagy a már telepített meglévő virtuális gépeken belül DeepSecurity használatára.

Symantec végpont védelme (Szeptember) az Azure-on is támogatott. Portál integrálásán keresztül ügyfelek adhatja meg, hogy kíván-e a virtuális Gépen belül Szeptember használja. Szeptember is telepíthető egy teljesen új virtuális Gépet az Azure-portálon, vagy egy meglévő virtuális gép PowerShell használatával is telepíthető.

További információ:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Microsoft Antimalware Azure Felhőszolgáltatások és virtuális gépek](azure-security-antimalware.md)
* [Hogyan kell telepíteni, és a Trend Micro mély biztonságának konfigurálása a Windows virtuális gép szolgáltatásként](../virtual-machines/windows/classic/install-trend.md)
* [Hogyan kell telepíteni, és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Új kártevőirtó lehetőségei az Azure virtuális gépek – McAfee az Endpoint Protection védelme](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Többtényezős hitelesítés
Az Azure többtényezős hitelesítés (MFA), amely egynél több ellenőrzési módszer használatát igényli, és a kritikus fontosságú második biztonsági réteget ad hozzá felhasználói bejelentkezéseket és tranzakciókat hitelesítési mód. Többtényezős hitelesítés segítségével hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett. Erős hitelesítés, ellenőrzési lehetőségek széles keresztül biztosítja – a telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítés vagy ellenőrző kód és a harmadik féltől származó OATH-tokeneket.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

További információ:

* [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók
Virtuális hálózatok és a helyszíni helyek közötti hálózati forgalom küldése VPN-átjárók, más néven az Azure virtuális hálózati átjárók szolgálnak. Akkor is használhatók az Azure (VNet – VNet) több virtuális hálózat között forgalmat küldeni.  VPN-átjárók adja meg az Azure és az infrastruktúra közötti biztonságos létesítmények közötti kapcsolathoz.

További információ:

* [Információk a VPN-átjárókról](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Az Azure biztonsági áttekintése](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Néha a felhasználóknak kell az Azure-erőforrások vagy más SaaS-alkalmazások kiemelt műveletek végrehajtására. Ez gyakran azt jelenti, hogy a szervezetek meg kell adni nekik az Azure Active Directory (Azure AD) az állandó privilegizált hozzáférési jogosultsága. Ez a felhőben üzemeltetett erőforrásokhoz az egyre növekvő biztonsági kockázatot jelent, mert a szervezeteknek elég nem tud figyelni, ezek a felhasználók tevékenységeit a rendszerjogosultságú hozzáférést.
Továbbá ha jogosultsági szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, egy megsértésének jelentős hatással lehet az általános felhő biztonsági. Hárítsa el a kockázat jogosultságokat a kitettség idő csökkentése és a használatának láthatóság növelésével segít az Azure AD Privileged Identity Management.  

A privileged Identity Management bemutatja a egy ideiglenes rendszergazdai egy szerepkör vagy a "csak az időben" rendszergazdai hozzáféréssel, amely a felhasználókat, akiknek az adott szerepkörrel rendelkeznek az aktiválási folyamat befejezéséhez. Az aktiválási folyamat módosítja a felhasználó egy szerepkörhöz az Azure AD egy meghatározott ideig aktív inaktív időszak például nyolc óra.

További információ:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Ismerkedés az Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Az Azure Active Directory (AD) identitás Protection gyanús bejelentkezési tevékenységek és a vállalat védelme érdekében a potenciális biztonsági réseket konszolidált nézetét biztosítja. Azonosító adatok védelmét a felhasználók gyanús tevékenységek észlelése és a kiemelt (rendszergazda) identitások, a találgatásos támadásokkal, például jelek alapján elszivárgott a hitelesítő adatokat, és bejelentkezések ismeretlen helyekről, és fertőzött eszközök.

Értesítések és az ajánlott javítási megadásával Identity Protection megelőzheti valós idejű kockázatot is rejt magában. Felhasználói kockázat súlyosság alapján számítja ki, és beállíthatja, hogy elérje a jövőbeli fenyegetések védelmében alkalmazás automatikusan segítségével kockázat-alapú házirendek.

További információ:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [9. csatornán: Az Azure AD és az Identity: Identity Protection előzetes kiadásának](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Az Azure Security Center segít a megakadályozása, észlelésében és kezelésében fenyegetések, és biztosítja, hogy lássák, és szabályozhatják, az Azure-erőforrások biztonságának nőtt. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center segítségével optimalizálhatja és által az Azure-erőforrások biztonságának figyelése:

* Engedélyezi, hogy a vállalat biztonsági igényeinek megfelelően, és az alkalmazások típusának vagy az adatok érzékenységének megfelelően az Azure-előfizetés erőforrások házirendeket definiálhat az egyes előfizetések.
* Az Azure virtuális gépeken, a hálózat és az alkalmazások állapotának figyelése.
* Rangsorolt biztonsági riasztások, beleértve az integrált partneri megoldások, valamint a gyors vizsgálatra szükséges információkat és a támadás elhárításával kapcsolatos javaslatok riasztásait listájával.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
