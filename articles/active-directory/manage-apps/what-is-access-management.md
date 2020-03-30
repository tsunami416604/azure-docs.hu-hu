---
title: Alkalmazásokhoz való hozzáférés kezelése az Azure AD használatával | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Azure Active Directory hogyan engedélyezi a szervezetek számára, hogy megadják azokat az alkalmazásokat, amelyekhez az egyes felhasználók hozzáférhetnek.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409066"
---
# <a name="managing-access-to-apps"></a>Alkalmazásokhoz való hozzáférés kezelése

A folyamatos hozzáférés-kezelés, a használat kiértékelése és a jelentéskészítés továbbra is kihívást jelent, miután egy alkalmazás integrálva van a szervezet identitásrendszerébe. Sok esetben az informatikai rendszergazdáknak vagy az ügyfélszolgálatnak folyamatos aktív szerepet kell vállalniuk az alkalmazásokhoz való hozzáférés kezelésében. Néha a hozzárendelést egy általános vagy megosztott informatikai csapat végzi. A hozzárendelési döntést gyakran az üzleti döntéshozónak kell delegálni, és az informatikai részleg hozzárendelése előtt jóváhagyásra van szükség.  Más szervezetek befektetnek egy meglévő automatikus identitás- és hozzáférés-kezelő rendszerrel való integrációba, például a szerepköralapú hozzáférés-vezérlésbe (RBAC) vagy az attribútumalapú hozzáférés-vezérlésbe (ABAC). Mind az integráció és a szabály fejlesztés általában speciális és drága. A vagy irányítási megközelítés nyomon követése vagy jelentése saját különálló, költséges és összetett befektetés.

## <a name="how-does-azure-active-directory-help"></a>Hogyan segít az Azure Active Directory?

Az Azure AD széles körű hozzáférés-kezelést biztosít a konfigurált alkalmazásokhoz, lehetővé téve a szervezetek számára, hogy könnyen elérjék a megfelelő hozzáférési házirendeket az automatikus, attribútumalapú hozzárendeléstől (ABAC vagy RBAC-forgatókönyvek) a delegálásig és a delegálásig rendszergazdai kezelés. Az Azure AD segítségével egyszerűen összetett szabályzatokat érhet el, amelyek egyetlen alkalmazáshoz több felügyeleti modellt kombinálnak, és akár újra felishasználhatják a felügyeleti szabályokat az alkalmazások között ugyanazon közönséggel.

Az Azure AD-vel a használati és hozzárendelési jelentések teljes mértékben integrálva vannak, így a rendszergazdák egyszerűen jelenthetik a hozzárendelési állapotot, a hozzárendelési hibákat és még a használatot is.

### <a name="assigning-users-and-groups-to-an-app"></a>Felhasználók és csoportok hozzárendelése alkalmazáshoz

Az Azure AD alkalmazás-hozzárendelése két elsődleges hozzárendelési módra összpontosít:

* **Egyéni hozzárendelés** A globális címtárengedélyekkel rendelkező informatikai rendszergazda kijelölheti az egyes felhasználói fiókokat, és hozzáférést biztosíthat számukra az alkalmazáshoz.

* **Csoportalapú hozzárendelés (Azure AD Premium P1 vagy P2 szükséges)** A globális címtárengedélyekkel rendelkező informatikai rendszergazda csoportot rendelhet az alkalmazáshoz. Az egyes felhasználók hozzáférését az határozza meg, hogy tagjai-e a csoportnak az alkalmazás elérésének megkísérlésekekén. Más szóval a rendszergazda hatékonyan létrehozhat egy hozzárendelési szabályt, amely kimondja, hogy "a hozzárendelt csoport bármely jelenlegi tagja hozzáfér az alkalmazáshoz". Ezzel a hozzárendelési beállítással a rendszergazdák igénybe vehetik az Azure AD csoportfelügyeleti lehetőségek bármelyikét, beleértve [az attribútumalapú dinamikus csoportokat,](../fundamentals/active-directory-groups-create-azure-portal.md)a külső rendszercsoportokat (például a helyszíni Active Directory vagy a Workday) vagy a rendszergazda által felügyelt vagy önkiszolgáló által felügyelt csoportokat. Egyetlen csoport egyszerűen hozzárendelhető több alkalmazáshoz, így biztosíthatja, hogy a hozzárendelési affinitással rendelkező alkalmazások megoszthassák a hozzárendelési szabályokat, csökkentve ezzel az általános felügyeleti összetettséget. Vegye figyelembe, hogy a beágyazott csoporttagságok jelenleg nem támogatottak az alkalmazásokhoz való csoportalapú hozzárendeléshez.

A két hozzárendelési mód használatával a rendszergazdák bármilyen kívánatos hozzárendelés-kezelési megközelítést elérhetnek.

### <a name="requiring-user-assignment-for-an-app"></a>Felhasználói hozzárendelés megkövetelése egy alkalmazáshoz

Bizonyos típusú alkalmazások esetében megkövetelheti a [felhasználóktól, hogy hozzárendeljék őket az alkalmazáshoz.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) Ezzel megakadályozhatja, hogy mindenki bejelentkezzen, kivéve azokat a felhasználókat, akiket kifejezetten hozzárendel az alkalmazáshoz. A következő típusú alkalmazások támogatják ezt a lehetőséget:

* SAML-alapú hitelesítéssel rendelkező egyszeri bejelentkezésre (SSO) konfigurált alkalmazások
* Az Azure Active Directory előhitelesítését használó alkalmazásproxy-alkalmazások
* Az Azure AD alkalmazásplatformra épülő alkalmazások, amelyek OAuth 2.0 / OpenID Connect hitelesítést használnak, miután egy felhasználó vagy rendszergazda hozzájárult az adott alkalmazáshoz. Egyes nagyvállalati alkalmazások további szabályozást biztosítanak a bejelentkezésre.

Ha nincs szükség a felhasználó-hozzárendelésre, a hozzá nem rendelt felhasználók nem látják az alkalmazást a Saját alkalmazások hozzáférési panelen, de továbbra is bejelentkezhetnek az alkalmazásba (más néven SP által kezdeményezett bejelentkezés), vagy használhatják a **Felhasználói hozzáférés URL-címét** az alkalmazás *not required* **tulajdonságai** lapján (más néven IDP által kezdeményezett bejelentkezés).

Egyes alkalmazások esetében a felhasználói hozzárendelés megkövetelésének lehetősége nem érhető el az alkalmazás tulajdonságai között. Ezekben az esetekben a PowerShell segítségével beállíthatja az appRoleAssignmentRequired tulajdonságot a szolgáltatásnéven.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Az alkalmazások elérésével kapcsolatos felhasználói élmény meghatározása

Az Azure AD számos testreszabható módot kínál [az alkalmazások telepítésére](end-user-experiences.md) a szervezet végfelhasználói számára:

* Az Azure AD Saját alkalmazások hozzáférési panel
* Az Office 365 alkalmazásindítója
* Közvetlen bejelentkezés összevont alkalmazásokba (service-pr)
* Mélyhivatkozások az összevont, jelszóalapú vagy meglévő alkalmazásokhoz

Meghatározhatja, hogy a vállalati alkalmazáshoz hozzárendelt felhasználók láthatják-e a hozzáférési panelen és az Office 365 alkalmazásindítójában.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Példa: Komplex alkalmazás-hozzárendelés az Azure AD-vel
Fontolja meg egy alkalmazás, mint a Salesforce. Számos szervezetben a Salesforce-ot elsősorban a marketing- és értékesítési csapatok használják. A marketingcsapat tagjai gyakran magas szintű jogosultsággal rendelkeznek a Salesforce-hoz, míg az értékesítési csapat tagjai korlátozott hozzáféréssel rendelkeznek. Sok esetben az információs dolgozók széles köre korlátozta az alkalmazáshoz való hozzáférést. Az e szabályok alóli kivételek bonyolítják a dolgokat. A marketing- vagy értékesítési vezetői csapatok gyakran előjoga, hogy a felhasználói hozzáférés biztosításához vagy szerepkörük megváltoztatásához az általános szabályoktól függetlenül módosítsanak.

Az Azure AD-vel az olyan alkalmazások, mint a Salesforce, előre konfigurálhatók egyszeri bejelentkezéshez (Egyszeri bejelentkezés) és automatikus kiépítéshez. Az alkalmazás konfigurálása után a rendszergazda egyszeri művelettel hozhatja létre és rendelheti hozzá a megfelelő csoportokat. Ebben a példában a rendszergazda a következő hozzárendeléseket hajthatja végre:

* [A dinamikus csoportok](../fundamentals/active-directory-groups-create-azure-portal.md) definiálhatók úgy, hogy automatikusan képviseljék a marketing- és értékesítési csoportok összes tagját olyan attribútumok használatával, mint a részleg vagy a szerepkör:
  
  * A marketingcsoportok minden tagja a Salesforce "marketing" szerepköréhez rendelődne.
  * Az értékesítési csoport csoportok minden tagja a Salesforce "értékesítés" szerepköréhez lesz hozzárendelve. Egy további finomítás több csoportot is használhat, amelyek különböző Salesforce-szerepkörökhöz rendelt regionális értékesítési csoportokat képviselnek.

* A kivételmechanizmus engedélyezéséhez minden szerepkörhöz létrehozható egy önkiszolgáló csoport. A "Salesforce marketing kivétel" csoport például önkiszolgáló csoportként hozható létre. A csoport hozzárendelhető a Salesforce marketing szerepkörhöz, és a marketingvezetői csapat tulajdonossá tehető. A marketingvezetői csoport tagjai felhasználókat vehetnek fel vagy távolíthatnak el, csatlakozási szabályzatot állíthatnak be, vagy akár jóváhagyhatják vagy megtagadhatják az egyes felhasználók csatlakozási kérelmét. Ezt a mechanizmust egy megfelelő információs munkatárs idomítása támogatja, amely nem igényel speciális képzést a tulajdonosok vagy a tagok számára.

Ebben az esetben az összes hozzárendelt felhasználó automatikusan kilesz építve a Salesforce-ba, mivel különböző csoportokhoz adják hozzá őket, hogy a szerepkör-hozzárendelésük frissüljön a Salesforce-ban. A felhasználók felfedezhetik és elérhetik a Salesforce-ot a Microsoft alkalmazás-hozzáférési panelen, az Office webes ügyfelein keresztül, vagy akár a szervezeti Salesforce bejelentkezési lapjára navigálva. A rendszergazdák az Azure AD-jelentések használatával könnyedén megtekinthetik a használatot és a hozzárendelési állapotot.

A rendszergazdák azure [AD feltételes hozzáférést](../active-directory-conditional-access-azure-portal.md) alkalmazhatnak az adott szerepkörök hozzáférési szabályzatának beállításához. Ezek a házirendek magukban foglalhatják, hogy a hozzáférés engedélyezett a vállalati környezeten kívül, és még a többtényezős hitelesítés vagy az eszköz követelmények eléréséhez különböző esetekben.

## <a name="access-to-microsoft-applications"></a>Hozzáférés a Microsoft-alkalmazásokhoz

A Microsoft-alkalmazások (például az Office 365 Exchange, a SharePoint, a Yammer stb.) hozzárendelése és kezelése kicsit eltérő, mint a harmadik féltől származó SaaS-alkalmazások vagy az Azure AD-vel egyetlen bejelentkezéshez integrálott alkalmazások.

A felhasználók három fő módon férhetnek hozzá egy Microsoft által közzétett alkalmazáshoz.

- Az Office 365-ben vagy más fizetős csomagokban található alkalmazások esetében a felhasználók **licenc-hozzárendeléssel** vagy közvetlenül a felhasználói fiókjukhoz, vagy a csoportalapú licenckiosztási képességünket használó csoporton keresztül kapnak hozzáférést.
- A Microsoft vagy egy harmadik fél által bárki számára szabadon közzétett alkalmazások esetében a felhasználók [felhasználói hozzájárulással](configure-user-consent.md)kaphatnak hozzáférést. Ez azt jelenti, hogy az Azure AD Work vagy school fiókjukkal jelentkeznek be az alkalmazásba, és lehetővé teszik számára, hogy hozzáférjen a fiókjukon lévő bizonyos korlátozott adathalmazhoz.
- A Microsoft vagy egy harmadik fél által bárki számára szabadon közzétett alkalmazások esetében a felhasználók [rendszergazdai hozzájárulással](manage-consent-requests.md)is hozzáférhetnek. Ez azt jelenti, hogy a rendszergazda megállapította, hogy az alkalmazást a szervezet minden tagja használhat, ezért globális rendszergazdai fiókkal jelentkeznek be az alkalmazásba, és hozzáférést biztosítanak a szervezet minden tagjának.

Egyes alkalmazások egyesítik ezeket a módszereket. Bizonyos Microsoft-alkalmazások például egy Office 365-előfizetés részét képezik, de ehhez ehhez is szükség van.

A felhasználók az Office 365-alkalmazásokat az Office 365-portáljaikon keresztül érhetik el. Az Office 365-alkalmazásokat a Saját alkalmazások hozzáférési panelen is megjelenítheti vagy elrejtheti, ha az [Office 365 láthatósága bevan kapcsolva](hide-application-from-user-portal.md) a címtár **felhasználói beállításai**között. 

A vállalati alkalmazásokhoz ugyanúgy [hozzárendelheti](assign-user-or-group-access-portal.md) a felhasználókat bizonyos Microsoft-alkalmazásokhoz az Azure Portalon keresztül, vagy ha a portálbeállítás nem érhető el, a PowerShell használatával.

## <a name="next-steps"></a>További lépések
* [Alkalmazások védelme feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md)
* [Önkiszolgáló csoportmenedzsment/SSAA](../users-groups-roles/groups-self-service-management.md)
