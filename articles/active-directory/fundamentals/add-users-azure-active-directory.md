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
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073507"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Hozzáadhat vagy törölhet a felhasználók Azure Active Directory használatával

Vegyen fel új felhasználókat, vagy törölje a meglévő felhasználókat a Azure Active Directory (Azure AD) szervezetből. Felhasználók hozzáadásához vagy törléséhez felhasználói rendszergazdának vagy globális rendszergazdának kell lennie.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

Létrehozhat egy új felhasználót az Azure Active Directory portál használatával.

Új felhasználó hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként a szervezet számára.

1. Keresse meg és válassza ki a *Azure Active Directory* bármely oldalon.

1. Válassza a **felhasználók**lehetőséget, majd válassza az **új felhasználó**lehetőséget.

    ![Felhasználó hozzáadása felhasználókon keresztül – az Azure AD összes felhasználója](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. A **felhasználó** lapon adja meg a felhasználó adatait:

   - **Név**. Kötelező. Az első és utolsó az új felhasználó neve. Ha például *Mary Parker*.

   - **Felhasználónév**. Kötelező. Az új felhasználó felhasználóneve. Például: `mary@contoso.com`.

     A Felhasználónév tartomány részének a kezdeti alapértelmezett tartománynevet kell használnia, *\<yourdomainname >. onmicrosoft. com*vagy egy egyéni tartománynevet, például *contoso.com*. További információ az Egyéni tartománynév létrehozásáról: [Egyéni tartománynév hozzáadása a Azure Active Directory portál használatával](add-custom-domain.md).

   - **Csoportok**. Szükség esetén a felhasználót adhat hozzá egy vagy több meglévő csoportot. A felhasználói csoportokhoz egy későbbi időpontban is hozzáadhat. A felhasználók csoportokba való felvételével kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](active-directory-groups-create-azure-portal.md).

   - **Címtárbeli szerepkör**: Ha az Azure ad rendszergazdai engedélyekre van szüksége a felhasználó számára, hozzáadhat egy Azure ad-szerepkörhöz. A felhasználót hozzárendelheti globális rendszergazdaként, vagy egy vagy több korlátozott rendszergazdai szerepkörrel az Azure AD-ben. További információ a szerepkörök hozzárendelése: [szerepkörök hozzárendelése a felhasználók](active-directory-users-assign-role-azure-portal.md).

   - **Job info**: további információkat adhat meg a felhasználóról, vagy később is megteheti. Felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [hozzáadása vagy módosítása a felhasználói profil adatainak](active-directory-users-profile-azure-portal.md).

1. Másolja a **jelszó** mezőben megadott automatikusan generált jelszót. Ezt a jelszót kell megadnia a felhasználónak, hogy első alkalommal jelentkezzen be.

1. Kattintson a **Létrehozás** gombra.

A rendszer létrehozza és hozzáadja a felhasználót az Azure AD-szervezethez.

## <a name="add-a-new-guest-user"></a>Új vendég felhasználó hozzáadása

Azt is megteheti, hogy új vendég felhasználóval együttműködik a szervezettel a **felhasználó meghívása** az **új felhasználó** lapról lehetőség kiválasztásával. Ha a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy Ön is meghívhatja a vendégeket, a felhasználó e-mailben meghívót küld, hogy megkezdődjön az együttműködés. A B2B csoportmunka-felhasználók meghívásával kapcsolatos további információkért lásd: [B2B-felhasználók](../b2b/add-users-administrator.md) meghívása Azure Active Directory

## <a name="add-a-consumer-user"></a>Fogyasztói felhasználó hozzáadása

Lehetnek olyan helyzetek, amikor manuálisan szeretné létrehozni a fogyasztói fiókokat a Azure Active Directory B2C (Azure AD B2C) címtárban. A fogyasztói fiókok létrehozásával kapcsolatos további információkért lásd: [felhasználói felhasználók létrehozása és törlése Azure ad B2Cban](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>A hibrid környezetben új felhasználó hozzáadása

Ha rendelkezik Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) tartalmazó környezet, hozzáadhat új felhasználókat való szinkronizálásával a meglévő felhasználói fiók adatait. Hibrid környezetek és a felhasználók kapcsolatos további információkért lásd: [a helyszíni címtárak integrálása az Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Felhasználó törlése

Egy meglévő felhasználó Azure Active Directory portálon törölheti.

A felhasználók törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjának használatával.

1. Keresse meg és válassza ki a *Azure Active Directory* bármely oldalon.

1. Keresse meg és válassza ki azt a felhasználót, akit törölni szeretne az Azure AD-bérlőből. Ha például _Mary Parker_.

1. Válassza ki **felhasználó törlése**.

    ![Felhasználók – minden felhasználó oldalon a kiemelt felhasználó törlése](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

A felhasználó törlődik, és már nem jelenik meg a **felhasználók – minden felhasználó** lapot. A felhasználó láthatók a **törölt felhasználók** lapon a következő 30 napra, és ez idő alatt vissza tudja állítani. További információ a felhasználók visszaállításáról: [nemrég törölt felhasználó visszaállítása vagy eltávolítása Azure Active Directory használatával](active-directory-users-restore.md).

Ha töröl egy felhasználót, a felhasználó által felhasznált összes licenc elérhetővé válik más felhasználók számára.

>[!Note]
>A Windows Server Active Directory használatával frissítenie kell az identitást, a kapcsolattartási adatokat vagy a feladatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. Miután elvégezte a frissítést, meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt a módosítások láthatja.

## <a name="next-steps"></a>További lépések

A felhasználók hozzáadása után a következő alapvető folyamatokat végezheti el:

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók használata](../users-groups-roles/groups-create-rule.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a [vendég felhasználóinak egy másik címtárból való hozzáadásával](../b2b/what-is-b2b.md) vagy [egy törölt felhasználó visszaállításával](active-directory-users-restore.md). Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
