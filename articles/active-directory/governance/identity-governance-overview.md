---
title: Az Azure AD-identitáskezelést |} A Microsoft Docs
description: Az Azure AD-identitáskezelést lehetővé teszi, hogy azt biztonság és az alkalmazottak a termelékenység a megfelelő folyamatokat és látható-e szükség a szervezet elosztása érdekében.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 304399905e9dfd37557a342248c829c1f940452b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167581"
---
# <a name="what-is-azure-ad-identity-governance"></a>Mi az Azure ad-ben identitáskezelést?

Az Azure Active Directory (Azure AD) identitáskezelést lehetővé teszi, hogy azt biztonság és az alkalmazottak a termelékenység a megfelelő folyamatokat és látható-e szükség a szervezet elosztása érdekében. Győződjön meg arról, hogy a megfelelő felhasználók megfelelő hozzáférése legyen a megfelelő erőforrásokat, és lehetővé teszi, így megvédheti, monitorozhatja és naplózhatja a kritikus fontosságú eszközeit – miközben termelékenységének lehetőségeket biztosít.  

Identitáskezelést lehetővé teheti a szervezet alkalmazottai, üzleti partnerek és szállítók, és a szolgáltatások és alkalmazások között a következőket:

- Az identitás-életciklus szabályozása
- Életciklus hozzáférés szabályozása
- Biztonságos felügyelet

Pontosabban a célja, hogy megkönnyíti a szervezetek számára, oldja meg ezeket a négy fő kérdéseket:

- Mely felhasználók rendelkezzenek mely erőforrásokhoz való hozzáférést?
- Mivel foglalkoznak az számukra a hozzáférést?
- Vannak-e a hozzáférés-kezelés hatékony szervezeti vezérlők?
- Ellenőrök ellenőrizheti, hogy a vezérlők működik?

## <a name="identity-lifecycle"></a>Identitás-életciklus

Identitáskezelést segítségével a szervezetek közötti egyensúly elérése *termelékenység* -milyen gyorsan egy személy férhet erőforrások szükségük van, például ha a szervezetem csatlakoznak? És *biztonsági* – hogyan kell hozzáférésüket időbeli változásait, például a változások miatt az, hogy a címzett állapota?  Identitás-életciklus-felügyelet identitáskezelést alapját, és hatékony szabályzását nagy méretekben igényel modernizálhatja alkalmazások identitás-életciklus felügyeleti infrastruktúrát.

A legtöbb szervezet számára az alkalmazottak identitás-életciklus kötődik leképezése az, hogy a felhasználó egy HCM (Emberierőforrás-kezelési) rendszerben.  Az Azure AD Premium automatikusan is kezeli felhasználói identitások WORKDAY az Active Directory és az Azure Active Directory, a jelölt felhasználók leírtak szerint a [Workday bejövő provisioning (előzetes verzió) oktatóanyag](../saas-apps/workday-inbound-tutorial.md).  Az Azure AD Premium is tartalmaz [Microsoft Identity Manager](/microsoft-identity-manager/), amely rekordok importálhatja a helyszíni HCM rendszerekből, például SAP, Oracle eBusiness, és az Oracle PeopleSoft.

Egyre több forgatókönyvek igényelnek, a szervezeten kívüli személyekkel való együttműködést. [Az Azure AD B2B](/azure/active-directory/b2b/) együttműködést lehetővé teszi, hogy a munkahelyi alkalmazások és szolgáltatások biztonságos megosztását vendégfelhasználók és minden olyan szervezet, miközben a saját vállalati adatokat a külső partnerekkel.

## <a name="access-lifecycle"></a>Access-életciklus

Egy folyamatot, hogy milyen eredetileg kiosztott egy felhasználó a felhasználói identitás létrehozásakor túli hozzáférés kezelése a cégeknek szükségük van.  Emellett vállalati szervezetek képeseknek kell lenniük hatékony a skálázás fejlesztése és hozzáférési szabályzatot, és folyamatosan vezérlők kényszerítésére.

Általában az informatikai delegáltak eléréséhez jóváhagyási döntéseket hozhat, üzleti döntéshozók számára.  Továbbá informatikai a felhasználók maguk is magában foglalhat.  Például a vállalat marketing alkalmazás Európai bizalmas felhasználói adatokhoz hozzáférő felhasználók kell tudni, hogy a cég szabályzatait. Előfordulhat, hogy vendégfelhasználók nélkül is működőképesek maradhatnak a kezelési követelmények, amelyhez meghívásuk olyan szervezeti adatokat.

Szervezetek számára a hozzáférés életciklus folyamat technológiákkal például automatizálható [dinamikus csoportok](../users-groups-roles/groups-dynamic-membership.md), összekapcsolt az való [SaaS-alkalmazások](../saas-apps/tutorial-list.md) vagy [alkalmazások integrált SCIM-mel](../manage-apps/use-scim-to-provision-users-and-groups.md).  Szervezetek is meghatározhatja, amely [vendég felhasználók férhetnek hozzá a helyszíni alkalmazások](../b2b/hybrid-cloud-to-on-premises.md).  Ezek a hozzáférési jogok is, majd rendszeresen ismétlődő használata vizsgálni [az Azure AD hozzáférési felülvizsgálatok](access-reviews-overview.md).

Amikor egy felhasználó megpróbál hozzáférni az alkalmazásokhoz, az Azure ad-ben kényszerít [feltételes hozzáférési](/azure/active-directory/conditional-access/) házirendeket. Például a feltételes hozzáférési szabályzatok lehetnek megjelenítése egy [használati feltételeket tartalmazó fájl](active-directory-tou.md) és [biztosítása a felhasználó hozzájárult ezeket a kifejezéseket](../conditional-access/require-tou.md) előtt nem tudnak hozzáférni az alkalmazáshoz.

## <a name="privileged-access-lifecycle"></a>Emelt szintű hozzáférés életciklusa

Hagyományosan emelt szintű hozzáférés a fentiekben leírtak más gyártók szerint külön az identitáskezelést. Azonban a Microsoft azt gondolja, hogy emelt szintű hozzáférést szabályozó identitáskezelést--különösen azokat rendszergazdai jogok okozhat nyújtva a cég társított visszaélés megadott a lehetséges kulcsfontosságú része. Az alkalmazottak, a szállítók és az alvállalkozók, a rendszergazdai jogosultságokkal kell az irányadó.

Az Azure AD Privileged Identity Management (PIM) szabott több eszközt biztosít a hozzáférés biztosítása a rights erőforrások, az Azure ad-ben, az Azure és más Microsoft Online Services.  Az igény szerinti hozzáférés és a szerepkör módosítása a multi-factor authentication és a feltételes hozzáférés mellett az Azure AD PIM által biztosított képességek riasztási biztosít átfogó készlete cégirányítási vezérlők segítségével biztonságos (directory, a vállalati erőforrásokhoz Az Office 365 és Azure-erőforrások szerepköreihez). Hozzáférés más formáinak a szervezetek használhatják a hozzáférési felülvizsgálatok az összes felhasználó számára az ismétlődő hozzáférés tanúsításra konfigurálása a rendszergazdai szerepköröket.

## <a name="getting-started"></a>Első lépések

Nem áll fenn tökéletes megoldás vagy javaslat, minden ügyfél számára, miközben a következő konfigurációk biztosítanak egy útmutató, amellyel a Microsoft azt javasolja, annak biztosítására, egy további biztonságosan és hatékonyan munkaerő alapkonfiguráció szabályzatokat.

- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Emelt szintű hozzáférés biztonsága](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

- [Mi az a hozzáférési felülvizsgálat?](access-reviews-overview.md)
- [Felhasználói hozzáférés kezelése hozzáférési felülvizsgálatokkal](manage-user-access-with-access-reviews.md)
- [Vendéghozzáférés kezelése hozzáférési felülvizsgálatokkal](manage-guest-access-with-access-reviews.md)
- [A directory-szerepkörök hozzáférési felülvizsgálat indítása](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Használati feltételek

- [Mire használhatom a Használati feltételek funkciót?](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged identity management

- [Mi az Azure AD PIM?](../privileged-identity-management/pim-configure.md)
