---
title: Áttekintés a felhasználói fiókok az Azure Active Directory B2C |} A Microsoft Docs
description: További információ az Azure Active Directory B2C felhasználói fiókokat.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2354bf02b47c9fbc74dbc0dab07e30ca321279cf
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669916"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói fiókok áttekintése

Az Azure Active Directory (Azure AD) B2C-vel a különböző típusú fiókokat is használhatja. Az Azure Active Directory, Azure Active Directory B2B és Azure Active Directory B2C a típusok felhasználói fiókok, amelyek segítségével megoszthatja.

A következő típusú fiókok érhetők el:

- **Munkahelyi fiók** – munkahelyi fiókkal a bérlőben lévő erőforrások eléréséhez, és a egy rendszergazda szerepkör segítségével felügyelheti a bérlők.
- **Vendégfiók** -Vendég fiókkal csak Microsoft-fiókkal vagy egy használható alkalmazások hozzáféréséhez vagy felügyeletéhez a bérlő Azure Active Directory-felhasználó lehet. 
- **Felhasználói fiók** – végfelhasználói fiók létrejön egy regisztrálási szabályzatot az Azure AD B2C alkalmazások végigkövetésével vagy Azure AD Graph API-val, és a felhasználók számára, hogy az Azure AD B2C-vel regisztrált alkalmazások. 

## <a name="work-account"></a>Munkahelyi fiók

Egy munkahelyi fiókot az Azure AD-alapú minden bérlő esetében ugyanúgy jön létre. Hozzon létre egy munkahelyi fiókot, használhatja az adatokat a [a rövid útmutató: új felhasználók hozzáadása az Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Munkahelyi fiók használatával jön létre a **új felhasználó** választási lehetőség az Azure Portalon.

Egy új munkahelyi fiók hozzáadásakor meg kell fontolja meg a következő beállításokat:

- **Név** és **felhasználónév** – a **neve** tulajdonság tartalmazza a megadott és a felhasználó vezetékneve. A **felhasználónév** azonosítója, amelyet a felhasználó bejelentkezni. A felhasználó neve tartalmazza a teljes tartományban. A tartomány felhasználónév részét kell lennie a kezdeti alapértelmezett tartománynévnek *your tartomanynev.onmicrosoft.com*, vagy olyan ellenőrzött, nem összevont [egyéni tartomány](../active-directory/fundamentals/add-custom-domain.md) neve például  *a contoso.com*.
- **Profil** – a fiók be van állítva egy profil felhasználói adatok használatával. Lehetősége egy Utónév, Vezetéknév, beosztás és részleg neve meg van. A fiók létrehozása után szerkesztheti a profilt.
- **Csoportok** -csoport használata a kezelési feladatok, például licencek hozzárendelése vagy engedélyek számos olyan felhasználók vagy eszközök egyszerre. Az új fiók üzembe egy meglévő [csoport](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) a bérlőben. 
- **Címtárbeli szerepkör** – meg kell adnia a felhasználói fiók rendelkezik a bérlőn belüli erőforrásokhoz való hozzáférési szintjét. Az alábbi jogosultsági szintek érhetők el:

    - **Felhasználói** – a felhasználók hozzáférhetnek a hozzájuk rendelt erőforrásokhoz, de nem tudja kezelni a legtöbb bérlői erőforrásokhoz.
    - **Globális rendszergazda** – a globális rendszergazdák minden bérlői erőforrások teljes hozzáféréssel rendelkeznek.
    - **Korlátozott rendszergazda** – válassza ki a rendszergazdai szerepkör vagy a felhasználó szerepköreit. Választható szerepkörökkel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Hozzon létre egy munkahelyi fiókot

Az alábbi információk használatával hozzon létre egy új munkahelyi fiókot:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_post_users)

### <a name="update-a-user-profile"></a>A felhasználói profil frissítése

Az alábbi információkat használhatja egy felhasználó profiljának frissítése:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

### <a name="reset-a-password-for-a-user"></a>Egy felhasználó jelszavának alaphelyzetbe állítása

Az alábbi információk segítségével a felhasználó jelszavának alaphelyzetbe állítása: 

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

## <a name="guest-user"></a>Vendégfelhasználó

Egy külső felhasználók küldhetnek meghívót a bérlőhöz. Az Azure AD B2C-bérlő vendégfelhasználók jellemzően olyan helyzetben, hogy felügyeleti feladatkörök megosztása. A Vendég fiók használatával egy példa: [egy Azure Active Directory B2B-együttműködés felhasználói tulajdonságainak](../active-directory/b2b/user-properties.md).

Ha a bérlőbe meghívott vendégfelhasználó, meg kell adnia a címzetten, és a egy üzenetet a meghívó leíró e-mail-címét. A meghívó hivatkozás átirányítja a felhasználót, hogy a hozzájárulást kérő lap, a **Ismerkedés** gombot, és az engedélyek a felülvizsgálat elfogadták. Ha egy beérkező e-mail cím nem csatlakozik, a felhasználó által meghívott hitelesítő adatok használatával egy Microsoft-weblapra a hozzájárulást kérő lap is lépjen. A felhasználónak ezután meg kell beváltani a meghívót, ugyanúgy, mint az e-mailben szereplő hivatkozásra kattint. Például: `https://myapps.microsoft.com/B2CTENANTNAME`.

Is használhatja a [Microsoft Graph API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/invitation_post) vendégfelhasználó meghívni.

## <a name="consumer-user"></a>Fogyasztói felhasználó

A fogyasztói felhasználó bejelentkezhet az Azure AD B2C által védett alkalmazások, de például az Azure Portalt Azure erőforrásai nem érhetők el.  A fogyasztói felhasználó használhatja a helyi vagy összevont partnerek, mint a Facebook és Twitter. A felhasználói fiók használatával hozták létre a [regisztrálási vagy bejelentkezési szabályzat](../active-directory-b2c/active-directory-b2c-reference-policies.md).

A végfelhasználói felhasználói fiók létrehozásakor az egyéni felhasználói attribútumok használatával gyűjtött adatokat is megadhat. További információkért lásd: [egyéni attribútumok megadása az Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Használhatja a található információk a **fogyasztói felhasználói fiókokat hozhat létre** szakaszában [az Azure AD Graph API-val](active-directory-b2c-devquickstarts-graph-dotnet.md) hozhat létre Azure AD B2C felhasználói fiókot. Található információk is használhatja a **fogyasztói felhasználói fiókok frissítése** szakaszban is ugyanabban a cikkben a fiók tulajdonságainak kezeléséhez.

### <a name="migrate-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok áttelepítése

Előfordulhat, hogy nincs szüksége bármely identitásszolgáltató Azure AD B2C fogyasztói meglévő felhasználói fiókok áttelepítésére. További információkért lásd: [felhasználóáttelepítés](active-directory-b2c-user-migration.md) vagy [közösségi identitású felhasználók áttelepítése](active-directory-b2c-social-migration.md).