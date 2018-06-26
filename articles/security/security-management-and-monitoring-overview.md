---
title: Az Azure biztonsági kezelési és figyelési áttekintés |} Microsoft Docs
description: Ez a cikk az Azure által biztosított, a kezelés segítése érdekében biztonsági szolgáltatások és szolgáltatások áttekintése és figyelési Azure felhőszolgáltatások és virtuális gépek tartalmazza.
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
ms.openlocfilehash: 9e538ac39af5b6df44860a4a70b0fd1e058c060c
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752288"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Az Azure biztonsági kezelési és figyelési áttekintés
Azure biztonsági mechanizmusai a felügyeleti és figyelési Azure felhőszolgáltatások és virtuális gépek (VM). Ez a cikk áttekintése ezeket az alapvető biztonsági funkciókat és szolgáltatásokat. Adja meg az egyes az adatait, így további cikkek hivatkozásokkal.

A Microsoft más felhőszolgáltatásaival biztonsági szintje partnerség és egy megosztott felelőssége, és a Microsoft között. Microsoft felelős az Azure platformon és az Adatközpont fizikai biztonságát (például zárolt jelvény-bejegyzés, kerítések vagy megóvja a biztonsági védelmet használatával). Azure biztosít erős szintű felhőalapú biztonságot a szoftver rétegben, amely megfelel az ügyfelek biztonsági, adatvédelmi és megfelelőségi igényeinek.

Saját adatait és identitások, a felelősséget védi őket, a helyszíni erőforrások biztonsági és a biztonsági felhő összetevők, amelyben az informatikai részleg ellenőrzése. A Microsoft biztosít biztonsági vezérlők és az adatok és alkalmazások védelméhez képességeket. A bizonyos fokú biztonsági felelősséget alapul a felhőalapú szolgáltatás.

Az alábbi táblázat a Microsoft és az ügyfél közötti felelősségi egyenleg foglalja össze.

![Közös felelősség][1]

Biztonsági-kezeléssel kapcsolatos további információkért lásd: [biztonságkezelés az Azure-ban](azure-security-management.md).

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Szerepköralapú hozzáférés-vezérlés (RBAC) biztosít a részletes hozzáféréskezelést az Azure-erőforrások. Szerepalapú segítségével biztosíthat a felhasználók csak olyan mértékű hozzáférést a munkája elvégzéséhez szükséges. Az RBAC segítségével győződjön meg arról, hogy személyek adja meg a szervezet, amikor megszakad a hozzáférés a felhőben található erőforrásokhoz.

További információ:

* [Szerepalapú az Active Directory csapat blogja](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kártevőirtó
Az Azure-ral például a Microsoft, a Symantec, Trend Micro, McAfee és Kaspersky fő biztonsági szállítóktól származó kártevőirtó szoftverek is használhatja. Ez a szoftver megvédi a virtuális gépek a rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben.

Azure Cloud Services és a virtuális gépek Microsoft Antimalware lehetővé teszi a PaaS szerepkörök és a virtuális gépek kártevőirtó ügynök telepítéséhez. A System Center Endpoint Protection alapján, ez a funkció számos lehetőséget kínál helyszíni bevált biztonsági technológia a felhőbe.

Szoros integrációja trend meg is fel [mély biztonsági](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) és [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) termékek az Azure platformon. A részletes biztonsági víruskereső megoldás, és SecureCloud egy titkosítási megoldás. Átfogó biztonsági rendszer belüli virtuális gépek egy bővítmény modell használatával. Az Azure portál felhasználói felület és a PowerShell használatával dönthet úgy, hogy a rendszer éppen hoz létre új virtuális gépek vagy a már telepített meglévő virtuális gépeken belül mély biztonság használatára.

Symantec Endpoint Protection (Szeptember) is támogatja az Azure-on. Portál integrálásán keresztül adhatja meg, hogy szeretne-e a virtuális gép Szeptember használni. Szeptember is telepíthető egy új virtuális Gépet az Azure-portálon, vagy egy meglévő virtuális gép PowerShell segítségével telepíthető.

További információ:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [A Microsoft Antimalware Azure Felhőszolgáltatások és virtuális gépek](azure-security-antimalware.md)
* [Hogyan kell telepíteni, és a Trend Micro mély biztonságának konfigurálása a Windows virtuális gép szolgáltatásként](../virtual-machines/windows/classic/install-trend.md)
* [Hogyan kell telepíteni, és a Symantec Endpoint Protection konfigurálása a Windows virtuális gép](../virtual-machines/windows/classic/install-symantec.md)
* [Új kártevőirtó lehetőségei az Azure virtuális gépek védelme](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Az Azure multi-factor Authentication egynél több ellenőrzési módszer használatát igénylő hitelesítési mód. Egy kritikus fontosságú második biztonsági szintként, hozzáadja a felhasználói bejelentkezéseket és tranzakciókat. 

Többtényezős hitelesítés segítségével hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz egyszerű bejelentkezési folyamatot a felhasználó igény szerint betartása mellett. Erős hitelesítés egy tartományon keresztül ellenőrzési lehetőségek (telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítés vagy ellenőrző kódot) és harmadik féltől származó OATH jogkivonatokkal biztosítja.

További információ:

* [Többtényezős hitelesítés](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Azure multi-factor Authentication működése](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Azure ExpressRoute segítségével a helyszíni hálózatokhoz kiterjeszti a Microsoft Cloud, amely a kapcsolat szolgáltatójánál megkönnyíthető dedikált titkos kapcsolaton keresztül. Az expressroute-nál is létrehozhat kapcsolatot a Microsoft felhőszolgáltatásai, például Azure Office 365 vagy CRM Online-hoz. Kapcsolat származhatnak:

- Bármely elem közöttiként (IP VPN) hálózat.
- A Point-to-Point protokoll Ethernet-hálózaton.
- Virtuális kereszt-keresztüli csatlakozást egy kapcsolat szolgáltatóját egy közös elhelyezés létesítményben. 

Az ExpressRoute-kapcsolatok nem nyissa meg a nyilvános interneten keresztül. További megbízhatóságát, gyorsabb sebességű, kisebb késések és nagyobb biztonságot nyújtana tipikus kapcsolatok az interneten keresztül is biztosítanak.

További információ:

* [ExpressRoute műszaki áttekintés](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók
Virtuális hálózatok és a helyszíni helyek közötti hálózati forgalom küldése VPN-átjárók, más néven az Azure virtuális hálózati átjárók szolgálnak. Akkor is használhatók az Azure (hálózati) több virtuális hálózat között forgalmat küldeni. VPN-átjárók adja meg az Azure és az infrastruktúra közötti biztonságos létesítmények közötti kapcsolathoz.

További információ:

* [Információk a VPN-átjárókról](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Az Azure biztonsági áttekintése](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Néha a felhasználóknak kell az Azure-erőforrások vagy más SaaS-alkalmazások kiemelt műveletek végrehajtására. Ez gyakran azt jelenti, hogy a szervezetek számukra állandó privilegizált hozzáférési jogosultsága az Azure Active Directory (Azure AD). 

Ez a felhőben üzemeltetett erőforrásokhoz az egyre növekvő biztonsági kockázatot jelent, mert a szervezeteknek elég nem tud figyelni, ezek a felhasználók tevékenységeit a rendszerjogosultságú hozzáférést. Emellett ha jogosultsági szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, egy megsértésének hatással lehet a szervezet általános felhő biztonsági. Hárítsa el a kockázat jogosultságokat a kitettség idő csökkentése és a használatának láthatóság növelésével segít az Azure AD Privileged Identity Management.  

A privileged Identity Management bemutatja a egy ideiglenes rendszergazdai egy szerepkör vagy a rendszergazdai hozzáférés "csak az időben". Az ilyen felügyeleti olyan felhasználó, aki az adott szerepkörrel rendelkeznek az aktiválási folyamat befejezéséhez szükséges. Az aktiválási folyamat módosítja a felhasználó egy szerepkörhöz az Azure AD aktív, egy meghatározott ideig inaktív időszak.

További információ:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Ismerkedés az Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Az Azure AD Identity Protection gyanús bejelentkezési tevékenységek és a vállalat védelme érdekében a potenciális biztonsági réseket konszolidált nézetét biztosítja. Azonosító adatok védelmét a felhasználók és a kiemelt (rendszergazda) identitások, például a jelek alapján gyanús tevékenységek észlelése:

- Találgatásos támadások során.
- Kiszivárgott hitelesítő adatokat.
- Bejelentkezések ismeretlen helyek és a fertőzött eszközökről.

Értesítések és az ajánlott javítási megadásával Identity Protection megelőzheti valós idejű kockázatot is rejt magában. Felhasználói kockázat súlyossági számítja ki. Beállíthatja, hogy elérje a jövőbeli fenyegetések védelmében alkalmazás automatikusan segítségével kockázat-alapú házirendek.

További információ:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [9. csatornán: Az Azure AD és az Identity: Identity Protection előzetes kiadásának](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Az Azure Security Center segítséget nyújt a megakadályozása, észlelésében és kezelésében fenyegetések ellen. A Security Center által biztosított, láthatósága nőtt és felett, Azure-erőforrások biztonsági ellenőrzést. Biztosít integrált biztonsági monitorozást és az Azure-előfizetések. Ez segítséget nyújt az szolgáló szabályzatkezelést, és számos biztonsági megoldással együttműködik.

A Security Center segítségével optimalizálhatja és által az Azure-erőforrások biztonságának figyelése:

* A következők szerint az Azure-előfizetés erőforrásaihoz tartozó házirendek megadhatók engedélyezése:
  * A vállalat biztonsági igényeinek.
  * Az alkalmazások és az egyes előfizetések adatainak érzékenysége alapján típusa.
* Az Azure virtuális gépeken, a hálózat és az alkalmazások állapotának figyelése.
* Rangsorolt biztonsági riasztások, beleértve az integrált partnermegoldások riasztásait listájával. A gyors vizsgálatra támadás és annak megoldásáról ajánlások kell információkat is biztosít.

További információ:

* [Az Azure Security Center bemutatása](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
