---
title: Mi az az Azure Active Directory? | Microsoft Docs
description: További információ az Azure Active Directoryban, beleértve a szükséges terminológia célközönség, licencelés alapjai és társított szolgáltatások.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.date: 11/13/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 251161fbb5213c765463835e4537f1b443699670
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491739"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory? 
Az Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás- és hozzáférés felügyeleti szolgáltatása. Az Azure AD segítségével az alkalmazottak bejelentkezhet és elérheti az erőforrásokat:

- Külső erőforrások, például a Microsoft Office 365, az Azure Portalon, és több ezer más SaaS-alkalmazásokhoz.

- Belső erőforrásokat, például a vállalati hálózat és az intranetes, és olyan felhőalkalmazások, a saját szervezet által fejlesztett alkalmazások.

Használhatja a különböző [Microsoft Cloud for Enterprise Architects sorozat](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) poszterek jobb megértése érdekében az Azure-ban, az Azure AD identity alapszolgáltatások és az Office 365.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure ad-ben?
Az Azure AD célközönsége:

- **IT rendszergazdák.** INFORMATIKAI rendszergazdaként az Azure AD segítségével szabályozhatja a hozzáférést az alkalmazások és az alkalmazás-erőforrások, az üzleti követelmények alapján. Az Azure AD használatával például többtényezős hitelesítés megkövetelése, ha fontos a szervezeti erőforrások elérése. Emellett az Azure AD segítségével között a meglévő Windows Server AD és a felhőalapú alkalmazások, beleértve az Office 365 felhasználók létrehozásának automatizálása. Végül az Azure AD biztosítja a hatékony eszközöket automatikusan a felhasználói identitások és a hitelesítő adatok védelme érdekében, illetve a hozzáférési cégirányítási követelmények kielégítése céljából. Első lépésként regisztráljon egy [30 napos ingyenes Azure Active Directory Premium próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Alkalmazásfejlesztőknek.** Alkalmazásfejlesztőként Azure ad-ben megtalálható a szabványokon alapuló megközelítés egyszeri bejelentkezés (SSO) hozzáadása az alkalmazáshoz, lehetővé téve, hogy a felhasználó meglévő hitelesítő adatok használata. Azure ad-ben is biztosít az API-k, amelyek segítségével hozhat létre személyre szabott Alkalmazásfunkciók meglévő szervezeti adatok felhasználásával. Első lépésként regisztráljon egy [30 napos ingyenes Azure Active Directory Premium próbaverzió](https://azure.microsoft.com/trial/get-started-active-directory/). További információ is megtekinthető [Azure Active Directory fejlesztők számára](../develop/index.yml).

- **A Microsoft 365, Office 365, Azure vagy a Dynamics CRM Online-előfizetők számára.** Előfizetőként már használja az Azure ad-ben. Minden Microsoft 365, az Office 365, Azure és a Dynamics CRM Online bérlő automatikusan az Azure AD-bérlő. Azonnal elkezdheti az integrált felhőbeli alkalmazásokhoz való hozzáférés kezelésére.

## <a name="what-are-the-azure-ad-licenses"></a>Mik az Azure AD-licenceket?
Microsoft Online üzleti szolgáltatásokat, például Office 365 vagy a Microsoft Azure, Azure AD bejelentkezési és az identity protection segítségével igényelnek. Ezért ha előfizet bármely Microsoft Online üzleti, automatikusan kap az ingyenes funkciókat a hozzáférést az Azure ad-ben.

Az Azure AD-implementáció javítása érdekében is hozzáadhat fizetős képességek az alapszintű Azure Active Directory, prémium P1 vagy P2 licenccel. Fizetős licencek az Azure AD épülnek a meglévő ingyenes directory önkiszolgáló jellegű, fejlett monitorozás, biztonsági jelentések készítése és biztonságos hozzáférést biztosít a mobil munkaerő számára.

>[!Note]
>Az ezen licencek díjszabási lehetőségek, lásd: [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory/).<br><br>Az Azure Active Directory Prémium P1 és Prémium P2, valamint az Azure Active Directory Alapszintű kiadás használata Kínában jelenleg nem támogatott. Az Azure AD díjszabással kapcsolatos további információkért forduljon a [Azure Active Directory-fórumon](https://azure.microsoft.com/support/community/?product=active-directory).

- **Az Azure Active Directory ingyenes.** Felhasználók és csoportok kezelése, a helyszíni címtár-szinkronizálás, alapszintű jelentések és egyszeri bejelentkezést biztosít az Azure, Office 365-höz és számos népszerű SaaS-alkalmazásokhoz.

- **Alapszintű Azure Active Directory.** Az ingyenes szolgáltatások alapszintű cloud-központú alkalmazás eléréséhez, Csoportalapú hozzáférés-kezelést, önkiszolgáló jelszó-visszaállítást a felhőalapú alkalmazások és az Azure AD-alkalmazásproxy, tesz közzé, amellyel a helyszíni webalkalmazásokat az Azure AD-vel is nyújt.

- **P1 prémium szintű Azure Active Directory.** Az ingyenes és alapszintű funkciók mellett P1 is lehetővé teszi, hogy a hibrid felhasználók elérésére mind a helyszíni és felhőbeli erőforrásokhoz. Emellett támogatja a speciális felügyeleti, például a dinamikus csoportok, önkiszolgáló csoportfelügyelet, a Microsoft Identity Manager (egy a helyszíni identitás- és hozzáférés felügyeleti csomag) és a felhőalapú késleltetve visszaírt képességek, amelyek lehetővé teszik önkiszolgáló jelszó-visszaállítást a a helyszíni felhasználók.

- **P2 prémium szintű Azure Active Directory.** Kívül, az ingyenes, alapszintű és a P1, P2 is kínál [Azure Active Directory Identity Protection](../identity-protection/enable.md) segítségével biztosítja a kockázatalapú feltételes hozzáférés az alkalmazások és a kritikus vállalati adatokat és [Privileged Identity Felügyeleti](../privileged-identity-management/pim-getting-started.md) felderíteni, korlátozása és a rendszergazdák és azok erőforrásokhoz való hozzáférésének figyelése és szükség esetén – igény hozzáférést biztosítani.

- **"Használatalapú fizetés" funkció licenceket.** A szolgáltatás további licenceket, például az Azure Active Directory üzleti és ügyfél közötti (B2C) is kaphat. B2C segítségével biztosíthat identitás- és hozzáférés-kezelés az ügyfelek által használt alkalmazások számára. További információkért lásd: [Azure Active Directory B2C dokumentációja](../../active-directory-b2c/index.yml).

Az Azure ad-hez Azure-előfizetéssel való társításáról szóló további információkért lásd: [hogyan: Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md) és licencek hozzárendelése a felhasználók kapcsolatos további információkért lásd: [hogyan: Rendelje hozzá, vagy távolítsa el az Azure Active Directory-licenceket](license-users-groups.md).

## <a name="terminology"></a>Terminológia
Jobb megértése érdekében az Azure AD és a hozzá tartozó dokumentáció, tekintse át az alábbi feltételek.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Azure-előfizetés| Használja az Azure cloud services kell fizetnie. Több előfizetéssel is rendelkezik, és vannak kapcsolva hitelkártya.|
|Azure-bérlő| Egy dedikált és megbízható Azure AD példányát, amely automatikusan létrejön, amikor egy szervezet előfizet a Microsoft felhőalapú szolgáltatás előfizetésre, például a Microsoft Azure, Microsoft Intune vagy Office 365-höz. Az Azure-bérlő egyetlen szervezet jelöli.|
|Egybérlős alkalmazás| Az Azure a bérlők, dedikált környezetben más szolgáltatások eléréséhez egybérlős minősülnek.|
|Több-bérlős| Az Azure bérlők számára, hogy megosztott környezetben, más szolgáltatások eléréséhez több vállalat között, több-bérlős minősülnek.|
|Azure AD-címtár|Minden Azure-bérlő rendelkezik egy dedikált és megbízható Azure AD-címtárban. Az Azure AD-címtár tartalmazza a bérlő felhasználók, csoportok és alkalmazások, és hajtsa végre az identitás és hozzáférés-kezelési funkciók bérlői erőforrások szolgál.|
|Azure AD-fiók | Az identitás létrehozása az Azure AD-n keresztül, vagy egy másik Microsoft felhőszolgáltatáshoz, például az Office 365. Identitások a következők: Azure AD-ben tárolt és felhőszolgáltatás-előfizetés a szervezet számára is elérhető. Ez a fiók néven is ismert munkahelyi vagy iskolai fiókkal.|
|Egyéni tartomány|Minden új Azure AD-címtár is tartalmaz egy kezdeti tartománynevet domainname.onmicrosoft.com. Emellett a kezdeti neve, azt is megteheti neveket, többek között a nevek, üzleti és a felhasználók, illetve használhat a szervezet erőforrásaihoz, a listához eléréséhez használja a szervezet tartományához. Egyéni tartománynevek hozzáadása segítségével hozhat létre, amely számára is felismerhetők a felhasználók számára, például felhasználóneveket alain@contoso.com.|
|Fiókadminisztrátor|A hagyományos előfizetés-rendszergazda szerepkör elméleti szinten az előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáféréssel rendelkezik a [Azure Account Center](https://account.azure.com/Subscriptions) , és lehetővé teszi, hogy a fiók az összes előfizetés kezelése. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Szolgáltatás-rendszergazda|A hagyományos előfizetés-rendszergazda szerepkör lehetővé teszi, hogy az összes Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör rendelkezik egy előfizetésre a tulajdonosi szerepkörrel felruházott felhasználó a megfelelő hozzáférést. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Tulajdonos|Ez a szerepkör segítségével minden Azure-erőforrások kezelése, beleértve a hozzáférést. Ez a szerepkör egy újabb engedélyezési rendszer nevű szerepkör alapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások részletes hozzáférés-vezérlést biztosító épül. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepköröket, az Azure RBAC-szerepkörök és az Azure AD-rendszergazdai szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globális Azure AD-rendszergazda|A rendszergazdai szerepkör van automatikusan hozzárendelt, akinél létrehozni az Azure AD-bérlővel. Globális rendszergazdák hajthatják végre, minden, a felügyeleti funkciók az Azure ad-hez, és vonhat össze az Azure AD, például az Exchange Online, SharePoint Online és Skype vállalati online szolgáltatásokat. Több globális rendszergazda is használhat, de csak a globális rendszergazdák (beleértve a más globális rendszergazdákat hozzárendelése) rendszergazdai szerepkörök hozzárendelése a felhasználókhoz.<br><br>**Megjegyzés**<br>A rendszergazda szerepkör nevezzük a globális rendszergazda az Azure Portalon, de nevezzük **vállalati rendszergazda** a Microsoft Graph API, Azure AD Graph API és az Azure AD PowerShell.<br><br>Különböző rendszergazdai szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](../users-groups-roles/directory-assign-admin-roles.md).|
|Microsoft-fiók (más néven, MSA)|Személyes fiók, amely biztosít hozzáférést a végfelhasználóra irányuló Microsoft termékekhez, és a felhőalapú szolgáltatások, például az Outlook, OneDrive, Xbox LIVE- vagy Office 365-höz. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja.|

## <a name="which-features-work-in-azure-ad"></a>Melyik szolgáltatások akkor működnek az Azure ad-ben?
Miután kiválasztotta az Azure AD-licencre, vagy azok egy részét a következő szolgáltatások elérését jelenik meg a szervezet számára:

|Kategória|Leírás|
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
|Privileged identity management (PIM)|Kezelheti, irányíthatja, és figyelheti a hozzáférést a szervezeten belül. Ez a szolgáltatás biztosít hozzáférést az Azure AD-erőforrások Azure-erőforrások és más Microsoft Online szolgáltatásaihoz, például az Office 365 vagy az Intune. További információkért lásd: [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a biztonsági és használati minták a környezetben. További információkért lásd: [Azure Active Directory-jelentések és figyelés](../reports-monitoring/index.yml).|


## <a name="next-steps"></a>További lépések
- [Regisztráljon az Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Az Azure Active Directory Azure-előfizetés társítása](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory eléréséhez és a egy új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- [Az Azure Active Directory Premium P2 a szolgáltatás telepítési ellenőrzőlista](active-directory-deployment-checklist-p2.md)
