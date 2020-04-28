---
title: Felügyeleti és monitorozási biztonsági funkciók – Microsoft Azure | Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure által biztosított biztonsági szolgáltatásokról és szolgáltatásokról az Azure Cloud Services és a Virtual Machines felügyeletének és figyelésének támogatásához.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "73162744"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Az Azure biztonsági felügyeletének és figyelésének áttekintése
Ez a cikk áttekintést nyújt az Azure által biztosított biztonsági szolgáltatásokról és szolgáltatásokról az Azure Cloud Services és a Virtual Machines felügyeletének és figyelésének támogatásához.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A szerepköralapú Access Control (RBAC) részletes hozzáférés-kezelést biztosít az Azure-erőforrásokhoz. A RBAC használatával csak a feladataik elvégzéséhez szükséges hozzáférés mennyiségét adhatja meg a felhasználóknak. A RBAC segítségével biztosíthatja, hogy amikor a felhasználók elhagyják a szervezetet, elvesztik a felhőben lévő erőforrásokhoz való hozzáférést.

További információ:

* [Active Directory csapat blogja a RBAC-on](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Kártevőirtó

Az Azure-ban olyan nagy biztonsági gyártóktól származó kártevő szoftvereket használhat, mint például a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky. Ez a szoftver segít megvédeni a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben.

A Microsoft antimalware for Azure Cloud Services és Virtual Machines lehetővé teszi, hogy egy antimalware-ügynököt is telepítsen a Pásti-szerepkörökhöz és a virtuális gépekhez. A System Center Endpoint Protection alapján ez a szolgáltatás bevált helyszíni biztonsági technológiát biztosít a felhőben.

Mély integrációt is kínálunk a trend [mély biztonsági](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) és [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) termékeihez az Azure platformon. A Deep Security egy víruskereső megoldás, a SecureCloud pedig egy titkosítási megoldás. A részletes biztonság a virtuális gépeken belül, egy bővítmény-modell használatával van üzembe helyezve. A Azure Portal felhasználói felület és a PowerShell használatával kiválaszthatja, hogy az új virtuális gépeken, illetve a már üzembe helyezett meglévő virtuális gépeken belül használja-e a mélyebb biztonságot.

A Symantec Endpoint Protection (SEP) az Azure-ban is támogatott. A portál integrációja segítségével megadhatja, hogy a SEP-et egy virtuális gépen kívánja használni. A SEP a Azure Portalon keresztül telepíthető egy új virtuális gépre, vagy a PowerShell használatával telepíthető egy meglévő virtuális gépre.

További információ:

* [Kártevőirtó megoldások telepítése Azure-beli virtuális gépeken](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft antimalware az Azure Cloud Services és Virtual Machines](antimalware.md)
* [A Trend Micro Deep Security telepítése és konfigurálása Windows rendszerű virtuális gépen](/azure/virtual-machines/windows/classic/install-trend)
* [Symantec-Endpoint Protection telepítése és konfigurálása Windowsos virtuális gépen](/azure/virtual-machines/windows/classic/install-symantec)
* [Új antimalware-beállítások az Azure-Virtual Machines védelméhez](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Az Azure Multi-Factor Authentication olyan hitelesítési módszer, amely több ellenőrzési módszer használatát igényli. Kritikus második biztonsági réteget hoz létre a felhasználói bejelentkezésekhez és tranzakciókra.

Multi-Factor Authentication segít megőrizni az adathozzáférést és az alkalmazásokhoz való hozzáférést, miközben a felhasználói igényeket egy egyszerű bejelentkezési folyamaton keresztül teljesíti. Erős hitelesítést biztosít számos ellenőrzési lehetőség (telefonhívás, szöveges üzenet vagy Mobile App Notification vagy ellenőrző kód) és harmadik féltől származó eskü-tokenek használatával.

További információ:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Mi az az Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Az Azure Multi-Factor Authentication működése](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Az Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft Cloud egy olyan dedikált privát kapcsolaton keresztül, amelyet egy kapcsolat szolgáltatója is megnyit. A ExpressRoute használatával kapcsolatokat létesíthet olyan Microsoft Cloud Services-szolgáltatásokkal, mint például az Azure, az Office 365 és a CRM Online. A kapcsolat a következő lehet:

* Bármilyen típusú (IP VPN-) hálózat.
* Pont-pont típusú Ethernet-hálózat.
* Egy közös elhelyezési létesítményben lévő kapcsolati szolgáltatón keresztüli virtuális kapcsolat.

A ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Nagyobb megbízhatóságot, gyorsabb sebességet, kisebb késést és nagyobb biztonságot biztosíthatnak, mint az interneten keresztüli szokásos kapcsolatok.

További információ:

* [Az ExpressRoute technikai áttekintése](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuális hálózati átjárók

A VPN-átjárók, más néven Azure-beli virtuális hálózati átjárók a virtuális hálózatok és a helyszíni telephelyek közötti hálózati adatforgalom elküldésére szolgálnak. Az Azure-ban több virtuális hálózat között is küldenek forgalmat (hálózatról hálózatra). A VPN-átjárók biztonságos, létesítmények közötti kapcsolatot biztosítanak az Azure és az infrastruktúra között.

További információ:

* [Tudnivalók a VPN-átjárók használatáról](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Az Azure hálózati biztonság áttekintése](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Időnként a felhasználóknak az Azure-erőforrásokban vagy más SaaS-alkalmazásokban kell Kiemelt műveleteket végezniük. Ez gyakran azt jelenti, hogy a szervezetek állandó jogosultsági szintű hozzáférést biztosítanak a Azure Active Directory (Azure AD) szolgáltatásban.

Ez egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások esetében, mivel a szervezetek nem tudják eléggé figyelni, hogy a felhasználók hogyan használják a Kiemelt hozzáférési jogosultságokat. Emellett, ha egy emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, az egyik megszegés befolyásolhatja a szervezet teljes Felhőbeli biztonságát. Azure AD Privileged Identity Management segít feloldani ezt a kockázatot azáltal, hogy csökkenti a jogosultságok expozíciós idejét és növeli a használat átláthatóságát.  

Privileged Identity Management bevezeti egy adott szerepkörhöz tartozó ideiglenes rendszergazda fogalmát, vagy az "időben" rendszergazdai hozzáférést. Ez a rendszergazda olyan felhasználó, akinek az adott szerepkörhöz tartozó aktiválási folyamatot kell végrehajtania. Az aktiválási folyamat egy adott időszakra módosítja a felhasználó hozzárendelését egy Azure AD-beli szerepkörben az inaktívról aktívra.

További információ:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Ismerkedés az Azure AD Privileged Identity Management alkalmazással](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection a gyanús bejelentkezési tevékenységek és a lehetséges sebezhetőségek összevont nézetét biztosítja a vállalata védelmének elősegítése érdekében. Az Identity Protection gyanús tevékenységeket észlel a felhasználók és a privilegizált (rendszergazdai) identitások számára a következő jelek alapján:

* Találgatásos támadásokat.
* Kiszivárgott hitelesítő adatok.
* Ismeretlen helyekről és fertőzött eszközökről történő bejelentkezések.

Az értesítések és az ajánlott szervizelés biztosításával a személyazonosság védelme segít a kockázatok valós idejű enyhítésében. Kiszámítja a felhasználói kockázati súlyosságot. A kockázatalapú házirendeket beállíthatja úgy, hogy automatikusan segítse az alkalmazások hozzáférését a jövőbeli fenyegetésektől.

További információ:

* [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection)
* [9. csatorna: Azure AD-és identitás-megjelenítés: Identity Protection előnézet](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Az Azure Security Center lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Security Center az Azure-erőforrások jobb láthatóságát és felügyeletét teszi lehetővé. Integrált biztonsági monitorozást és házirend-kezelést biztosít az Azure-előfizetések között. Segít felderíteni a fenyegetéseket, amelyek egyébként észrevétlenek lehetnek, és a biztonsági megoldások széles körű ökoszisztémával működnek.

A Security Center segítségével optimalizálhatja és figyelheti az Azure-erőforrások biztonságát:

* Az Azure-előfizetések erőforrásaihoz tartozó házirendek definiálásának lehetővé tétele a következőknek megfelelően:
  * A vállalat biztonsági igényeit.
  * Az egyes előfizetésekben szereplő alkalmazások típusa vagy az adatérzékenység.
* Azure-beli virtuális gépek, Hálózatkezelés és alkalmazások állapotának monitorozása.
* A rangsorolt biztonsági riasztások listája, beleértve az integrált partneri megoldások riasztásait is. Emellett biztosítja a támadás gyors kivizsgálásához és a megoldási javaslatokhoz szükséges információkat.

További információ:

* [Az Azure Security Center bemutatása](../../security-center/security-center-intro.md)
* [Javítsa a biztonságos pontszámot Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligens biztonsági gráf

A Intelligens biztonsági gráf valós idejű veszélyforrások elleni védelmet biztosít a Microsoft termékeiben és szolgáltatásaiban. Fejlett elemzéseket használ, amelyek nagy mennyiségű fenyegetési intelligenciát és biztonsági adatok összekapcsolását teszik lehetővé a szervezeti biztonság megerősítése érdekében. A Microsoft fejlett elemzési módszert használ: havonta több mint 450 000 000 000 hitelesítés feldolgozását, a kártevők és az adathalászat elleni 400 000 000 000 e-mailek vizsgálatát, valamint a 1 000 000 000-eszközök frissítését, így gazdagabb elemzéseket nyújthat. Ezek az elemzések segítségével a szervezet gyorsan észlelheti és reagálhat a támadásokra.

* [Intelligens biztonsági gráf](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Következő lépések
Ismerje meg a [megosztott felelősségi modellt](shared-responsibility.md) , valamint azt, hogy mely biztonsági feladatokat kezeli a Microsoft, és mely feladatokat kezeli.

A biztonság kezelésével kapcsolatos további információkért lásd: [a biztonság kezelése az Azure-ban](management.md).
