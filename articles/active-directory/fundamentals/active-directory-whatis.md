---
title: Mi az Azure Active Directory? – Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 8e0e028be6b4a507ba97f9126c791ee77c764c70
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880496"
---
# <a name="what-is-azure-active-directory"></a>Mi az Azure Active Directory?

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni, és hozzáférni a következő erőforrásokhoz:

- Külső erőforrások, például Microsoft Office 365, a Azure Portal és több ezer más SaaS-alkalmazás.

- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által fejlesztett felhőalapú alkalmazások.

Az Azure, az Azure AD és az Office 365 legfontosabb identitás-szolgáltatásainak jobb megismeréséhez használhatja a [vállalati építészek sorozatának](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) plakátok különböző Microsoft Cloudeit.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure AD-t?

Az Azure AD a következő célokra szolgál:

- **IT rendszergazdák.** Rendszergazdaként az Azure AD használatával szabályozhatja az alkalmazásaihoz és az alkalmazás-erőforrásaihoz való hozzáférést az üzleti követelmények alapján. Használhatja például az Azure AD-t a többtényezős hitelesítés megköveteléséhez a fontos szervezeti erőforrások elérésekor. Emellett az Azure AD segítségével automatizálhatja a felhasználók üzembe helyezését a meglévő Windows Server AD és a felhőalapú alkalmazások között, beleértve az Office 365-et is. Végül az Azure AD hatékony eszközöket biztosít a felhasználói identitások és hitelesítő adatok védelméhez, valamint a hozzáférési irányítási követelmények teljesítéséhez. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Alkalmazások fejlesztői.** Alkalmazás-fejlesztőként használhatja az Azure AD-t szabvány-alapú megközelítésként az egyszeri bejelentkezés (SSO) az alkalmazáshoz való hozzáadásához, amely lehetővé teszi, hogy a felhasználó már meglévő hitelesítő adataival működjön. Az Azure AD olyan API-kat is biztosít, amelyek segítségével személyre szabott alkalmazásokat hozhat létre meglévő szervezeti adatai alapján. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). További információt a [fejlesztők Azure Active Directory](../develop/index.yml)is talál.

- **Microsoft 365, Office 365, Azure vagy Dynamics CRM Online-előfizetők.** Előfizetőként már használja az Azure AD-t. A Microsoft 365, az Office 365, az Azure és a Dynamics CRM Online bérlője automatikusan egy Azure AD-bérlő. Azonnal elkezdheti az integrált felhőalapú alkalmazásokhoz való hozzáférés kezelését.

## <a name="what-are-the-azure-ad-licenses"></a>Mik az Azure AD-licencek?

A Microsoft online üzleti szolgáltatásai, például az Office 365 vagy a Microsoft Azure, az Azure AD-t igénylik a bejelentkezéshez, és segítséget nyújtanak az identitások védelméhez. Ha előfizet bármely Microsoft Online Business Service szolgáltatásra, automatikusan megkapja az Azure AD-t az összes ingyenes szolgáltatás eléréséhez.

Az Azure AD megvalósításának javítása érdekében a prémium szintű Azure Active Directory P1 vagy prémium P2 licencekre való frissítéssel is hozzáadhat fizetős funkciókat. Az Azure AD fizetős licencek a meglévő ingyenes címtárra épülnek, így önkiszolgáló, továbbfejlesztett monitorozási, biztonsági jelentéskészítési és biztonságos hozzáférést biztosíthat a mobil felhasználói számára.

>[!Note]
>A licencek díjszabási lehetőségeiért tekintse meg a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/).
>
>A P1 és a prémium P2 prémium szintű Azure Active Directory Kínában jelenleg nem támogatott. Az Azure AD díjszabásával kapcsolatos további információkért forduljon a [Azure Active Directory fórumhoz](https://azure.microsoft.com/support/community/?product=active-directory).

- **Ingyenes Azure Active Directory.** Lehetővé teszi a felhasználók és a csoportok felügyeletét, a helyszíni címtár-szinkronizálást, az alapszintű jelentéseket, az önkiszolgáló jelszó-módosítást a felhőalapú felhasználók számára, valamint az Azure, az Office 365 és számos népszerű SaaS-alkalmazás egyszeri bejelentkezését.

- **P1 prémium szintű Azure Active Directory.** Az ingyenes funkciók mellett a P1 lehetővé teszi a hibrid felhasználók számára a helyszíni és a Felhőbeli erőforrások elérését is. Emellett támogatja a speciális felügyeletet, például a dinamikus csoportokat, az önkiszolgáló csoportok felügyeletét, a Microsoft Identity Managert (helyszíni identitás-és hozzáférés-kezelési csomagot) és a felhőalapú írási visszalépési képességeket, amelyek lehetővé teszik az önkiszolgáló jelszó-visszaállítást a helyszíni felhasználók.

- **P2 prémium szintű Azure Active Directory.** Az ingyenes és a P1 funkciók mellett a P2 is [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) biztosít az alkalmazásaihoz és a kritikus fontosságú vállalati adatokhoz való kockázatalapú feltételes hozzáférés biztosításához, és [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) segíti a felderítést, korlátozhatja és figyelheti a rendszergazdákat és azok erőforrásokhoz való hozzáférését, és igény szerint igény szerint biztosíthatja az igény szerinti hozzáférést.

- **"Pay as go" szolgáltatás-licencek.** További szolgáltatás-licenceket is kaphat, például Azure Active Directory üzleti – ügyfél (B2C) szolgáltatást. A B2C segítségével identitás-és hozzáférés-kezelési megoldásokat biztosíthat az ügyfelek felé irányuló alkalmazásaihoz. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).

Az Azure-előfizetések Azure AD-hez való társításával kapcsolatos további információkért lásd [: útmutató: Azure-előfizetés társítása vagy hozzáadása a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md) , valamint a licencek felhasználókhoz való hozzárendelésével kapcsolatos további információkért lásd [: How to: assign or Remove Azure Active Directory licencek](license-users-groups.md).

## <a name="terminology"></a>Szakkifejezések

Az Azure AD és a hozzá tartozó dokumentáció jobb megismerése érdekében javasoljuk, hogy tekintse át az alábbi feltételeket.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Identitáskezelés| Olyan dolog, amely hitelesíthető lehet. Az identitás lehet felhasználónévvel és jelszóval rendelkező felhasználó. Az identitások olyan alkalmazásokat vagy más kiszolgálókat is tartalmaznak, amelyekhez titkos kulcsokon vagy tanúsítványokon keresztül lehet hitelesítést igényelni.|
|Fiók| A hozzá társított adatokkal rendelkező identitás. Identitás nélkül nem lehet fiókja.|
|Azure AD-fiók| Az Azure AD-n vagy egy másik Microsoft Cloud Service-en keresztül létrehozott identitás, például az Office 365. Az identitások az Azure AD-ben tárolódnak, és elérhetők a szervezet Cloud Service-előfizetései számára. Ezt a fiókot más néven munkahelyi vagy iskolai fióknak is nevezik.|
|Azure-előfizetés| Az Azure Cloud Services fizetésére használatos. Több előfizetéssel is rendelkezhet, és ezek egy hitelkártyához vannak társítva.|
|Azure-bérlő| Az Azure AD dedikált és megbízható példánya, amely akkor jön létre automatikusan, amikor a szervezet regisztrál egy Microsoft Cloud Service-előfizetésre, például Microsoft Azure, Microsoft Intune vagy Office 365. Az Azure-bérlő egyetlen szervezetet jelöl.|
|Egybérlős alkalmazás| A dedikált környezetben más szolgáltatásokhoz hozzáférő Azure-bérlők egyetlen bérlőnek tekintendők.|
|Több-bérlős| Azok az Azure-bérlők, amelyek más szolgáltatásokhoz férnek hozzá egy megosztott környezetben, több szervezeten belül több-bérlőnek számítanak.|
|Azure AD-címtár|Minden egyes Azure-bérlő dedikált és megbízható Azure AD-címtárral rendelkezik. Az Azure AD-címtár tartalmazza a bérlő felhasználóit, csoportjait és alkalmazásait, és a bérlői erőforrások identitás-és hozzáférés-kezelési funkcióinak végrehajtásához használatos.|
|Egyéni tartomány|Minden új Azure AD-címtárhoz tartozik egy kezdeti tartománynév, a domainname.onmicrosoft.com. A kezdeti név mellett felveheti a szervezet tartományneveit is, amelyek tartalmazzák az üzleti és a felhasználók által a szervezet erőforrásaihoz való hozzáféréshez használt neveket a listához. Az egyéni tartománynevek hozzáadásával a felhasználók számára ismerős felhasználónevek (például alain@contoso.com) hozhatók létre.|
|Fiókadminisztrátor|Ez a klasszikus előfizetés-rendszergazdai szerepkör fogalmilag egy előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáfér a [Azure Fiókközponthoz](https://account.azure.com/Subscriptions) , és lehetővé teszi a fiókban lévő összes előfizetés kezelését. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Szolgáltatás-rendszergazda|Ez a klasszikus előfizetés-rendszergazdai szerepkör lehetővé teszi az összes Azure-erőforrás kezelését, beleértve a hozzáférést is. Ez a szerepkör egyenértékű hozzáféréssel rendelkezik egy olyan felhasználóhoz, aki az előfizetés hatókörében tulajdonosi szerepkört rendelt hozzá. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tulajdonos|Ez a szerepkör segít az összes Azure-erőforrás kezelésében, beleértve a hozzáférést is. Ez a szerepkör a szerepköralapú hozzáférés-vezérlés (RBAC) nevű újabb engedélyezési rendszerre épül, amely részletes hozzáférés-kezelést biztosít az Azure-erőforrásokhoz. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globális Azure AD-rendszergazda|Ez a rendszergazdai szerepkör automatikusan hozzá lett rendelve az Azure AD-bérlőhöz. A globális rendszergazdák az Azure AD összes felügyeleti funkcióját és az Azure AD-be összevonása szolgáltatásokat (például Exchange Online, SharePoint Online és Skype vállalati online) is elvégezhetik. Több globális rendszergazda is lehet, de csak a globális rendszergazdák rendelhetnek rendszergazdai szerepköröket (beleértve más globális rendszergazdák hozzárendelését is) a felhasználók számára.<br><br>**Megjegyzés**<br>Ez a rendszergazdai szerepkör a Azure Portal globális rendszergazdája, de a Microsoft Graph API, az Azure AD Graph API és az Azure AD PowerShell nevű **vállalati rendszergazda** .<br><br>A különböző rendszergazdai szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdai szerepkör engedélyei Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Microsoft-fiók (más néven, MSA)|Személyes fiókok, amelyek hozzáférést biztosítanak a Microsoft-termékekhez és a felhőalapú szolgáltatásokhoz, például az Outlookhoz, a OneDrive, az Xbox LIVE-hoz vagy az Office 365-hoz. A Microsoft-fiók a Microsoft által futtatott Microsoft fogyasztói identitás-fiók rendszerében jön létre és tárolja.|

## <a name="which-features-work-in-azure-ad"></a>Mely funkciók működnek az Azure AD-ben?

Miután kiválasztotta az Azure AD-licencét, hozzáférhet a szervezete következő szolgáltatásainak egy részéhez vagy mindegyikéhez:

|Kategória|Leírás|
|-------|-----------|
|Alkalmazáskezelés|A Felhőbeli és helyszíni alkalmazásokat az alkalmazásproxy, az egyszeri bejelentkezés, a saját alkalmazások portál (más néven hozzáférési panel) és a szolgáltatott szoftverek (SaaS) használatával kezelheti. További információkért lásd: [biztonságos távelérés biztosítása a helyszíni alkalmazásokhoz](../manage-apps/application-proxy.md) és az [alkalmazás-felügyeleti dokumentációhoz](../manage-apps/index.yml).|
|Hitelesítés|Felügyelheti Azure Active Directory önkiszolgáló jelszó-visszaállítást, Multi-Factor Authentication, egyéni tiltott jelszavak listáját és az intelligens zárolást. További információ: [Azure ad-hitelesítés dokumentációja](../authentication/index.yml).|
|Vállalatközi (B2B)|Kezelheti vendégeit és külső partnereit, miközben felügyelheti a saját céges adatait. További információ: [Azure Active Directory B2B-dokumentáció](../b2b/index.yml).|
|Vállalat és ügyfél közötti (B2C)|Testreszabhatja és szabályozhatja, hogy a felhasználók hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik profiljaikat az alkalmazások használatakor. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).|
|Feltételes hozzáférés|A felhőalapú alkalmazásokhoz való hozzáférés kezelése. További információt az [Azure ad feltételes hozzáférési dokumentációjában](../conditional-access/index.yml)talál.|
|Azure Active Directory fejlesztők számára|Olyan alkalmazásokat hozhat létre, amelyek az összes Microsoft-identitást bejelentkeznek, és a Microsoft Graph, más Microsoft API-k vagy egyéni API-k meghívására kapnak tokeneket. További információ: [Microsoft Identity platform (Azure Active Directory for Developers)](../develop/index.yml).|
|Eszközfelügyelet|Kezelheti a felhő vagy a helyszíni eszközök hozzáférését a vállalati adataihoz. További információ: az [Azure ad-eszközkezelés dokumentációja](../devices/index.yml).|
|Tartományi szolgáltatások|Az Azure-beli virtuális gépeket tartományvezérlők használata nélkül csatlakoztathatja egy tartományhoz. További információ: [Azure ad Domain Services dokumentáció](../../active-directory-domain-services/index.yml).|
|Vállalati felhasználók|A licenc-hozzárendelések kezelése, az alkalmazások elérése és a delegált csoportok és rendszergazdai szerepkörök használatával történő beállítása. További információ: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).|
|Hibrid identitás|A Azure Active Directory Connect és a kapcsolódási állapot használatával egyetlen felhasználói identitást biztosíthat a hitelesítéshez és az engedélyezéshez minden erőforráshoz, a helytől (Felhőbeli vagy helyszíni) függetlenül. További információ: [hibrid identitás dokumentációja](../hybrid/index.yml).|
|Identitáskezelés|Szervezete személyazonosságának kezelése az alkalmazottak, az üzleti partner, a szállító, a szolgáltatás és az alkalmazás-hozzáférés-vezérlés használatával. A hozzáférési felülvizsgálatokat is elvégezheti. További információ: [Azure ad Identity irányítási dokumentáció](../governance/identity-governance-overview.md) és [Azure ad hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md).|
|Identity Protection|A szervezet identitásait érintő lehetséges sebezhetőségek észlelése, a szabályzatok konfigurálása a gyanús műveletekre való válaszadásra, majd a szükséges műveletek elvégzése a megoldáshoz. További információ: [Azure ad Identity Protection](../identity-protection/index.yml).|
|Azure-erőforrások felügyelt identitásai|Az Azure AD-ben automatikusan felügyelt identitással biztosítja az Azure-szolgáltatásokat, amely képes hitelesíteni bármely Azure AD által támogatott hitelesítési szolgáltatást, beleértve a Key Vault is. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|A szervezeten belüli hozzáférés kezelése, szabályozása és figyelése. Ez a funkció magában foglalja az Azure AD-ben és az Azure-ban, valamint más Microsoft Online Services-szolgáltatásokban, például az Office 365-ben vagy az Intune-ban További információ: [Azure ad Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a környezet biztonsági és használati szokásaiba. További információ: [Azure Active Directory jelentések és figyelés](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Következő lépések

- [Regisztráljon prémium szintű Azure Active Directory](active-directory-get-started-premium.md)

- [Azure-előfizetés hozzárendelése a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)

- [Hozzáférés Azure Active Directory és új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- [prémium szintű Azure Active Directory P2 szolgáltatás üzembe helyezési ellenőrzőlistája](active-directory-deployment-checklist-p2.md)
