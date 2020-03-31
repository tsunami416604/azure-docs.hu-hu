---
title: Mi az az Azure Active Directory? - Azure Active Directory | Microsoft dokumentumok
description: Áttekintés és általános információk az Azure Active Directoryról, beleértve a terminológiát, a rendelkezésre álló licenceket, valamint a további információkat tartalmazó hivatkozásokat tartalmazó kapcsolódó funkciók listáját.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240785"
---
# <a name="what-is-azure-active-directory"></a>Mi az az Azure Active Directory?

Az Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása, amely segít az alkalmazottaknak bejelentkezni és erőforrásokat elérni:

- Külső erőforrások, például a Microsoft Office 365, az Azure Portal és több ezer más SaaS-alkalmazás.

- Belső erőforrások, például a vállalati hálózaton és az intraneten lévő alkalmazások, valamint a saját szervezete által kifejlesztett felhőalkalmazások.

A [Különböző Microsoft Cloud for Enterprise Architects sorozatposzterek](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) segítségével jobban megismerheti az Azure, az Azure AD és az Office 365 alapvető identitáskezelési szolgáltatásait.

## <a name="who-uses-azure-ad"></a>Ki használja az Azure AD-t?

Az Azure AD célja:

- **IT rendszergazdák.** Informatikai rendszergazdaként az Azure AD segítségével szabályozhatja az alkalmazásokhoz és az alkalmazás-erőforrásokhoz való hozzáférést az üzleti követelmények nek megfelelően. Például használhatja az Azure AD-t a többtényezős hitelesítés megköveteléséhez fontos szervezeti erőforrások elérésekor. Emellett az Azure AD segítségével automatizálhatja a meglévő Windows Server AD és a felhőalapú alkalmazások, köztük az Office 365 közötti felhasználói kiépítést. Végül az Azure AD hatékony eszközöket biztosít a felhasználói identitások és hitelesítő adatok automatikus védelméhez és a hozzáférés-irányítási követelmények teljesítéséhez. Első lépésekhez regisztráljon egy [ingyenes, 30 napos Azure Active Directory Premium próbaverzióra.](https://azure.microsoft.com/trial/get-started-active-directory/)

- **Alkalmazásfejlesztők.** Alkalmazásfejlesztőként használhatja az Azure AD-t szabványalapú megközelítésként egyszeri bejelentkezés (SSO) hozzáadásához az alkalmazáshoz, lehetővé téve, hogy a felhasználó már meglévő hitelesítő adataival dolgozzon. Az Azure AD olyan API-kat is biztosít, amelyek segítségével személyre szabott alkalmazásélményeket hozhat létre a meglévő szervezeti adatok használatával. Első lépésekhez regisztráljon egy [ingyenes, 30 napos Azure Active Directory Premium próbaverzióra.](https://azure.microsoft.com/trial/get-started-active-directory/) További információt az Azure [Active Directory fejlesztőknek](../develop/index.yml)című témakörben talál.

- **Microsoft 365, Office 365, Azure vagy Dynamics CRM Online-előfizetők.** Előfizetőként már használja az Azure AD-t. Minden Microsoft 365, Office 365, Azure és Dynamics CRM Online-bérlő automatikusan Egy Azure AD-bérlő. Azonnal elkezdheti kezelni az integrált felhőalapú alkalmazásokhoz való hozzáférést.

## <a name="what-are-the-azure-ad-licenses"></a>Mik azok az Azure AD-licencek?

A Microsoft Online üzleti szolgáltatásaihoz, például az Office 365-höz vagy a Microsoft Azure-hoz a bejelentkezéshez és az identitásvédelemhez az Azure AD-re van szükség. Ha feliratkozik bármelyik Microsoft Online üzleti szolgáltatásra, automatikusan megkapja az Azure AD-t az összes ingyenes funkcióhoz való hozzáféréssel.

Az Azure AD-implementáció javítása érdekében fizetős képességeket is hozzáadhat az Azure Active Directory Premium P1 vagy Premium P2 licencekre való frissítéssel. Az Azure AD fizetős licencei a meglévő ingyenes címtárra épülnek, amely önkiszolgáló, továbbfejlesztett figyelést, biztonsági jelentéseket és biztonságos hozzáférést biztosít a mobilfelhasználók számára.

>[!Note]
>A licencek díjszabási beállításairól az [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)című témakörben található.
>
>Az Azure Active Directory Prémium P1 és prémium szintű P2 jelenleg nem támogatott Kínában. Az Azure AD díjszabásával kapcsolatos további információkért forduljon az [Azure Active Directory fórumhoz.](https://azure.microsoft.com/support/community/?product=active-directory)

- **Az Azure Active Directory ingyenes.** Felhasználói és csoportkezelést, helyszíni címtár-szinkronizálást, alapvető jelentéseket, önkiszolgáló jelszómódosítást biztosít a felhőbeli felhasználók számára, valamint egyszeri bejelentkezést az Azure-ban, az Office 365-ben és számos népszerű SaaS-alkalmazásban.

- **P1 prémium szintű Azure Active Directory.** Az ingyenes funkciók mellett a P1 lehetővé teszi a hibrid felhasználók számára a helyszíni és a felhőbeli erőforrások elérését is. Támogatja a speciális felügyeleteket is, például a dinamikus csoportokat, az önkiszolgáló csoportkezelést, a Microsoft Identity Managert (helyszíni identitás- és hozzáférés-kezelési csomag) és a felhőalapú visszaírási funkciókat, amelyek lehetővé teszik az önkiszolgáló jelszó-visszaállítást a helyszíni felhasználók számára.

- **P2 prémium szintű Azure Active Directory.** Az ingyenes és a P1 funkciók mellett a P2 [az Azure Active Directory identity protection](../identity-protection/overview-identity-protection.md) szolgáltatást is kínál, amely kockázatalapú feltételes hozzáférést biztosít az alkalmazásokhoz és a kritikus vállalati adatokhoz, valamint a kiemelt [identitáskezeléshez,](../privileged-identity-management/pim-getting-started.md) hogy segítsen felderíteni, korlátozni és figyelni a rendszergazdákat és az erőforrásokhoz való hozzáférésüket, és szükség esetén csak időben biztosítani az időben való hozzáférést.

- **"Pay as you go" funkciólicencek.** További szolgáltatáslicenceket is beszerezhet, például az Azure Active Directory üzleti ügyfél (B2C) szolgáltatást. A B2C segítségével identitás- és hozzáférés-kezelési megoldásokat biztosíthat az ügyfélfelé néző alkalmazásokhoz. További információ: [Azure Active Directory B2C dokumentáció.](../../active-directory-b2c/index.yml)

Az Azure-előfizetés Azure AD-hez való társításáról a [Hogyan társítson vagy adjon hozzá Azure-előfizetést az Azure Active Directoryhoz,](active-directory-how-subscriptions-associated-directory.md) valamint a licencek felhasználókhoz rendeléséről a [Hogyan rendelheti hozzá vagy távolíthatja el az Azure Active Directory-licenceket.](license-users-groups.md)

## <a name="terminology"></a>Terminológia

Az Azure AD és dokumentációjának jobb megértéséhez javasoljuk az alábbi kifejezések áttekintését.

|Kifejezés vagy fogalom|Leírás|
|---------------|-----------|
|Identitás| Egy dolog, amit hitelesíteni lehet. Az identitás lehet felhasználónévvel és jelszóval rendelkező felhasználó. Az identitások olyan alkalmazásokat vagy más kiszolgálókat is tartalmaznak, amelyek titkos kulcsok on vagy tanúsítványokon keresztüli hitelesítést igényelhetnek.|
|Fiók| Olyan identitás, amelyhez adatok vannak társítva. Identitás nélkül nem lehet fiókja.|
|Azure AD-fiók| Az Azure AD vagy más Microsoft felhőszolgáltatás, például az Office 365 által létrehozott identitás. Az identitások az Azure AD-ben tárolódnak, és a szervezet felhőszolgáltatás-előfizetései számára érhetők el. Ezt a fiókot munkahelyi vagy iskolai fióknak is nevezik.|
|Azure-előfizetés| Az Azure felhőszolgáltatásaiért fizetett. Számos előfizetése lehet, és hitelkártya-kapcsolatban állnak.|
|Azure-bérlő| Az Azure AD dedikált és megbízható példánya, amely automatikusan létrejön, amikor a szervezet regisztrál egy Microsoft felhőszolgáltatási előfizetésre, például a Microsoft Azure, a Microsoft Intune vagy az Office 365 szolgáltatásra. Az Azure-bérlő egyetlen szervezetet jelöl.|
|Egybérlős alkalmazás| Az Azure-bérlők, amelyek más szolgáltatásokat egy dedikált környezetben férnek hozzá, egyetlen bérlőnek minősülnek.|
|Több-bérlős| Az Azure-bérlők, amelyek más szolgáltatásokhoz férnek hozzá egy megosztott környezetben, több szervezetközött, több-bérlősnek minősülnek.|
|Azure AD-címtár|Minden Azure-bérlő rendelkezik egy dedikált és megbízható Azure AD-könyvtárral. Az Azure AD-címtár tartalmazza a bérlő felhasználók, csoportok és alkalmazások, és a bérlői erőforrások identitás- és hozzáférés-kezelési funkciók végrehajtására szolgál.|
|Egyéni tartomány|Minden új Azure AD-címtár hoz egy kezdeti domainnevet, domainname.onmicrosoft.com. A kezdeti név mellett hozzáadhatja a szervezet tartományneveit is, amelyek tartalmazzák az üzleti tevékenységhez használt neveket és a felhasználók által a szervezet erőforrásainak eléréséhez használt neveket. Az egyéni tartománynevek hozzáadásával olyan felhasználóneveket hozhat létre, amelyek ismerősek a felhasználók számára, például alain@contoso.com.|
|Fiókadminisztrátor|Ez a klasszikus előfizetés-rendszergazdai szerepkör fogalmilag egy előfizetés számlázási tulajdonosa. Ez a szerepkör hozzáfér az [Azure Account Center,](https://account.azure.com/Subscriptions) és lehetővé teszi, hogy egy fiók összes előfizetését kezelheti. További információ: [Klasszikus előfizetéses rendszergazdai szerepkörök, Azure szerepkör-alapú hozzáférés-vezérlési (RBAC) szerepkörök és Az Azure AD rendszergazdai szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md)|
|Szolgáltatás-rendszergazda|Ez a klasszikus előfizetés-rendszergazdai szerepkör lehetővé teszi az összes Azure-erőforrás kezelését, beleértve a hozzáférést is. Ez a szerepkör az előfizetéshatókörtulajdonosi szerepkörhöz rendelt felhasználó egyenértékű hozzáféréssel rendelkezik. További információ: [Klasszikus előfizetéses rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure AD-rendszergazdai szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md)|
|Tulajdonos|Ez a szerepkör segít az összes Azure-erőforrás kezeléséhez, beleértve a hozzáférést is. Ez a szerepkör egy újabb engedélyezési rendszerre, a szerepkör-alap hozzáférés-vezérlésre (RBAC) épül, amely részletes hozzáférés-kezelést biztosít az Azure-erőforrásokhoz. További információ: [Klasszikus előfizetéses rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure AD-rendszergazdai szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md)|
|Globális Azure AD-rendszergazda|Ez a rendszergazdai szerepkör automatikusan hozzá van rendelve ahhoz, aki létrehozta az Azure AD-bérlőt. A globális rendszergazdák elvégezhetik az Azure AD összes felügyeleti funkcióját és az Azure AD-hez szükséges szolgáltatásokat, például az Exchange Online-t, a SharePoint Online-t és a Skype Vállalati online verziót. Több globális rendszergazdával is rendelkezhet, de csak a globális rendszergazdák rendelhetnek rendszergazdai szerepköröket (beleértve más globális rendszergazdákhozzárendelését is) a felhasználókhoz.<br><br>**Megjegyzés**<br>Ezt a rendszergazdai szerepkört globális rendszergazdának hívják az Azure Portalon, de a Microsoft Graph API-ban és az Azure AD PowerShellben **vállalati rendszergazdának** nevezik.<br><br>A különböző rendszergazdai szerepkörökről további információt a [Rendszergazdai szerepkör-engedélyek az Azure Active Directoryban című témakörben talál.](../users-groups-roles/directory-assign-admin-roles.md)|
|Microsoft-fiók (más néven MSA)|Olyan személyes fiókok, amelyek hozzáférést biztosítanak a fogyasztóorientált Microsoft-termékekhez és felhőszolgáltatásokhoz, például az Outlookhoz, a OneDrive-hoz, az Xbox LIVE-hoz vagy az Office 365-höz. A Microsoft-fiókot a Microsoft által működtetett Microsoft fogyasztói identitásfiók-rendszerben hoztuk létre és tároljuk.|

## <a name="which-features-work-in-azure-ad"></a>Mely funkciók működnek az Azure AD-ben?

Miután kiválasztotta az Azure AD-licencet, hozzáférhet a szervezet alábbi funkcióihoz vagy az összeshez:

|Kategória|Leírás|
|-------|-----------|
|Alkalmazáskezelés|A felhőbeli és helyszíni alkalmazások kezelése alkalmazásproxyval, egyszeri bejelentkezéssel, a Saját alkalmazások portállal (más néven Access panel) és a Szoftver szolgáltatásként (SaaS) alkalmazásokkal. További információt a [Helyszíni alkalmazások biztonságos távoli hozzáférésének biztosítása](../manage-apps/application-proxy.md) és [alkalmazáskezelési dokumentáció című témakörben talál.](../manage-apps/index.yml)|
|Hitelesítés|Kezelheti az Azure Active Directory önkiszolgáló jelszó-alaphelyzetbe állítását, a többtényezős hitelesítést, az egyéni tiltott jelszavak listáját és az intelligens zárolást. További információ: [Azure AD authentication documentation](../authentication/index.yml).|
|Vállalatközi (B2B)|Kezelje vendégfelhasználóit és külső partnereit, miközben továbbra is kézben tarthatja a saját vállalati adatait. További információ: [Azure Active Directory B2B dokumentáció.](../b2b/index.yml)|
|Vállalat és ügyfél közötti (B2C)|Testreszabhatja és szabályozhatja, hogy a felhasználók hogyan regisztrálhatnak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során. További információ: [Azure Active Directory B2C dokumentáció.](../../active-directory-b2c/index.yml)|
|Feltételes hozzáférés|Kezelheti a felhőalapú alkalmazásokhoz való hozzáférést. További információ: [Azure AD conditional access documentation.](../conditional-access/index.yml)|
|Azure Active Directory fejlesztők számára|Olyan alkalmazásokat hozhat létre, amelyek minden Microsoft-identitást bejelentkeznek, jogkivonatokat kapnak a Microsoft Graph, más Microsoft API-k vagy egyéni API-k hívásához. További információ: [Microsoft identity platform (Azure Active Directory fejlesztőknek).](../develop/index.yml)|
|Eszközkezelés|Kezelheti, hogyan férnek hozzá vállalati adataihoz a felhőbeli vagy helyszíni eszközei. További információ: [Azure AD Device Management documentation](../devices/index.yml).|
|Tartományi szolgáltatások|Csatlakozzon az Azure virtuális gépeihez tartományvezérlők használata nélkül. További információt az [Azure AD tartományi szolgáltatások dokumentációjában](../../active-directory-domain-services/index.yml)talál.|
|Vállalati felhasználók|Csoportokkal és rendszergazdai szerepkörökkel kezelheti a licenchozzárendelést, az alkalmazásokhoz való hozzáférést, és meghatalmazottak at állíthat be. További információt az [Azure Active Directory felhasználói felügyeleti dokumentációjában](../users-groups-roles/index.yml)talál.|
|Hibrid identitás|Az Azure Active Directory Connect és a Connect Health használatával egyetlen felhasználói identitást biztosítaz összes erőforrás hitelesítéséhez és engedélyezéséhez, helytől függetlenül (felhőben vagy a helyszínen). További információ: [Hibrid identitásdokumentáció](../hybrid/index.yml).|
|Identitáskezelés|A szervezet identitásának kezelése alkalmazotti, üzleti partneri, szállítói, szolgáltatás- és alkalmazás-hozzáférés-vezérlőkön keresztül. Hozzáférési felülvizsgálatokat is végezhet. További információ: [Azure AD identitáskezelési dokumentáció](../governance/identity-governance-overview.md) és [az Azure AD-hozzáférés-felülvizsgálatok.](../governance/access-reviews-overview.md)|
|Identitásvédelem|Észlelheti a szervezet identitását érintő potenciális biztonsági réseket, konfigurálhatja a szabályzatokat a gyanús műveletekre, majd tegye meg a megfelelő lépéseket azok megoldásához. További információ: [Azure AD Identity Protection.](../identity-protection/index.yml)|
|Azure-erőforrások felügyelt identitásai|Az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure AD-ben, amely bármely Azure AD által támogatott hitelesítési szolgáltatást hitelesítheti, beleértve a Key Vaultot is. További információ: [Mi az Azure-erőforrások felügyelt identitásai?](../managed-identities-azure-resources/overview.md)|
|Privileged Identity Management (PIM)|Kezelheti, szabályozhatja és figyelheti a szervezeten belüli hozzáférést. Ez a funkció hozzáférést biztosít az Azure AD és az Azure erőforrásaihoz, valamint más Microsoft Online Services szolgáltatásokhoz, például az Office 365-höz vagy az Intune-hoz. További információ: [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Jelentések és monitorozás|Betekintést nyerhet a környezet biztonsági és használati mintáiba. További információ: [Azure Active Directory-jelentések és figyelés.](../reports-monitoring/index.yml)|

## <a name="next-steps"></a>További lépések

- [Prémium szintű Azure Active Directory – Regisztráció](active-directory-get-started-premium.md)

- [Azure-előfizetés társítása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)

- [Az Azure Active Directory elérése és új bérlő létrehozása](active-directory-access-create-new-tenant.md)

- [Az Azure Active Directory Premium P2 szolgáltatás telepítési ellenőrzőlistája](active-directory-deployment-checklist-p2.md)
