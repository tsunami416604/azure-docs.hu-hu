---
title: Identitás-irányítási - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory identitáskezelési lehetővé teszi, hogy egyensúlyt a szervezet biztonsági és munkavállalói termelékenységének a megfelelő folyamatok és láthatóság.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063679"
---
# <a name="what-is-azure-ad-identity-governance"></a>Mi az Azure AD Identity Governance?

Az Azure Active Directory (Azure AD) identitáskezelési lehetővé teszi, hogy egyensúlyt a szervezet biztonsági és munkavállalói termelékenysége a megfelelő folyamatok és láthatóság. Olyan képességekkel rendelkezik, amelyek biztosítják, hogy a megfelelő emberek megfelelő hozzáféréssel rendelkezzenek a megfelelő erőforrásokhoz. Ezek és a kapcsolódó Azure AD és Enterprise Mobility + Security funkciók lehetővé teszik a hozzáférési kockázat csökkentését a kritikus eszközökhöz való hozzáférés védelmével, figyelésével és naplózásával – miközben biztosítja az alkalmazottak és az üzleti partnerek termelékenységét.  

Az identitás-szabályozás lehetővé teszi a szervezetek számára, hogy a következő feladatokat végezd el az alkalmazottak, az üzleti partnerek és a szállítók között, valamint a szolgáltatások és alkalmazások között, mind a helyszínen, mind a felhőkben:

- Az identitás életciklusának szabályozása
- A hozzáférés életciklusának szabályozása
- Biztonságos, kiemelt hozzáférés a felügyelethez

Pontosabban, ez a célja, hogy segítse a szervezeteket, hogy foglalkozzon a következő négy kulcsfontosságú kérdés:

- Mely felhasználóknak kell hozzáférniük az erőforrásokhoz?
- Mit csinálnak ezek a felhasználók ezzel a hozzáféréssel?
- Vannak-e hatékony szervezeti vezérlők a hozzáférés kezeléséhez?
- Ellenőrizhetik-e az ellenőrök, hogy az ellenőrzések működnek-e?

## <a name="identity-lifecycle"></a>Identitás életciklusa

Az identitás-szabályozás segítségével a szervezetek egyensúlyt teremtenek a *termelékenység* között – Milyen gyorsan férhet hozzá egy személy a szükséges erőforrásokhoz, például amikor csatlakozik a szervezetemhez? És *biztonság* - Hogyan kell a hozzáférés idővel változik, például a változások miatt az adott személy foglalkoztatási státuszát?  Az identitáséletciklus-kezelés az identitáskezelés alapja, és a hatékony nagy mértékű szabályozás az alkalmazások identitáséletciklus-kezelési infrastruktúrájának modernizálását igényli.

![Identitás életciklusa](./media/identity-governance-overview/identity-lifecycle.png)

Számos szervezet esetében az alkalmazottak identitásának életciklusa az adott felhasználó HCM (humántőke-kezelési) rendszerben való megjelenítéséhez kötődik.  Az Azure AD Premium automatikusan megőrzi a workday-ben az Active Directoryban és az Azure Active Directoryban képviselt személyek felhasználói identitását, [aworkday-i bejövő kiépítési oktatóanyagban](../saas-apps/workday-inbound-tutorial.md)leírtak szerint.  Az Azure AD Premium tartalmazza a [Microsoft Identity Managert](/microsoft-identity-manager/)is, amely rekordokat importálhat a helyszíni HCM-rendszerekből, például az SAP-ból, az Oracle eBusiness-ből és az Oracle PeopleSoft-ból.

A forgatókönyvek egyre inkább a szervezeten kívüli személyekkel való együttműködést igénylik. [Az Azure AD B2B](/azure/active-directory/b2b/) együttműködés lehetővé teszi, hogy biztonságosan ossza meg a szervezet alkalmazásait és szolgáltatásait bármely szervezet vendégfelhasználóival és külső partnereivel, miközben továbbra is kézben tarthatja a saját vállalati adatait.  [Az Azure AD jogosultságkezelés](entitlement-management-overview.md) lehetővé teszi, hogy kiválassza, mely szervezet felhasználói kérhetnek hozzáférést, és a szervezet címtárába B2B-vendégekként adhatók hozzá, és biztosíthatja, hogy ezek a vendégek eltávolításra kerüljenek, amikor már nincs szükségük hozzáférésre.

## <a name="access-lifecycle"></a>Hozzáférés életciklusa

A szervezeteknek olyan folyamatra van szükségük, amely meghaladja a felhasználó számára eredetileg kiépített hozzáférésen túl a felhasználó identitásának létrehozásakor.  Továbbá a vállalati szervezeteknek képesnek kell lenniük a hatékony méretezésre, hogy folyamatosan fejleszthessék és érvényesíthessék a hozzáférési házirendeket és -vezérlőket.

![Hozzáférés életciklusa](./media/identity-governance-overview/access-lifecycle.png)

Az informatikai részleg általában hozzáférést biztosít az üzleti döntéshozóknak a jóváhagyási döntésekhez.  Továbbá, az it is bevonja a felhasználók magukat.  Például azoknak a felhasználóknak, akik egy vállalat európai marketingalkalmazásában férnek hozzá a bizalmas ügyféladatokhoz, ismernie kell a vállalat irányelveit. Előfordulhat, hogy a vendégfelhasználók nincsenek tisztában azon szervezet adatainak kezelési követelményeivel, amelyekhez meghívást kaptak.

A szervezetek automatizálhatják a hozzáférési életciklus-folyamatot olyan technológiákkal, mint például [a dinamikus csoportok,](../users-groups-roles/groups-dynamic-membership.md)valamint a felhasználók [saas-alkalmazásokba](../saas-apps/tutorial-list.md) vagy [az SCIM-mel integrált alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)való kiépítéssel.  A szervezetek azt is szabályozhatják, hogy mely [vendégfelhasználók férhetnek hozzá a helyszíni alkalmazásokhoz.](../b2b/hybrid-cloud-to-on-premises.md)  Ezek a hozzáférési jogok ezután rendszeresen felülvizsgálhatók az ismétlődő [Azure AD-hozzáférési felülvizsgálatok](access-reviews-overview.md)használatával.   [Az Azure AD jogosultságkezelés](entitlement-management-overview.md) azt is lehetővé teszi, hogy meghatározza, hogyan kérnek hozzáférést a felhasználók a csoport- és csoporttagságok, alkalmazásszerepkörök és SharePoint Online-szerepkörök csomagjai között.

Amikor egy felhasználó megpróbál hozzáférni az alkalmazásokhoz, az Azure AD [feltételes hozzáférési](/azure/active-directory/conditional-access/) szabályzatokat kényszerít ki. A feltételes hozzáférési házirendek közé tartozhat például a [használati feltételek](../conditional-access/terms-of-use.md) megjelenítése, valamint annak biztosítása, hogy a felhasználó az alkalmazás elérése előtt [beleegyezett ezekbe a feltételekbe.](../conditional-access/require-tou.md)

## <a name="privileged-access-lifecycle"></a>Kiemelt hozzáférés életciklusa

Korábban privilegizált hozzáférést más szállítók az identitás-szabályozástól különálló képességként írtak le. A Microsoftnál azonban úgy gondoljuk, hogy a kiemelt hozzáférés szabályozása az identitás-szabályozás kulcsfontosságú része – különösen, ha figyelembe vesszük a rendszergazdai jogokkal kapcsolatos visszaélések lehetőségét a szervezet számára. Az okat az alkalmazottakat, szállítókat és alvállalkozókat, akik rendszergazdai jogokat vállalnak, szabályozni kell.

![Kiemelt hozzáférés életciklusa](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Az Azure AD emelt szintű identitáskezelés (PIM)](../privileged-identity-management/pim-configure.md) további vezérlőket biztosít az erőforrások hozzáférési jogainak védelmére az Azure AD, az Azure és más Microsoft Online Services szolgáltatásban.  Az Azure AD PIM által biztosított just-in-time hozzáférés és szerepkör-módosítási riasztási képességek a többtényezős hitelesítés és a feltételes hozzáférés mellett átfogó cégirányítási vezérlőket biztosítanak a vállalat erőforrásainak (címtár, Office 365-ös és Azure-erőforrás-szerepkörök). A hozzáférés más formáihoz is a szervezetek hozzáférési felülvizsgálatok segítségével konfigurálhatják az ismétlődő hozzáférés-újraengedélyezést a rendszergazdai szerepkörökben lévő összes felhasználó számára.

## <a name="getting-started"></a>Első lépések

Tekintse meg az **Identitáskezelés** az Azure Portalon az Első lépések lapon a jogosultságkezelés, a hozzáférési felülvizsgálatok, a kiemelt identitáskezelés és a használati feltételek használatának megkezdéséhez.

![Identitás-irányítási lépések](./media/identity-governance-overview/getting-started.png)


Ha bármilyen visszajelzést identitáscégirányítási funkciók, kattintson a **Visszajelzés bekérése az** Azure Portalon a visszajelzés elküldéséhez. A csapat rendszeresen értékeli a visszajelzéseket.

Bár nincs tökéletes megoldás vagy javaslat minden ügyfél számára, a következő konfigurációs útmutatók is biztosítják a Microsoft által javasolt alapházirendeket a biztonságosabb és termelékenyebb munkaerő biztosítása érdekében.

- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Emelt szintű hozzáférés biztonsága](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Függelék – az identitás-irányítási funkciók kezeléséhez a legkevésbé kiemelt szerepkörök

Ajánlott eljárás a legkevésbé kiemelt szerepkör használatával felügyeleti feladatok végrehajtása az identitás-szabályozás. Azt javasoljuk, hogy az Azure AD PIM használatával aktiválja a szerepkört, ha szükséges a feladatok végrehajtásához. Az identitás-irányítási szolgáltatások konfigurálásához a legkevésbé kiemelt címtárszerepkörök vannak:

| Szolgáltatás | Legkevésbé privilegizált szerepkör |
| ------- | --------------------- |
| Jogosultságkezelés | Rendszergazda (kivéve a SharePoint Online-webhelyek katalógusokhoz való hozzáadását, amelyglobális rendszergazdát igényel) |
| Hozzáférési felülvizsgálatok | Felhasználói rendszergazda (az Azure- vagy Azure AD-szerepkörök hozzáférési felülvizsgálatának kivételével, amely hez kiemelt szerep-rendszergazda szükséges) |
|Privileged Identity Management | Kiemelt szerepkör-rendszergazda |
| Használati feltételek | Biztonsági rendszergazda vagy feltételes hozzáférés-rendszergazda |

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD-jogosultságkezelés?](entitlement-management-overview.md)
- [Mik azok az Azure AD-hozzáférési felülvizsgálatok?](access-reviews-overview.md)
- [Mi az az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Mire használhatom a Használati feltételek funkciót?](active-directory-tou.md)


