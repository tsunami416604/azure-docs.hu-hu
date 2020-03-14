---
title: Mi az az Azure Active Directory? – Az azure Active Directory |} A Microsoft Docs
description: Áttekintő és elméleti információk a Azure Active Directoryről, beleértve a terminológiát és a rendelkezésre álló licenceket, valamint a kapcsolódó szolgáltatások listáját, amelyekben további információk találhatók.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd7b412e99526935738c2494d31a16fded7101a6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240785"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?

Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni, és hozzáférni a következő erőforrásokhoz:

- Külső erőforrások, például a Microsoft Office 365, az Azure Portalon, és több ezer más SaaS-alkalmazásokhoz.

- Belső erőforrásokat, például a vállalati hálózat és az intranetes, és olyan felhőalkalmazások, a saját szervezet által fejlesztett alkalmazások.

Az Azure, az Azure AD és az Office 365 legfontosabb identitás-szolgáltatásainak jobb megismeréséhez használhatja a [vállalati építészek sorozatának](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) plakátok különböző Microsoft Cloudeit.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure ad-ben?

Az Azure AD célközönsége:

- **IT rendszergazdák.** INFORMATIKAI rendszergazdaként az Azure AD segítségével szabályozhatja a hozzáférést az alkalmazások és az alkalmazás-erőforrások, az üzleti követelmények alapján. Az Azure AD használatával például többtényezős hitelesítés megkövetelése, ha fontos a szervezeti erőforrások elérése. Emellett az Azure AD segítségével között a meglévő Windows Server AD és a felhőalapú alkalmazások, beleértve az Office 365 felhasználók létrehozásának automatizálása. Végül az Azure AD biztosítja a hatékony eszközöket automatikusan a felhasználói identitások és a hitelesítő adatok védelme érdekében, illetve a hozzáférési cégirányítási követelmények kielégítése céljából. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Alkalmazások fejlesztői.** Alkalmazás-fejlesztőként használhatja az Azure AD-t szabvány-alapú megközelítésként az egyszeri bejelentkezés (SSO) az alkalmazáshoz való hozzáadásához, amely lehetővé teszi, hogy a felhasználó már meglévő hitelesítő adataival működjön. Az Azure AD olyan API-kat is biztosít, amelyek segítségével személyre szabott alkalmazásokat hozhat létre meglévő szervezeti adatai alapján. Első lépésként regisztráljon egy [30 napos ingyenes prémium szintű Azure Active Directory próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). További információt a [fejlesztők Azure Active Directory](../develop/index.yml)is talál.

- **Microsoft 365, Office 365, Azure vagy Dynamics CRM Online-előfizetők.** Előfizetőként már használja az Azure ad-ben. Minden Microsoft 365, az Office 365, Azure és a Dynamics CRM Online bérlő automatikusan az Azure AD-bérlő. Azonnal elkezdheti az integrált felhőbeli alkalmazásokhoz való hozzáférés kezelésére.

## <a name="what-are-the-azure-ad-licenses"></a>Mik az Azure AD-licenceket?

Microsoft Online üzleti szolgáltatásokat, például Office 365 vagy a Microsoft Azure, Azure AD bejelentkezési és az identity protection segítségével igényelnek. Ha előfizet bármely Microsoft Online Business Service szolgáltatásra, automatikusan megkapja az Azure AD-t az összes ingyenes szolgáltatás eléréséhez.

Az Azure AD megvalósításának javítása érdekében a prémium szintű Azure Active Directory P1 vagy prémium P2 licencekre való frissítéssel is hozzáadhat fizetős funkciókat. Az Azure AD fizetős licencek a meglévő ingyenes címtárra épülnek, így önkiszolgáló, továbbfejlesztett monitorozási, biztonsági jelentéskészítési és biztonságos hozzáférést biztosíthat a mobil felhasználói számára.

>[!Note]
>A licencek díjszabási lehetőségeiért tekintse meg a [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/).
>
>A P1 és a prémium P2 prémium szintű Azure Active Directory Kínában jelenleg nem támogatott. Az Azure AD díjszabásával kapcsolatos további információkért forduljon a [Azure Active Directory fórumhoz](https://azure.microsoft.com/support/community/?product=active-directory).

- **ingyenes Azure Active Directory.** Lehetővé teszi a felhasználók és a csoportok felügyeletét, a helyszíni címtár-szinkronizálást, az alapszintű jelentéseket, az önkiszolgáló jelszó-módosítást a felhőalapú felhasználók számára, valamint az Azure, az Office 365 és számos népszerű SaaS-alkalmazás egyszeri bejelentkezését.

- **P1 prémium szintű Azure Active Directory.** Az ingyenes funkciók mellett a P1 lehetővé teszi a hibrid felhasználók számára a helyszíni és a Felhőbeli erőforrások elérését is. Emellett támogatja a speciális felügyeleti, például a dinamikus csoportok, önkiszolgáló csoportfelügyelet, a Microsoft Identity Manager (egy a helyszíni identitás- és hozzáférés felügyeleti csomag) és a felhőalapú késleltetve visszaírt képességek, amelyek lehetővé teszik önkiszolgáló jelszó-visszaállítást a a helyszíni felhasználók.

- **P2 prémium szintű Azure Active Directory.** Az ingyenes és a P1 funkciókon kívül a P2 olyan [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) is biztosít, amelyek segítenek az alkalmazásokhoz és a kritikus fontosságú vállalati adatokhoz való kockázatalapú feltételes hozzáférés biztosításában, valamint [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) a rendszergazdák felderítésére, korlátozására és figyelésére, valamint az erőforrásokhoz való hozzáférésre, valamint az igény szerinti hozzáférés biztosítására.

- **"Pay as go" szolgáltatás-licencek.** A szolgáltatás további licenceket, például az Azure Active Directory üzleti és ügyfél közötti (B2C) is kaphat. B2C segítségével biztosíthat identitás- és hozzáférés-kezelés az ügyfelek által használt alkalmazások számára. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).

Az Azure-előfizetések Azure AD-hez való társításával kapcsolatos további információkért lásd: útmutató: az Azure- [előfizetés társítása vagy hozzáadása a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md) , valamint a licencek felhasználókhoz való hozzárendelésével kapcsolatos további információkért lásd [: útmutató: Azure Active Directory-licencek hozzárendelése vagy eltávolítása](license-users-groups.md).

## <a name="terminology"></a>Terminológia

Az Azure AD és a hozzá tartozó dokumentáció jobb megismerése érdekében javasoljuk, hogy tekintse át az alábbi feltételeket.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Identitás| Olyan dolog, amely hitelesíthető lehet. Az identitás lehet felhasználónévvel és jelszóval rendelkező felhasználó. Az identitások olyan alkalmazásokat vagy más kiszolgálókat is tartalmaznak, amelyekhez titkos kulcsokon vagy tanúsítványokon keresztül lehet hitelesítést igényelni.|
|Fiók| A hozzá társított adatokkal rendelkező identitás. Identitás nélkül nem lehet fiókja.|
|Azure AD-fiók| Az identitás létrehozása az Azure AD-n keresztül, vagy egy másik Microsoft felhőszolgáltatáshoz, például az Office 365. Identitások a következők: Azure AD-ben tárolt és felhőszolgáltatás-előfizetés a szervezet számára is elérhető. Ez a fiók néven is ismert munkahelyi vagy iskolai fiókkal.|
|Azure-előfizetés| Használja az Azure cloud services kell fizetnie. Több előfizetéssel is rendelkezik, és vannak kapcsolva hitelkártya.|
|Azure-bérlő| Egy dedikált és megbízható Azure AD példányát, amely automatikusan létrejön, amikor egy szervezet előfizet a Microsoft felhőalapú szolgáltatás előfizetésre, például a Microsoft Azure, Microsoft Intune vagy Office 365-höz. Az Azure-bérlő egyetlen szervezetet jelöl.|
|Egybérlős alkalmazás| Az Azure a bérlők, dedikált környezetben más szolgáltatások eléréséhez egybérlős minősülnek.|
|Több-bérlős| Az Azure bérlők számára, hogy megosztott környezetben, más szolgáltatások eléréséhez több vállalat között, több-bérlős minősülnek.|
|Azure AD-címtár|Minden Azure-bérlő rendelkezik egy dedikált és megbízható Azure AD-címtárban. Az Azure AD-címtár tartalmazza a bérlő felhasználók, csoportok és alkalmazások, és hajtsa végre az identitás és hozzáférés-kezelési funkciók bérlői erőforrások szolgál.|
|Egyéni tartomány|Minden új Azure AD-címtár is tartalmaz egy kezdeti tartománynevet domainname.onmicrosoft.com. Emellett a kezdeti neve, azt is megteheti neveket, többek között a nevek, üzleti és a felhasználók, illetve használhat a szervezet erőforrásaihoz, a listához eléréséhez használja a szervezet tartományához. Az egyéni tartománynevek hozzáadásával a felhasználók számára ismerős felhasználónevek (például alain@contoso.com) hozhatók létre.|
|Fiókadminisztrátor|A hagyományos előfizetés-rendszergazda szerepkör elméleti szinten az előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáfér a [Azure Fiókközponthoz](https://account.azure.com/Subscriptions) , és lehetővé teszi a fiókban lévő összes előfizetés kezelését. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Szolgáltatás-rendszergazda|A hagyományos előfizetés-rendszergazda szerepkör lehetővé teszi, hogy az összes Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör rendelkezik egy előfizetésre a tulajdonosi szerepkörrel felruházott felhasználó a megfelelő hozzáférést. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tulajdonos|Ez a szerepkör segítségével minden Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör egy újabb engedélyezési rendszer nevű szerepkör alapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások részletes hozzáférés-vezérlést biztosító épül. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globális Azure AD-rendszergazda|A rendszergazdai szerepkör van automatikusan hozzárendelt, akinél létrehozni az Azure AD-bérlővel. A globális rendszergazdák az Azure AD összes felügyeleti funkcióját és az Azure AD-be összevonása szolgáltatásokat (például Exchange Online, SharePoint Online és Skype vállalati online) is elvégezhetik. Több globális rendszergazda is használhat, de csak a globális rendszergazdák (beleértve a más globális rendszergazdákat hozzárendelése) rendszergazdai szerepkörök hozzárendelése a felhasználókhoz.<br><br>**Megjegyzés**<br>Ezt a rendszergazdai szerepkört globális rendszergazdaként nevezik a Azure Portalban, de a Microsoft Graph API-ban és az Azure AD PowerShellben a **vállalati rendszergazda** néven is ismert.<br><br>A különböző rendszergazdai szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdai szerepkör engedélyei Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Microsoft-fiók (más néven, MSA)|Személyes fiók, amely biztosít hozzáférést a végfelhasználóra irányuló Microsoft termékekhez, és a felhőalapú szolgáltatások, például az Outlook, OneDrive, Xbox LIVE- vagy Office 365-höz. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja.|

## <a name="which-features-work-in-azure-ad"></a>Mely funkciók működnek az Azure AD-ben?

Miután kiválasztotta az Azure AD-licencét, hozzáférhet a szervezete következő szolgáltatásainak egy részéhez vagy mindegyikéhez:

|Kategória|Leírás|
|-------|-----------|
|Alkalmazáskezelés|Az alkalmazásproxyval, felhőbeli és helyszíni alkalmazások felügyelete egyszeri bejelentkezés, a saját alkalmazások portál (más néven a hozzáférési panel) és a szoftver-szoftverszolgáltatások (SaaS) alkalmazásokat. További információkért lásd: [biztonságos távelérés biztosítása a helyszíni alkalmazásokhoz](../manage-apps/application-proxy.md) és az [alkalmazás-felügyeleti dokumentációhoz](../manage-apps/index.yml).|
|Authentication|Kezelheti az Azure Active Directory önkiszolgáló jelszó-visszaállítási, többtényezős hitelesítés, egyéni letiltott jelszavak lista és intelligens zárolás. További információ: [Azure ad-hitelesítés dokumentációja](../authentication/index.yml).|
|Vállalatközi (B2B)|A vendég felhasználók és a külső partnerek kezelése irányítást a saját vállalati adatokat. További információ: [Azure Active Directory B2B-dokumentáció](../b2b/index.yml).|
|Vállalat és ügyfél közötti (B2C)|Testre szabhatja és vezérlési felhasználói regisztrálás módja, jelentkezzen be, és profiljuk kezelését, az alkalmazások használatakor. További információ: [Azure Active Directory B2C dokumentáció](../../active-directory-b2c/index.yml).|
|Feltételes hozzáférés|A felhőbeli alkalmazásokhoz való hozzáférés kezelése. További információt az [Azure ad feltételes hozzáférési dokumentációjában](../conditional-access/index.yml)talál.|
|Azure Active Directory fejlesztők számára|Jelentkezzen be az összes Microsoft-identitások, a Microsoft Graph, más Microsoft APIs vagy egyéni API-k hívásához tokenekhez alkalmazásokat készíthet. További információ: [Microsoft Identity platform (Azure Active Directory for Developers)](../develop/index.yml).|
|Eszközfelügyelet|Kezelheti, hogyan a felhőbeli vagy helyszíni eszközök vállalati adatok eléréséhez. További információ: az [Azure ad-eszközkezelés dokumentációja](../devices/index.yml).|
|Tartományi szolgáltatások|Azure virtuális gépek tartományhoz csatlakoztatására tartományvezérlő nélkül. További információ: [Azure ad Domain Services dokumentáció](../../active-directory-domain-services/index.yml).|
|Vállalati felhasználók|A licenc-hozzárendelés, az alkalmazásokhoz való hozzáférés kezelése, és állítsa be a delegáltakat, csoportok és rendszergazdai szerepkörök használatával. További információ: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).|
|Hibrid identitás|Azure Active Directory Connect és a Connect Health használatával egyetlen felhasználói identitást biztosíthat a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül helyétől (felhőbeli vagy helyszíni). További információ: [hibrid identitás dokumentációja](../hybrid/index.yml).|
|Identitáskezelés|A szervezet identitás alkalmazott, üzleti partner, szállító, szolgáltatás és alkalmazás-hozzáférés-vezérlés kezelése. A hozzáférési felülvizsgálatok is elvégezheti. További információ: [Azure ad Identity irányítási dokumentáció](../governance/identity-governance-overview.md) és [Azure ad hozzáférési felülvizsgálatok](../governance/access-reviews-overview.md).|
|Identitásvédelem|Észleli a potenciális biztonsági réseket, hogy ez hatással lenne a szervezet identitásait, válaszolni a gyanús tevékenységeket szabályzatok konfigurálását és majd hajtsa végre a megfelelő műveletet a problémák megoldásához. További információ: [Azure ad Identity Protection](../identity-protection/index.yml).|
|Azure-erőforrások felügyelt identitásai|Az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure ad-ben, amely bármely Azure AD által támogatott hitelesítési szolgáltatás, beleértve a Key Vault hitelesítheti. További információ: [Mi az az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Kezelheti, irányíthatja, és figyelheti a hozzáférést a szervezeten belül. Ez a funkció magában foglalja az Azure AD-ben és az Azure-ban, valamint más Microsoft Online Services-szolgáltatásokban, például az Office 365-ben vagy az Intune-ban További információ: [Azure ad Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a biztonsági és használati minták a környezetben. További információ: [Azure Active Directory jelentések és figyelés](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Következő lépések

- [Regisztráljon prémium szintű Azure Active Directory](active-directory-get-started-premium.md)

- [Azure-előfizetés hozzárendelése a Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)

- [Hozzáférés Azure Active Directory és új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- [prémium szintű Azure Active Directory P2 szolgáltatás üzembe helyezési ellenőrzőlistája](active-directory-deployment-checklist-p2.md)
