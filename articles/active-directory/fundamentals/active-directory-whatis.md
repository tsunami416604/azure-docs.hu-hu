---
title: Mi az az Azure Active Directory? – Az azure Active Directory |} A Microsoft Docs
description: Áttekintő és elméleti információk a Azure Active Directoryről, beleértve a terminológiát és a rendelkezésre álló licenceket, valamint a kapcsolódó szolgáltatások listáját, amelyekben további információk találhatók.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: eda457747a6a3e374e7fdea571f64446e15ded21
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774173"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottak bejelentkezési és hozzáférési erőforrásainak elérésében:

- Külső erőforrások, például a Microsoft Office 365, az Azure Portalon, és több ezer más SaaS-alkalmazásokhoz.

- Belső erőforrásokat, például a vállalati hálózat és az intranetes, és olyan felhőalkalmazások, a saját szervezet által fejlesztett alkalmazások.

Használhatja a különböző [Microsoft Cloud for Enterprise Architects sorozat](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) poszterek jobb megértése érdekében az Azure-ban, az Azure AD identity alapszolgáltatások és az Office 365.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure ad-ben?

Az Azure AD célközönsége:

- **IT rendszergazdák.** INFORMATIKAI rendszergazdaként az Azure AD segítségével szabályozhatja a hozzáférést az alkalmazások és az alkalmazás-erőforrások, az üzleti követelmények alapján. Az Azure AD használatával például többtényezős hitelesítés megkövetelése, ha fontos a szervezeti erőforrások elérése. Emellett az Azure AD segítségével között a meglévő Windows Server AD és a felhőalapú alkalmazások, beleértve az Office 365 felhasználók létrehozásának automatizálása. Végül az Azure AD biztosítja a hatékony eszközöket automatikusan a felhasználói identitások és a hitelesítő adatok védelme érdekében, illetve a hozzáférési cégirányítási követelmények kielégítése céljából. Első lépésként regisztráljon egy [30 napos ingyenes Azure Active Directory Premium próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Alkalmazásfejlesztőknek.** Alkalmazás-fejlesztőként használhatja az Azure AD-t szabvány-alapú megközelítésként az egyszeri bejelentkezés (SSO) az alkalmazáshoz való hozzáadásához, amely lehetővé teszi, hogy a felhasználó már meglévő hitelesítő adataival működjön. Az Azure AD olyan API-kat is biztosít, amelyek segítségével személyre szabott alkalmazásokat hozhat létre meglévő szervezeti adatai alapján. Első lépésként regisztráljon egy [30 napos ingyenes Azure Active Directory Premium próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/). További információ is megtekinthető [Azure Active Directory fejlesztők számára](../develop/index.yml).

- **A Microsoft 365, Office 365, Azure vagy a Dynamics CRM Online-előfizetők számára.** Előfizetőként már használja az Azure ad-ben. Minden Microsoft 365, az Office 365, Azure és a Dynamics CRM Online bérlő automatikusan az Azure AD-bérlő. Azonnal elkezdheti az integrált felhőbeli alkalmazásokhoz való hozzáférés kezelésére.

## <a name="what-are-the-azure-ad-licenses"></a>Mik az Azure AD-licenceket?

Microsoft Online üzleti szolgáltatásokat, például Office 365 vagy a Microsoft Azure, Azure AD bejelentkezési és az identity protection segítségével igényelnek. Ha előfizet bármely Microsoft Online Business Service szolgáltatásra, automatikusan megkapja az Azure AD-t az összes ingyenes szolgáltatás eléréséhez.

Az Azure AD megvalósításának javítása érdekében a prémium szintű Azure Active Directory P1 vagy prémium P2 licencekre való frissítéssel is hozzáadhat fizetős funkciókat. Az Azure AD fizetős licencek a meglévő ingyenes címtárra épülnek, így önkiszolgáló, továbbfejlesztett monitorozási, biztonsági jelentéskészítési és biztonságos hozzáférést biztosíthat a mobil felhasználói számára.

>[!Note]
>Az ezen licencek díjszabási lehetőségek, lásd: [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).
>
>A P1 és a prémium P2 prémium szintű Azure Active Directory Kínában jelenleg nem támogatott. Az Azure AD díjszabásával kapcsolatos további információkért forduljon a [Azure Active Directory fórumhoz](https://azure.microsoft.com/support/community/?product=active-directory).

- **Az Azure Active Directory ingyenes.** Lehetővé teszi a felhasználók és a csoportok felügyeletét, a helyszíni címtár-szinkronizálást, az alapszintű jelentéseket, az önkiszolgáló jelszó-módosítást a felhőalapú felhasználók számára, valamint az Azure, az Office 365 és számos népszerű SaaS-alkalmazás egyszeri bejelentkezését.

- **P1 prémium szintű Azure Active Directory.** Az ingyenes és alapszintű funkciók mellett P1 is lehetővé teszi, hogy a hibrid felhasználók elérésére mind a helyszíni és felhőbeli erőforrásokhoz. Emellett támogatja a speciális felügyeleti, például a dinamikus csoportok, önkiszolgáló csoportfelügyelet, a Microsoft Identity Manager (egy a helyszíni identitás- és hozzáférés felügyeleti csomag) és a felhőalapú késleltetve visszaírt képességek, amelyek lehetővé teszik önkiszolgáló jelszó-visszaállítást a a helyszíni felhasználók.

- **P2 prémium szintű Azure Active Directory.** Az ingyenes, alapszintű és P1 funkciókon kívül a P2 is kínál [Azure Active Directory Identity Protection](../identity-protection/enable.md) az alkalmazásokhoz és a kritikus fontosságú vállalati adatokhoz való kockázatalapú feltételes hozzáférés biztosításához, és [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) , amely segít a felderítésben , korlátozhatja és figyelheti a rendszergazdákat és azok erőforrásokhoz való hozzáférését, és igény szerint igény szerinti hozzáférést biztosíthat.

- **"Használatalapú fizetés" funkció licenceket.** A szolgáltatás további licenceket, például az Azure Active Directory üzleti és ügyfél közötti (B2C) is kaphat. B2C segítségével biztosíthat identitás- és hozzáférés-kezelés az ügyfelek által használt alkalmazások számára. További információkért lásd: [Azure Active Directory B2C dokumentációja](../../active-directory-b2c/index.yml).

Az Azure-előfizetés Azure ad-hez való társításával kapcsolatos további információkért [lásd: How to: Azure-előfizetés társítása vagy hozzáadása a](active-directory-how-subscriptions-associated-directory.md) Azure Active Directoryhoz és a licencek felhasználókhoz való hozzárendelésével kapcsolatos további [információkért lásd: How to: Azure Active Directory licencek](license-users-groups.md)kiosztása vagy eltávolítása.

## <a name="terminology"></a>Terminológia

Az Azure AD és a hozzá tartozó dokumentáció jobb megismerése érdekében javasoljuk, hogy tekintse át az alábbi feltételeket.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Identitás| Olyan dolog, amely hitelesíthető lehet. Az identitás lehet felhasználónévvel és jelszóval rendelkező felhasználó. Az identitások olyan alkalmazásokat vagy más kiszolgálókat is tartalmaznak, amelyekhez titkos kulcsokon vagy tanúsítványokon keresztül lehet hitelesítést igényelni.|
|Fiók| A hozzá társított adatokkal rendelkező identitás. Identitás nélkül nem lehet fiókja.|
|Azure AD-fiók| Az identitás létrehozása az Azure AD-n keresztül, vagy egy másik Microsoft felhőszolgáltatáshoz, például az Office 365. Identitások a következők: Azure AD-ben tárolt és felhőszolgáltatás-előfizetés a szervezet számára is elérhető. Ez a fiók néven is ismert munkahelyi vagy iskolai fiókkal.|
|Azure-előfizetés| Használja az Azure cloud services kell fizetnie. Több előfizetéssel is rendelkezik, és vannak kapcsolva hitelkártya.|
|Azure-bérlő| Egy dedikált és megbízható Azure AD példányát, amely automatikusan létrejön, amikor egy szervezet előfizet a Microsoft felhőalapú szolgáltatás előfizetésre, például a Microsoft Azure, Microsoft Intune vagy Office 365-höz. Az Azure-bérlő egyetlen szervezet jelöli.|
|Egybérlős alkalmazás| Az Azure a bérlők, dedikált környezetben más szolgáltatások eléréséhez egybérlős minősülnek.|
|Több-bérlős| Az Azure bérlők számára, hogy megosztott környezetben, más szolgáltatások eléréséhez több vállalat között, több-bérlős minősülnek.|
|Azure AD-címtár|Minden Azure-bérlő rendelkezik egy dedikált és megbízható Azure AD-címtárban. Az Azure AD-címtár tartalmazza a bérlő felhasználók, csoportok és alkalmazások, és hajtsa végre az identitás és hozzáférés-kezelési funkciók bérlői erőforrások szolgál.|
|Egyéni tartomány|Minden új Azure AD-címtár is tartalmaz egy kezdeti tartománynevet domainname.onmicrosoft.com. Emellett a kezdeti neve, azt is megteheti neveket, többek között a nevek, üzleti és a felhasználók, illetve használhat a szervezet erőforrásaihoz, a listához eléréséhez használja a szervezet tartományához. Egyéni tartománynevek hozzáadása segítségével hozhat létre, amely számára is felismerhetők a felhasználók számára, például felhasználóneveket alain@contoso.com.|
|Fiókadminisztrátor|A hagyományos előfizetés-rendszergazda szerepkör elméleti szinten az előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáféréssel rendelkezik a [Azure Account Center](https://account.azure.com/Subscriptions) , és lehetővé teszi, hogy a fiók az összes előfizetés kezelése. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Szolgáltatás-rendszergazda|A hagyományos előfizetés-rendszergazda szerepkör lehetővé teszi, hogy az összes Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör rendelkezik egy előfizetésre a tulajdonosi szerepkörrel felruházott felhasználó a megfelelő hozzáférést. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tulajdonos|Ez a szerepkör segítségével minden Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör egy újabb engedélyezési rendszer nevű szerepkör alapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások részletes hozzáférés-vezérlést biztosító épül. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globális Azure AD-rendszergazda|A rendszergazdai szerepkör van automatikusan hozzárendelt, akinél létrehozni az Azure AD-bérlővel. A globális rendszergazdák az Azure AD összes felügyeleti funkcióját és az Azure AD-be összevonása szolgáltatásokat (például Exchange Online, SharePoint Online és Skype vállalati online) is elvégezhetik. Több globális rendszergazda is használhat, de csak a globális rendszergazdák (beleértve a más globális rendszergazdákat hozzárendelése) rendszergazdai szerepkörök hozzárendelése a felhasználókhoz.<br><br>**Megjegyzés**<br>A rendszergazda szerepkör nevezzük a globális rendszergazda az Azure Portalon, de nevezzük **vállalati rendszergazda** a Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell.<br><br>Különböző rendszergazdai szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md).|
|Microsoft-fiók (más néven, MSA)|Személyes fiók, amely biztosít hozzáférést a végfelhasználóra irányuló Microsoft termékekhez, és a felhőalapú szolgáltatások, például az Outlook, OneDrive, Xbox LIVE- vagy Office 365-höz. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja.|

## <a name="which-features-work-in-azure-ad"></a>Mely funkciók működnek az Azure AD-ben?

Miután kiválasztotta az Azure AD-licencét, hozzáférhet a szervezete következő szolgáltatásainak egy részéhez vagy mindegyikéhez:

|Category|Leírás|
|-------|-----------|
|Alkalmazáskezelés|Az alkalmazásproxyval, felhőbeli és helyszíni alkalmazások felügyelete egyszeri bejelentkezés, a saját alkalmazások portál (más néven a hozzáférési panel) és a szoftver-szoftverszolgáltatások (SaaS) alkalmazásokat. További információkért lásd: [biztonságos távoli elérést biztosíthat a helyszíni alkalmazások](../manage-apps/application-proxy.md) és [Alkalmazáskezelés dokumentáció](../manage-apps/index.yml).|
|Hitelesítés|Kezelheti az Azure Active Directory önkiszolgáló jelszó-visszaállítási, többtényezős hitelesítés, egyéni letiltott jelszavak lista és intelligens zárolás. További információkért lásd: [dokumentáció az Azure AD-hitelesítés](../authentication/index.yml).|
|Vállalatközi (B2B)|A vendég felhasználók és a külső partnerek kezelése irányítást a saját vállalati adatokat. További információkért lásd: [Azure Active Directory B2B-dokumentáció](../b2b/index.yml).|
|Üzleti és ügyfél közötti (B2C)|Testre szabhatja és vezérlési felhasználói regisztrálás módja, jelentkezzen be, és profiljuk kezelését, az alkalmazások használatakor. További információkért lásd: [Azure Active Directory B2C dokumentációja](../../active-directory-b2c/index.yml).|
|Feltételes hozzáférés|A felhőbeli alkalmazásokhoz való hozzáférés kezelése. További információkért lásd: [Azure AD feltételes hozzáférés dokumentációjának](../conditional-access/index.yml).|
|Azure Active Directory fejlesztők számára|Jelentkezzen be az összes Microsoft-identitások, a Microsoft Graph, más Microsoft APIs vagy egyéni API-k hívásához tokenekhez alkalmazásokat készíthet. További információkért lásd: [Microsoft identitásplatformja (az Azure vonatkozó Active Directory fejlesztők számára)](../develop/index.yml).|
|Eszközfelügyelet|Kezelheti, hogyan a felhőbeli vagy helyszíni eszközök vállalati adatok eléréséhez. További információkért lásd: [Azure AD Device Management dokumentációja](../devices/index.yml).|
|Tartományi szolgáltatások|Azure virtuális gépek tartományhoz csatlakoztatására tartományvezérlő nélkül. További információkért lásd: [az Azure AD Domain Services – dokumentáció](../../active-directory-domain-services/index.yml).|
|Vállalati felhasználók|A licenc-hozzárendelés, az alkalmazásokhoz való hozzáférés kezelése, és állítsa be a delegáltakat, csoportok és rendszergazdai szerepkörök használatával. További információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).|
|Hibrid identitás|Azure Active Directory Connect és a Connect Health használatával egyetlen felhasználói identitást biztosíthat a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül helyétől (felhőbeli vagy helyszíni). További információkért lásd: [hibrid identitáskezelési dokumentáció](../hybrid/index.yml).|
|Identitáskezelés|A szervezet identitás alkalmazott, üzleti partner, szállító, szolgáltatás és alkalmazás-hozzáférés-vezérlés kezelése. A hozzáférési felülvizsgálatok is elvégezheti. További információkért lásd: [az Azure AD identity cégirányítási dokumentáció](../governance/identity-governance-overview.md) és [az Azure AD hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md).|
|Identitásvédelem|Észleli a potenciális biztonsági réseket, hogy ez hatással lenne a szervezet identitásait, válaszolni a gyanús tevékenységeket szabályzatok konfigurálását és majd hajtsa végre a megfelelő műveletet a problémák megoldásához. További információkért lásd: [Azure AD Identity Protection](../identity-protection/index.yml).|
|Azure-erőforrások felügyelt identitásai|Az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure ad-ben, amely bármely Azure AD által támogatott hitelesítési szolgáltatás, beleértve a Key Vault hitelesítheti. További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../managed-identities-azure-resources/overview.md).|
|Privileged identity management (PIM)|Kezelheti, irányíthatja, és figyelheti a hozzáférést a szervezeten belül. Ez a funkció magában foglalja az Azure AD-ben és az Azure-ban, valamint más Microsoft Online Services-szolgáltatásokban, például az Office 365-ben vagy az Intune-ban További információkért lásd: [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a biztonsági és használati minták a környezetben. További információkért lásd: [Azure Active Directory-jelentések és figyelés](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>További lépések

- [Regisztráljon az Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Az Azure Active Directory Azure-előfizetés társítása](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory eléréséhez és a egy új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- [Az Azure Active Directory Premium P2 a szolgáltatás telepítési ellenőrzőlista](active-directory-deployment-checklist-p2.md)
