---
title: Identitás-szabályozás – Azure Active Directory | Microsoft Docs
description: A Azure Active Directory Identity Governance lehetővé teszi, hogy a megfelelő folyamatokkal és láthatósággal egyensúlyt biztosítson a szervezete számára a biztonság és az alkalmazottak hatékonysága iránt.
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
ms.date: 04/29/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f09a8c2f8caae3cbb182cf2dc4621deb95f7e5c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499660"
---
# <a name="what-is-azure-ad-identity-governance"></a>Mi az Azure AD Identity Governance?

A Azure Active Directory (Azure AD) Identity-szabályozás lehetővé teszi, hogy a megfelelő folyamatokkal és láthatósággal egyensúlyt biztosítson a szervezete számára a biztonság és az alkalmazottak hatékonysága szempontjából. Lehetővé teszi az olyan képességek biztosítását, amelyekkel biztosíthatja, hogy a megfelelő felhasználók hozzáférjenek a megfelelő erőforrásokhoz, és így biztosítható a kritikus fontosságú eszközökhöz való hozzáférés biztosítása, monitorozása és naplózása – az alkalmazottak termelékenységének biztosítása mellett.  

Az Identitáskezelés lehetővé teszi a szervezeteknek az alábbi feladatok elvégzését az alkalmazottak, az üzleti partnerek és a szállítók, valamint a szolgáltatások és alkalmazások terén:

- Az identitás életciklusának szabályozása
- Hozzáférési életciklus szabályozása
- Biztonságos felügyelet

Konkrétan a következő négy fő kérdés megválaszolására szolgál a szervezetek számára:

- Mely felhasználók férhetnek hozzá az erőforrásokhoz?
- Mik azok a felhasználók, akik ezt a hozzáférést használják?
- Vannak hatékony szervezeti szabályozások a hozzáférés kezeléséhez?
- Ellenőrizhető, hogy a vezérlők működnek-e?

## <a name="identity-lifecycle"></a>Identitás életciklusa

Az identitások szabályozása révén a szervezetek a *termelékenység* egyensúlyát érhetik el – milyen gyorsan férhet hozzá egy személy a szükséges erőforrásokhoz, például ha a szervezethez csatlakoznak? És *Biztonság* – Hogyan változnak a hozzáférésük az idő múlásával, például az adott személy foglalkoztatási állapotának változásai miatt?  Az identitás-életciklus kezelése az identitások irányításának alapja, és a hatékony irányítás érdekében az alkalmazásokhoz szükséges az identitás-életciklus-kezelési infrastruktúra korszerűsítése.

![Identitás életciklusa](./media/identity-governance-overview/identity-lifecycle.png)

Számos szervezet esetében az alkalmazottak identitás-életciklusa az adott felhasználó egy HCM (Human Capital Management) rendszerbeli ábrázolásához van kötve.  Prémium szintű Azure AD automatikusan megtartja a felhasználói identitásokat a munkanapokban képviselt személyek számára a Active Directory és a Azure Active Directory esetében is, a [munkaidő bejövő létesítése (előzetes verzió) oktatóanyagban](../saas-apps/workday-inbound-tutorial.md)leírtak szerint.  A prémium szintű Azure AD [Microsoft Identity Manager](/microsoft-identity-manager/)is tartalmaz, amelyek olyan helyszíni HCM-rendszerekből származó rekordokat importálnak, mint az SAP, az Oracle inintegráció vagy az Oracle PeopleSoft.

A forgatókönyvek egyre nagyobb mértékben igénylik a szervezeten kívüli személyekkel való együttműködést. Az [Azure ad B2B](/azure/active-directory/b2b/) együttműködés lehetővé teszi, hogy biztonságosan ossza meg szervezete alkalmazásait és szolgáltatásait a vendég felhasználókkal és a külső partnerekkel bármely szervezettől, miközben a saját vállalati adatok felett tartja a felügyeletet.

## <a name="access-lifecycle"></a>Hozzáférési életciklus

A szervezeteknek olyan folyamatra van szükségük, amely túllépi az adott felhasználó identitásának létrehozásakor az eredetileg kiépített hozzáférést.  Emellett a vállalati szervezeteknek képesnek kell lenniük a hatékony méretezésre, hogy folyamatosan képesek legyenek a hozzáférési szabályzatok és ellenőrzések fejlesztésére és betartatására.

![Hozzáférési életciklus](./media/identity-governance-overview/access-lifecycle.png)

Ez általában a hozzáférés-jóváhagyási döntéseket delegálja az üzleti döntéshozóknak.  Emellett maguk is bejárhatják a felhasználókat.  A vállalati marketing alkalmazásban található bizalmas ügyféladatokat használó felhasználóknak például ismerniük kell a vállalat szabályzatait. Előfordulhat, hogy a vendég felhasználói nem tudnak az olyan szervezeten belüli adatkezelési követelményekkel kapcsolatban, amelyeknek meghívásra kerültek.

A szervezetek az olyan technológiákon keresztül automatizálják a hozzáférési életciklus folyamatát, mint a [dinamikus csoportok](../users-groups-roles/groups-dynamic-membership.md), valamint a [scim integrált](../manage-apps/use-scim-to-provision-users-and-groups.md) [SaaS](../saas-apps/tutorial-list.md) -alkalmazásokhoz vagy alkalmazásokhoz való felhasználói kiépítés.  A szervezetek azt is megadhatják [, hogy mely vendég felhasználók férhetnek hozzá a](../b2b/hybrid-cloud-to-on-premises.md)helyszíni alkalmazásokhoz.  Ezek a hozzáférési jogosultságok rendszeresen áttekinthetők az ismétlődő [Azure ad hozzáférési felülvizsgálatok](access-reviews-overview.md)használatával.

Amikor egy felhasználó megpróbál hozzáférni az alkalmazásokhoz, az Azure AD kikényszeríti a [feltételes hozzáférési](/azure/active-directory/conditional-access/) szabályzatokat. A feltételes hozzáférési szabályzatok tartalmazhatják például a használati [feltételek](../conditional-access/terms-of-use.md) megjelenítését, és biztosítják, hogy [a felhasználó az](../conditional-access/require-tou.md) alkalmazáshoz való hozzáférés megkezdése előtt jóváhagyja ezeket a feltételeket.

## <a name="privileged-access-lifecycle"></a>Emelt szintű hozzáférés életciklusa

A rendszerjogosultságú hozzáférést a többi szállító a személyazonosság szabályozásának külön funkciójaként írja le. A Microsoft azonban úgy gondolja, hogy a privilegizált hozzáférés szabályozása kulcsfontosságú része az identitás szabályozásának – különösen, ha az ilyen rendszergazdai jogokkal való visszaélés lehetséges, a szervezet számára is okozhat. A rendszergazdai jogosultságokat igénybe vevő alkalmazottakat, szállítókat és alvállalkozókat szabályozni kell.

![Emelt szintű hozzáférés életciklusa](./media/identity-governance-overview/privileged-access-lifecycle.png)

A Azure AD Privileged Identity Management (PIM) további vezérlőket biztosít a hozzáférési jogosultságok biztosításához az erőforrásokhoz, az Azure AD-ben, az Azure-ban és más Microsoft Online-szolgáltatásokban.  A többtényezős hitelesítés és a feltételes hozzáférés terén az Azure AD PIM által biztosított igény szerinti hozzáférés-vezérlési és szerepkör-változási riasztások széles körű irányítási funkciókat biztosítanak a vállalati erőforrások biztonságossá tételéhez (címtár, Office 365-és Azure-erőforrás-szerepkörök). A más hozzáférési formákhoz hasonlóan a szervezetek hozzáférési felülvizsgálatokat is használhatnak a rendszergazdai szerepkörökben lévő összes felhasználóra vonatkozó ismétlődő hozzáférés-visszaigazolások konfigurálásához.

## <a name="getting-started"></a>Első lépések

Habár nincs tökéletes megoldás vagy javaslat minden ügyfélhez, az alábbi konfigurációk ismertetik, hogy a Microsoft milyen alapelveket ajánl a biztonságosabb és produktív munkaerő biztosításához.

- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Emelt szintű hozzáférés biztonsága](../users-groups-roles/directory-admin-roles-secure.md)

A Azure Portal **identitás** -irányításának első lépések lapján is megtekintheti a jogosultsági felügyelet, a hozzáférési felülvizsgálatok, a Privileged Identity Management és a használati feltételek használatának megkezdéséhez szükséges lépéseket.

![A személyazonosság szabályozása – első lépések](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>További lépések

- [Mi az Azure AD-jogosultságok kezelése? előzetes verziója?](entitlement-management-overview.md)
- [Mik azok az Azure AD-hozzáférési felülvizsgálatok?](access-reviews-overview.md)
- [Mi az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Mire használhatom a Használati feltételek funkciót?](active-directory-tou.md)
