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
ms.date: 08/28/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: a17b64cbfffbe9cabd909bccb16d55c07bba8bb0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241445"
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

A szervezetek az olyan technológiákon keresztül automatizálják a hozzáférési életciklus folyamatát, mint a [dinamikus csoportok](../users-groups-roles/groups-dynamic-membership.md), valamint a scim integrált [SaaS-alkalmazásokhoz](../saas-apps/tutorial-list.md) vagy [alkalmazásokhoz](../manage-apps/use-scim-to-provision-users-and-groups.md)való felhasználói kiépítés.  A szervezetek azt is megadhatják [, hogy mely vendég felhasználók férhetnek hozzá a helyszíni alkalmazásokhoz](../b2b/hybrid-cloud-to-on-premises.md).  Ezek a hozzáférési jogosultságok rendszeresen áttekinthetők az ismétlődő [Azure ad hozzáférési felülvizsgálatok](access-reviews-overview.md)használatával.

Amikor egy felhasználó megpróbál hozzáférni az alkalmazásokhoz, az Azure AD kikényszeríti a [feltételes hozzáférési](/azure/active-directory/conditional-access/) szabályzatokat. A feltételes hozzáférési szabályzatok tartalmazhatják például a használati [feltételek](../conditional-access/terms-of-use.md) megjelenítését, és biztosítják, hogy a felhasználó az alkalmazáshoz való hozzáférés megkezdése előtt [jóváhagyja ezeket a feltételeket](../conditional-access/require-tou.md) .

## <a name="privileged-access-lifecycle"></a>Emelt szintű hozzáférés életciklusa

A rendszerjogosultságú hozzáférést a többi szállító a személyazonosság szabályozásának külön funkciójaként írja le. A Microsoft azonban úgy gondolja, hogy a privilegizált hozzáférés szabályozása kulcsfontosságú része az identitás szabályozásának – különösen, ha az ilyen rendszergazdai jogokkal való visszaélés lehetséges, a szervezet számára is okozhat. A rendszergazdai jogosultságokat igénybe vevő alkalmazottakat, szállítókat és alvállalkozókat szabályozni kell.

![Emelt szintű hozzáférés életciklusa](./media/identity-governance-overview/privileged-access-lifecycle.png)

A Azure AD Privileged Identity Management (PIM) további vezérlőket biztosít a hozzáférési jogosultságok biztosításához az erőforrásokhoz, az Azure AD-ben, az Azure-ban és más Microsoft Online-szolgáltatásokban.  A többtényezős hitelesítés és a feltételes hozzáférés terén az Azure AD PIM által biztosított igény szerinti hozzáférés-vezérlési és szerepkör-változási riasztások széles körű irányítási funkciókat biztosítanak a vállalati erőforrások biztonságossá tételéhez (címtár, Office 365-és Azure-erőforrás-szerepkörök). A más hozzáférési formákhoz hasonlóan a szervezetek hozzáférési felülvizsgálatokat is használhatnak a rendszergazdai szerepkörökben lévő összes felhasználóra vonatkozó ismétlődő hozzáférés-visszaigazolások konfigurálásához.

## <a name="least-privileged-roles"></a>Legkevésbé Kiemelt szerepkörök

Ajánlott eljárás a legkevésbé Kiemelt szerepkör használata a rendszergazdai feladatok elvégzéséhez az Identitáskezelés irányításában. Javasoljuk, hogy az Azure AD PIM használatával aktiválja a szerepköröket a feladatok elvégzéséhez szükséges módon. Az identitás-irányítási funkciók konfigurálásához az alábbi legkevesebb jogosultsággal rendelkező címtár-szerepkört kell megadni: 

| Funkció | Legkevésbé Kiemelt szerepkör |
| ------- | --------------------- |
| Jogosultságkezelés | Felhasználói rendszergazda (a SharePoint Online-helyek katalógusba való felvételének kivételével, amely globális rendszergazdai jogosultságot igényel) |
| Használati feltételek | Biztonsági rendszergazda vagy feltételes hozzáférésű rendszergazda |
| Hozzáférési felülvizsgálatok | Felhasználói rendszergazda (az Azure-beli vagy az Azure AD-szerepkörök hozzáférési felülvizsgálatának kivételével, amelyhez Kiemelt szerepkörű rendszergazda szükséges) |
|Privileged Identity Management | Kiemelt szerepkörgazda |


## <a name="getting-started"></a>Első lépések

Habár nincs tökéletes megoldás vagy javaslat minden ügyfélhez, az alábbi konfigurációk ismertetik, hogy a Microsoft milyen alapelveket ajánl a biztonságosabb és produktív munkaerő biztosításához.

- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Emelt szintű hozzáférés biztonsága](../users-groups-roles/directory-admin-roles-secure.md)

A Azure Portal **identitás-irányításának** első lépések lapján is megtekintheti a jogosultsági felügyelet, a hozzáférési felülvizsgálatok, a Privileged Identity Management és a használati feltételek használatának megkezdéséhez szükséges lépéseket.

![A személyazonosság szabályozása – első lépések](./media/identity-governance-overview/getting-started.png)

Ha visszajelzést szeretne küldeni a személyazonosság-irányítási funkciókról, kattintson a **visszajelzés** küldése lehetőségre a Azure Portal a visszajelzés elküldéséhez. A csapat rendszeresen felülvizsgálja visszajelzéseit.

## <a name="next-steps"></a>További lépések

- [Mi az Azure AD-jogosultságok kezelése? előzetes verziója?](entitlement-management-overview.md)
- [Mik azok az Azure AD-hozzáférési felülvizsgálatok?](access-reviews-overview.md)
- [Mi az Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Mire használhatom a Használati feltételek funkciót?](active-directory-tou.md)
