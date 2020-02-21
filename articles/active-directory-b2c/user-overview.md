---
title: A Azure Active Directory B2C felhasználói fiókjainak áttekintése
description: Tudnivalók a Azure Active Directory B2C használható felhasználói fiókok típusairól.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f34fe324f22be41d8cdff0df67777bc254bbc290
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484264"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C felhasználói fiókjainak áttekintése

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú fiókot hozhat létre. Azure Active Directory, Active Directory B2B és Active Directory B2C megosztás a használható felhasználói fiókok típusaiban.

A következő típusú fiókok érhetők el:

- **Munkahelyi fiók** – a munkahelyi fiók hozzáférhet a bérlő erőforrásaihoz, és rendszergazdai szerepkörrel is kezelheti a bérlőket.
- **Vendégfiók** – a vendég fiók csak olyan Microsoft-fiók vagy Azure Active Directory felhasználó lehet, amely használható az alkalmazások eléréséhez vagy a bérlők kezeléséhez.
- **Fogyasztói fiók** – a Azure ad B2C regisztrált alkalmazások felhasználója használja a fogyasztói fiókot. A felhasználói fiókokat a alábbiakkal hozhatja létre:
  - A felhasználó egy Azure AD B2C alkalmazásban egy regisztrációs felhasználói folyamaton keresztül zajlik
  - Microsoft Graph API használata
  - Az Azure Portal használata

## <a name="work-account"></a>Munkahelyi fiók

A munkahelyi fiókokat ugyanúgy hozza létre a rendszer az összes bérlő számára az Azure AD-on alapuló módon. Munkahelyi fiók létrehozásához használhatja a gyors útmutató [: új felhasználók hozzáadása Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). A munkahelyi fiók az **új felhasználó** választása alapján jön létre a Azure Portal.

Új munkahelyi fiók hozzáadásakor figyelembe kell vennie a következő konfigurációs beállításokat:

- **Név** és **Felhasználónév** – a **Name (név** ) tulajdonság a felhasználó megadott és vezetéknevét tartalmazza. A **Felhasználónév** annak az azonosítónak a neve, amelyet a felhasználó be kell jelentkeznie. A Felhasználónév tartalmazza a teljes tartományt. A Felhasználónév tartománynév részének a kezdeti alapértelmezett tartománynév *Your-Domain.onmicrosoft.com*vagy egy ellenőrzött, nem összevont [Egyéni tartománynévnek](../active-directory/fundamentals/add-custom-domain.md) (például *contoso.com*) kell lennie.
- **Profil** – a fiók felhasználói adatprofillal van beállítva. Lehetősége van megadnia az utónév, a vezetéknév, a beosztás és a részleg nevét. A profilt a fiók létrehozása után módosíthatja.
- **Csoportok** – olyan felügyeleti feladatok elvégzésére, mint például a licencek vagy az engedélyek több felhasználóhoz vagy eszközhöz való hozzárendelésének engedélyezése egy csoport használatával. Az új fiókot egy meglévő [csoportba](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) helyezheti a bérlőben.
- **Directory-szerepkör** – meg kell adnia azt a hozzáférési szintet, amelyet a felhasználói fióknak a bérlő erőforrásaihoz kell használnia. A következő jogosultsági szintek érhetők el:

    - A felhasználók hozzáférhetnek a hozzárendelt erőforrásokhoz, de nem kezelhetik a legtöbb bérlői erőforrást.
    - **Globális rendszergazda** – a globális rendszergazdák teljes hozzáféréssel rendelkeznek az összes bérlői erőforráshoz.
    - **Korlátozott rendszergazda** – válassza ki a felhasználó rendszergazdai szerepkörét vagy szerepköreit. További információ a kiválasztható szerepkörökről: [rendszergazdai szerepkörök kiosztása a Azure Active Directoryban](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Munkahelyi fiók létrehozása

Új munkahelyi fiók létrehozásához a következő információkat használhatja:

- [Azure Portalra](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Felhasználói profil frissítése

A következő információk segítségével frissítheti a felhasználó profilját:

- [Azure Portalra](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Felhasználó jelszavának alaphelyzetbe állítása

A felhasználó jelszavának alaphelyzetbe állításához a következő információkat használhatja:

- [Azure Portalra](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Vendég felhasználó

Meghívhatja a külső felhasználókat a bérlőnek vendég felhasználóként. Egy tipikus példa arra, hogy a vendég felhasználókat meghívja a Azure AD B2C bérlőre, hogy megossza az adminisztrációs feladatokat. A vendég fiók használatára példát a [Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../active-directory/b2b/user-properties.md)című témakörben talál.

Amikor meghívja a vendég felhasználóját a bérlőre, megadja a címzett e-mail-címét, valamint egy, a meghívót leíró üzenetet. A Meghívási hivatkozás a felhasználót a jóváhagyás oldalra viszi, ahol az **első lépések** gomb van kiválasztva, az engedélyek felülvizsgálata pedig elfogadva. Ha a beérkezett fájlok nem az e-mail-címre vannak csatolva, a felhasználó a meghívott hitelesítő adatokkal megnyitva egy Microsoft-oldalra navigálva megkeresheti a beleegyezik lapot. A felhasználónak ezután meg kell váltania a meghívót úgy, hogy az e-mailben szereplő hivatkozásra kattint. Például: `https://myapps.microsoft.com/B2CTENANTNAME`.

Használhatja a [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) -t is egy vendég felhasználó meghívásához.

## <a name="consumer-user"></a>Fogyasztói felhasználó

A felhasználó bejelentkezhet Azure AD B2C által védett alkalmazásokba, de nem fér hozzá az Azure-erőforrásokhoz, például a Azure Portalhoz. A fogyasztói felhasználó használhat helyi fiókot vagy összevont fiókot, például a Facebookot vagy a Twittert. A felhasználói fiók a Microsoft Graph API használatával vagy a Azure Portal használatával jön létre a [regisztrációs vagy bejelentkezési felhasználói folyamat](user-flow-overview.md)használatával.

Megadhatja azokat az adatokat, amelyeket a rendszer akkor gyűjt, amikor egyéni felhasználói attribútumokkal hozza létre a fogyasztói felhasználói fiókot. További információ: [Egyéni attribútumok definiálása a Azure Active Directory B2Cban](user-flow-custom-attributes.md).

A fogyasztói fiókok kezelésével kapcsolatos további információkért lásd: [Azure ad B2C felhasználói fiókok kezelése Microsoft Graphokkal](manage-user-accounts-graph-api.md).

### <a name="migrate-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok migrálása

Előfordulhat, hogy a meglévő felhasználói fiókokat át kell telepítenie bármely identitás-szolgáltatótól a Azure AD B2Cba. További információ: [felhasználók Áttelepítése Azure ad B2Cra](user-migration.md).
