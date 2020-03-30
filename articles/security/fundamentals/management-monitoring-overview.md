---
title: Felügyeleti és figyelési biztonsági funkciók - Microsoft Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure által az Azure felhőszolgáltatások és virtuális gépek felügyeletéhez és felügyeletéhez nyújtott biztonsági funkciókról és szolgáltatásokról.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162744"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Az Azure biztonsági kezelése és figyelése – áttekintés
Ez a cikk áttekintést nyújt az Azure által az Azure felhőszolgáltatások és virtuális gépek felügyeletéhez és felügyeletéhez nyújtott biztonsági funkciókról és szolgáltatásokról.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-kezelést biztosít az Azure-erőforrások. Az RBAC használatával csak annyi hozzáférést adhat a személyeknek, amennyi a feladataik elvégzéséhez szükséges. Az RBAC abban is segítséget nyújt, hogy amikor az emberek elhagyják a szervezetet, elveszítik a hozzáférést a felhőben lévő erőforrásokhoz.

További információ:

* [Az Active Directory csapatblogja az RBAC-on](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ral olyan jelentős biztonsági gyártók kártevőirtó szoftvereit használhatja, mint a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a rosszindulatú fájloktól, adware-től és más fenyegetésektől.

A Microsoft Antimalware for Azure Cloud Services és virtuális gépek lehetővé teszi, hogy telepítsen egy kártevőirtó ügynököt paaS-szerepkörökhöz és virtuális gépekhez. A System Center Endpoint Protection szolgáltatás alapján ez a funkció bizonyítottan helyszíni biztonsági technológiát hoz a felhőbe.

A Trend [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) és [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) termékeinek mélyreható integrációját is kínáljuk az Azure platformon. A Deep Security egy víruskereső megoldás, a SecureCloud pedig titkosítási megoldás. A Deep Security egy bővítménymodellen keresztül van telepítve a virtuális gépeken. Az Azure Portal felhasználói felületének és a PowerShell használatával választhat, hogy a Deep Security belül az új virtuális gépek, amelyek a megpördült, vagy a meglévő virtuális gépek, amelyek már telepítve vannak.

A Symantec Endpoint Protection (SEP) az Azure-ban is támogatott. A portálintegráción keresztül megadhatja, hogy a SEP-t egy virtuális gépen kívánja-e használni. A SEP telepíthető egy új virtuális gépre az Azure Portalon keresztül, vagy egy meglévő virtuális gépre a PowerShellen keresztül is telepíthető.

További információ:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft kártevőirtó az Azure Felhőszolgáltatásokhoz és a virtuális gépekhez](antimalware.md)
* [A Trend Micro Deep Security szolgáltatásként való telepítése és konfigurálása Windows virtuális gépen](/azure/virtual-machines/windows/classic/install-trend)
* [A Symantec Endpoint Protection telepítése és konfigurálása Windows virtuális gépen](/azure/virtual-machines/windows/classic/install-symantec)
* [Új kártevőirtó beállítások az Azure virtuális gépek védelmére](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure többtényezős hitelesítés olyan hitelesítési módszer, amely több ellenőrzési módszert igényel. Hozzáadja a biztonság kritikus második rétegét a felhasználói bejelentkezések és tranzakciók számára.

A többtényezős hitelesítés segít biztosítani az adatokhoz és alkalmazásokhoz való hozzáférést, miközben kielégíti a felhasználói igényeket egy egyszerű bejelentkezési folyamathoz. Erős hitelesítést biztosít számos ellenőrzési lehetőség (telefonhívás, szöveges üzenet vagy mobilalkalmazás értesítési vagy ellenőrző kód) és harmadik fél OATH tokenjein keresztül.

További információ:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Az Azure Multi-Factor Authentication működése](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Az Azure ExpressRoute segítségével kibővítheti helyszíni hálózatait a Microsoft Cloud szolgáltatásba egy dedikált magánkapcsolaton keresztül, amelyet egy kapcsolatszolgáltató segít. Az ExpressRoute segítségével kapcsolatokat létesíthet a Microsoft felhőszolgáltatásaival, például az Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolat a következő innen származhat:

* Egy tetszőleges (IP VPN) hálózat.
* Pont-pont Ethernet hálózat.
* Virtuális keresztkapcsolat egy helymegosztási létesítmény kapcsolódási szolgáltatóján keresztül.

Az ExpressRoute-kapcsolatok nem mennek át a nyilvános interneten. Nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot kínálnak, mint az interneten keresztüli tipikus kapcsolatok.

További információ:

* [Az ExpressRoute technikai áttekintése](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók

Vpn-átjárók, más néven Az Azure virtuális hálózati átjárók, a virtuális hálózatok és a helyszíni helyek közötti hálózati forgalom küldésére szolgálnak. Emellett az Azure-on belüli (hálózatról hálózatra) lévő több virtuális hálózat közötti forgalom küldésére is szolgálnak. A VPN-átjárók biztonságos, létesítmények közötti kapcsolatot biztosítanak az Azure és az infrastruktúra között.

További információ:

* [VPN-átjárók – beolvasása](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Az Azure hálózati biztonságának áttekintése](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Néha a felhasználóknak emelt szintű műveleteket kell végrehajtaniuk az Azure-erőforrásokban vagy más SaaS-alkalmazásokban. Ez gyakran azt jelenti, hogy a szervezetek állandó kiemelt hozzáférést biztosítanak számukra az Azure Active Directoryban (Azure AD).

Ez egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások számára, mivel a szervezetek nem tudják megfelelően figyelni, hogy ezek a felhasználók mit csinálnak a kiemelt hozzáférésükkel. Továbbá, ha egy kiemelt hozzáférésű felhasználói fiók biztonsága sérül, hogy egy megsértése hatással lehet a szervezet teljes felhőbiztonság. Az Azure AD kiemelt identitáskezelés segít megoldani ezt a kockázatot azáltal, hogy csökkenti a jogosultságok expozíciós idejét, és növeli a használat láthatóságát.  

A kiemelt identitáskezelés egy szerepkör vagy a "just in time" rendszergazdai hozzáférés ideiglenes rendszergazdájának fogalmát mutatja be. Ez a fajta rendszergazda olyan felhasználó, akinek el kell végeznie az adott hozzárendelt szerepkör aktiválási folyamatát. Az aktiválási folyamat módosítja a felhasználó hozzárendelését egy szerepköraz Azure AD-ben inaktívról aktívra, egy meghatározott ideig.

További információ:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Ismerkedés az Azure AD Privileged Identity Management alkalmazással](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Az Azure AD Identity Protection a gyanús bejelentkezési tevékenységek és a potenciális biztonsági rések összevont nézetét biztosítja a vállalkozás védelme érdekében. Az Identity Protection észleli a gyanús tevékenységeket a felhasználók és a kiemelt (rendszergazdai) identitások számára, például a jelek alapján:

* Brutális támadások.
* Kiszivárgott adatok.
* Ismeretlen helyekről és fertőzött eszközökről történő bejelentkezések.

Az értesítések biztosításával és az ajánlott szervizeléssel az Identity Protection segít a kockázatok valós idejű csökkentésében. Kiszámítja a felhasználói kockázat súlyosságát. Konfigurálhatja a kockázatalapú házirendeket, hogy automatikusan védje az alkalmazáshozzáférést a jövőbeli fenyegetésektől.

További információ:

* [Azure Active Directory identitásvédelem](/azure/active-directory/active-directory-identityprotection)
* [9. csatorna: Azure AD és identitásmegjelenítés: Identitásvédelem előzetes verziója](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Az Azure Security Center lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. A Security Center nagyobb betekintést nyújt az Azure-erőforrások biztonságába, és annak szabályozását is. Integrált biztonsági figyelést és házirend-kezelést biztosít az Azure-előfizetésekben. Segít észlelni azokat a fenyegetéseket, amelyek egyébként észrevétlenek maradnak, és a biztonsági megoldások széles ökoszisztémájával működik együtt.

A Security Center segítségével optimalizálhatja és figyelheti az Azure-erőforrások biztonságát:

* Lehetővé teszi, hogy szabályzatokat határozzon meg az Azure-előfizetési erőforrásokhoz a következők szerint:
  * A vállalat biztonsági igényei.
  * Az egyes előfizetésekben lévő alkalmazások típusa vagy érzékenysége.
* Az Azure virtuális gépek, a hálózatkezelés és az alkalmazások állapotának figyelése.
* A rangsorolt biztonsági riasztások listájának megadása, beleértve az integrált partnermegoldásokból származó riasztásokat is. Azt is előírja, hogy szükség van a támadás gyors kivizsgálásához, és ajánlásokat, hogyan kell orvosolni azt.

További információ:

* [Az Azure Security Center bemutatása](../../security-center/security-center-intro.md)
* [Javítsa biztonságos pontszámát az Azure Security Centerben](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligens biztonsági grafikon

Az Intelligent Security Graph valós idejű veszélyforrások elleni védelmet biztosít a Microsoft termékeiben és szolgáltatásaiban. Olyan fejlett elemzéseket használ, amelyek hatalmas mennyiségű fenyegetésfelderítési és biztonsági adatot kapcsolnak össze, hogy olyan elemzéseket nyújtsanak, amelyek erősíthetik a szervezeti biztonságot. A Microsoft fejlett elemzéseket használ – havonta több mint 450 milliárd hitelesítést dolgoz fel, 400 milliárd e-mailt keres rosszindulatú programok és adathalászat után, és egymilliárd eszközt frissít – a részletesebb információk biztosítása érdekében. Ezek az elemzések segíthetnek a szervezetnek a támadások gyors észleléséhez és az azokra való gyors reagáláshoz.

* [Intelligens biztonsági grafikon](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Következő lépések
Ismerje meg a [megosztott felelősség modellt,](shared-responsibility.md) valamint azt, hogy a Microsoft mely biztonsági feladatokat kezeli, és mely feladatokat kezeli.

A biztonságkezelésről további információt az Azure biztonságkezelése című [témakörben talál.](management.md)
