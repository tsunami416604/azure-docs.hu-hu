---
title: Hozzáadhat vagy törölhet az Azure Active Directory-felhasználók – |} A Microsoft Docs
description: Új felhasználók hozzáadása, vagy törli a meglévő felhasználókat az Azure Active Directoryval kapcsolatos utasításokat.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b01eb7730290fbf7340fc0a6d8cac8157498f64a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013617"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Hozzáadhat vagy törölhet a felhasználók Azure Active Directory használatával

Vegyen fel új felhasználókat, vagy törölje a meglévő felhasználókat a Azure Active Directory (Azure AD) szervezetből. Felhasználók hozzáadásához vagy törléséhez felhasználói rendszergazdának vagy globális rendszergazdának kell lennie.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

Létrehozhat egy új felhasználót az Azure Active Directory portál használatával.

### <a name="to-add-a-new-user"></a>Új felhasználó hozzáadása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként a szervezet számára.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki **új felhasználó**.

    ![Felhasználók – minden felhasználó oldalon kiemelve az új felhasználóval](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Az **új felhasználó** lapon válassza a **felhasználó létrehozása** elemet, majd adja hozzá a felhasználó adatait.

    ![Új felhasználó, a felhasználó oldalon a felhasználói adatok hozzáadása](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Név (kötelező)** : az új felhasználó vezetékneve és utóneve. Például: Chris Green.

   - **Felhasználónév (kötelező)** : az új felhasználó felhasználóneve. Például: chris@contoso.com.

     A Felhasználónév tartományának a kezdeti alapértelmezett tartománynevet, <_yourdomainname_>. onmicrosoft. com vagy egy egyéni tartománynevet kell használnia az Azure ad-szervezetben, például a contoso.com-ben. Az elérhető tartományok listájából választhat. A lista szűréséhez a tartománynév egy részét is beírhatja. Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md).

   - **Csoportok**: a felhasználót hozzáadhatja egy vagy több meglévő csoporthoz, vagy később is megteheti. Felhasználói csoportok hozzáadásával kapcsolatos további információkért lásd: [létrehozásához, és tagokat vehet fel](active-directory-groups-create-azure-portal.md).

   - **Címtárbeli szerepkör**: Ha az Azure ad rendszergazdai engedélyekre van szüksége a felhasználó számára, hozzáadhat egy Azure ad-szerepkörhöz. A felhasználót hozzárendelheti globális rendszergazdaként, vagy egy vagy több korlátozott rendszergazdai szerepkörrel az Azure AD-ben. További információ a szerepkörök hozzárendelése: [szerepkörök hozzárendelése a felhasználók](active-directory-users-assign-role-azure-portal.md).

   - **Job info**: további információkat adhat meg a felhasználóról, vagy később is megteheti. Felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [hozzáadása vagy módosítása a felhasználói profil adatainak](active-directory-users-profile-azure-portal.md).

4. A megadott automatikusan létrehozott jelszó másolása a **jelszó** mezőbe. Használhatja a jelszó mezőben megadott automatikusan generált jelszót, vagy létrehozhat egy egyéni jelszót. Kell megadnia ezt a jelszót a felhasználónak a kezdeti bejelentkezési folyamathoz.

5. Kattintson a **Létrehozás** gombra.

A rendszer létrehozza és hozzáadja a felhasználót az Azure AD-szervezethez.

## <a name="add-a-new-guest-user"></a>Új vendég felhasználó hozzáadása

Azt is megteheti, hogy új vendég felhasználóval együttműködik a szervezettel a **felhasználó meghívása** az **új felhasználó** lapról lehetőség kiválasztásával. Ha a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy Ön is meghívhatja a vendégeket, a felhasználó e-mailben meghívót küld, hogy megkezdődjön az együttműködés. A B2B csoportmunka-felhasználók meghívásával kapcsolatos további információkért lásd: [B2B-felhasználók](../b2b/add-users-administrator.md) meghívása Azure Active Directory

## <a name="add-a-consumer-user"></a>Fogyasztói felhasználó hozzáadása

Lehetnek olyan helyzetek, amikor manuálisan szeretné létrehozni a fogyasztói fiókokat a Azure Active Directory B2C (Azure AD B2C) címtárban. A fogyasztói fiókok létrehozásával kapcsolatos további információkért lásd: [felhasználói felhasználók létrehozása és törlése Azure ad B2Cban](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>A hibrid környezetben új felhasználó hozzáadása

Ha rendelkezik Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) tartalmazó környezet, hozzáadhat új felhasználókat való szinkronizálásával a meglévő felhasználói fiók adatait. Hibrid környezetek és a felhasználók kapcsolatos további információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Felhasználó törlése

Egy meglévő felhasználó Azure Active Directory portálon törölheti.

### <a name="to-delete-a-user"></a>Felhasználó törlése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjának használatával.

1. Válassza ki **Azure Active Directory**válassza **felhasználók**, és keressen rá, és válassza ki a felhasználót az Azure AD-bérlőjéből törölni szeretné. Ha például _Mary Parker_.

1. Válassza ki **felhasználó törlése**.

    ![Felhasználók – minden felhasználó oldalon a kiemelt felhasználó törlése](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    A felhasználó törlődik, és már nem jelenik meg a **felhasználók – minden felhasználó** lapot. A felhasználó láthatók a **törölt felhasználók** lapon a következő 30 napra, és ez idő alatt vissza tudja állítani. A felhasználó visszaállításával kapcsolatos további információkért lásd: [visszaállítása, vagy véglegesen a közelmúltban törölt felhasználó eltávolítása](active-directory-users-restore.md). Ha töröl egy felhasználót, a felhasználó által felhasznált összes licenc elérhetővé válik más felhasználók számára.

    >[!Note]
    >Az identitás, a kapcsolattartási adatok vagy a feladat adatainak a felhasználók számára, akiknek mérvadó forrás a Windows Server Active Directory frissítéséhez a Windows Server Active Directory kell használnia. Miután elvégezte a frissítést, meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt a módosítások láthatja.

## <a name="next-steps"></a>Következő lépések

Miután hozzáadta a felhasználókat, a következő alapszintű folyamatok hajthatja végre:

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók használata](../users-groups-roles/groups-create-rule.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a [vendég felhasználóinak egy másik Azure ad-szervezetből való hozzáadásával](../b2b/what-is-b2b.md) vagy [egy törölt felhasználó visszaállításával](active-directory-users-restore.md). Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
