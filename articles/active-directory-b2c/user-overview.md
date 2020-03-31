---
title: Felhasználói fiókok áttekintése az Azure Active Directory B2C-ben
description: Ismerje meg az Azure Active Directory B2C-ben használható felhasználói fiókok típusait.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9630e1f23b6595ca690ecafcf0c4b9bfff603f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185658"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Felhasználói fiókok áttekintése az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) többféle fiók hozható létre. Az Azure Active Directory, az Active Directory B2B és az Active Directory B2C megosztása a használható felhasználói fiókok típusaiban.

A következő típusú fiókok érhetők el:

- **Munkahelyi fiók** – A munkahelyi fiók hozzáférhet a bérlő erőforrásaihoz, és rendszergazdai szerepkörrel kezelheti a bérlőket.
- **Vendégfiók** – A vendégfiók csak egy Microsoft-fiók vagy egy Azure Active Directory-felhasználó lehet, amely alkalmazások elérésére vagy bérlők kezelésére használható.
- **Fogyasztói fiók** – A fogyasztói fiókot az Azure AD B2C-ben regisztrált alkalmazások felhasználója használja. A fogyasztói fiókokat a következők hozhatják létre:
  - A felhasználó egy Azure AD B2C-alkalmazásban egy regisztrációs felhasználói folyamaton megy keresztül
  - A Microsoft Graph API használata
  - Az Azure Portal használata

## <a name="work-account"></a>Munkahelyi fiók

A munkahelyi fiók ugyanúgy jön létre az Azure AD-n alapuló összes bérlő számára. Munkahelyi fiók létrehozásához használja a rövid útmutatóban található [információkat: Új felhasználók hozzáadása az Azure Active Directoryhoz.](../active-directory/fundamentals/add-users-azure-active-directory.md) A munkahelyi fiók az **Azure Portalon** az Új felhasználó választás használatával jön létre.

Új munkahelyi fiók hozzáadásakor a következő konfigurációs beállításokat kell figyelembe vennie:

- **Név** és **felhasználónév** – A **Név** tulajdonság a felhasználó vezeték- és keresztnevét tartalmazza. A **felhasználónév** az az azonosító, amelyet a felhasználó bead a bejelentkezéshez. A felhasználónév tartalmazza a teljes tartományt. A felhasználónév tartománynév-részének vagy a kezdeti alapértelmezett tartománynévnek *your-domain.onmicrosoft.com,* vagy ellenőrzött, nem összevont [egyéni tartománynévnek](../active-directory/fundamentals/add-custom-domain.md) , például *contoso.com.*
- **Profil** – A fiók felhasználói adatok profiljával van beállítva. Lehetősége van utónév, vezetéknév, beosztás és részlegnév megadására. A profila fiók létrehozása után szerkeszthető.
- **Csoportok** – Csoport használatával felügyeleti feladatokat hajthat végre, például licenceket vagy engedélyeket rendel egyszerre több felhasználóhoz vagy eszközhöz. Az új fiókot a bérlő egy meglévő [csoportjába](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) helyezheti.
- **Címtárszerepkör** – Meg kell adnia a felhasználói fiók által a bérlőerőforrásaihoz való hozzáférés szintjét. A következő jogosultsági szintek érhetők el:

    - **Felhasználó** – A felhasználók hozzáférhetnek a hozzárendelt erőforrásokhoz, de nem kezelhetik a legtöbb bérlői erőforrást.
    - **Globális rendszergazda** – A globális rendszergazdák teljes hozzáféréssel rendelkeznek az összes bérlői erőforrás felett.
    - **Korlátozott rendszergazda** – Válassza ki a felhasználó felügyeleti szerepkörét vagy szerepköreit. A kijelölhető szerepkörökről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

### <a name="create-a-work-account"></a>Munkahelyi fiók létrehozása

A következő információk segítségével hozhat létre új munkahelyi fiókot:

- [Azure-portál](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Felhasználói profil frissítése

A következő információk segítségével frissítheti a felhasználó profilját:

- [Azure-portál](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Felhasználó jelszavának alaphelyzetbe állítása

A következő információk segítségével állíthatja alaphelyzetbe a felhasználó jelszavát:

- [Azure-portál](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Vendégfelhasználó

Külső felhasználókat meghívhat a bérlőbe vendégfelhasználóként. Egy tipikus forgatókönyv egy vendégfelhasználó meghívására az Azure AD B2C bérlő, hogy megosszák a felügyeleti feladatokat. Egy vendégfiók használatával kapcsolatos példa: [Az Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../active-directory/b2b/user-properties.md)című témakörben található.

Amikor meghív egy vendégfelhasználót a bérlőhöz, megadja a címzett e-mail címét és a meghívót leíró üzenetet. A meghívóhivatkozás a felhasználót arra a jóváhagyási lapra viszi, ahol az **Első lépések** gomb ki van jelölve, és az engedélyek ellenőrzése elfogadásra kerül. Ha egy beérkezett üzenetek mappája nincs csatolva az e-mail címhez, a felhasználó a microsoftos lapon a meghívott hitelesítő adatok használatával a hozzájárulási lapra léphet. A felhasználó ezután kénytelen beváltani a meghívást ugyanúgy, mint kattintson a linkre az e-mailben. Például: `https://myapps.microsoft.com/B2CTENANTNAME`.

A [Microsoft Graph API-val](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) vendégfelhasználót is meghívhat.

## <a name="consumer-user"></a>Fogyasztói felhasználó

A fogyasztó felhasználó bejelentkezhet az Azure AD B2C által védett alkalmazásokba, de nem férhet hozzá az Azure-erőforrásokhoz, például az Azure Portalhoz. A fogyasztó felhasználó használhat helyi fiókot vagy összevont fiókokat, például a Facebookot vagy a Twittert. A fogyasztói fiók egy [regisztrációs vagy bejelentkezési felhasználói folyamat,](user-flow-overview.md)a Microsoft Graph API használatával vagy az Azure Portal használatával jön létre.

Megadhatja azokat az adatokat, amelyek akkor gyűjtenek, amikor egy fogyasztói felhasználói fiókot egyéni felhasználói attribútumok használatával hoznak létre. További információ: [Egyéni attribútumok definiálása az Azure Active Directory B2C könyvtárban.](user-flow-custom-attributes.md)

A fogyasztói fiókok kezeléséről az [Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph programmal](manage-user-accounts-graph-api.md)című témakörben talál további információt.

### <a name="migrate-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok áttelepítése

Előfordulhat, hogy át kell telepítenie a meglévő fogyasztói felhasználói fiókokat bármely identitásszolgáltatóról az Azure AD B2C-be. További információ: [A felhasználók áttelepítése az Azure AD B2C-be](user-migration.md)című témakörben talál.
