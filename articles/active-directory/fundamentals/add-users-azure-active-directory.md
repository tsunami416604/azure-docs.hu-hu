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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262112"
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

   - **Név**. Kötelező. Az első és utolsó az új felhasználó neve. Például: *Mary Parker*.

   - **Felhasználónév**. Kötelező. Az új felhasználó felhasználóneve. Például: `mary@contoso.com`.

     A Felhasználónév tartomány részének a kezdeti alapértelmezett tartománynevet kell használnia, *\<yourdomainname >. onmicrosoft. com*vagy egy egyéni tartománynevet, például *contoso.com*. További információ az Egyéni tartománynév létrehozásáról: [Egyéni tartománynév hozzáadása a Azure Active Directory portál használatával](add-custom-domain.md).

   - **Csoportok**. Szükség esetén a felhasználót adhat hozzá egy vagy több meglévő csoportot. A felhasználói csoportokhoz egy későbbi időpontban is hozzáadhat. A felhasználók csoportokba való felvételével kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](active-directory-groups-create-azure-portal.md).

   - **Címtárbeli szerepkör**: Ha az Azure ad rendszergazdai engedélyekre van szüksége a felhasználó számára, hozzáadhat egy Azure ad-szerepkörhöz. A felhasználót hozzárendelheti globális rendszergazdaként, vagy egy vagy több korlátozott rendszergazdai szerepkörrel az Azure AD-ben. A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [szerepkörök társítása a felhasználókhoz](active-directory-users-assign-role-azure-portal.md).

   - **Job info**: további információkat adhat meg a felhasználóról, vagy később is megteheti. A felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [felhasználói profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md).

1. Másolja a **jelszó** mezőben megadott automatikusan generált jelszót. Ezt a jelszót kell megadnia a felhasználónak, hogy első alkalommal jelentkezzen be.

1. Kattintson a **Létrehozás** gombra.

A rendszer létrehozza és hozzáadja a felhasználót az Azure AD-szervezethez.

## <a name="add-a-new-guest-user"></a>Új vendég felhasználó hozzáadása

Azt is megteheti, hogy új vendég felhasználóval együttműködik a szervezettel a **felhasználó meghívása** az **új felhasználó** lapról lehetőség kiválasztásával. Ha a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy Ön is meghívhatja a vendégeket, a felhasználó e-mailben meghívót küld, hogy megkezdődjön az együttműködés. A B2B csoportmunka-felhasználók meghívásával kapcsolatos további információkért lásd: [B2B-felhasználók](../b2b/add-users-administrator.md) meghívása Azure Active Directory

## <a name="add-a-consumer-user"></a>Fogyasztói felhasználó hozzáadása

Lehetnek olyan helyzetek, amikor manuálisan szeretné létrehozni a fogyasztói fiókokat a Azure Active Directory B2C (Azure AD B2C) címtárban. A fogyasztói fiókok létrehozásával kapcsolatos további információkért lásd: [felhasználói felhasználók létrehozása és törlése Azure ad B2Cban](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>A hibrid környezetben új felhasználó hozzáadása

Ha rendelkezik Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) tartalmazó környezet, hozzáadhat új felhasználókat való szinkronizálásával a meglévő felhasználói fiók adatait. További információ a hibrid környezetekről és a felhasználókról: [a helyszíni címtárak integrálása Azure Active Directorysal](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Felhasználó törlése

Egy meglévő felhasználó Azure Active Directory portálon törölheti.

A felhasználók törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjának használatával.

1. Keresse meg és válassza ki a *Azure Active Directory* bármely oldalon.

1. Keresse meg és válassza ki azt a felhasználót, akit törölni szeretne az Azure AD-bérlőből. Például: _Mary Parker_.

1. Válassza a **felhasználó törlése**lehetőséget.

    ![Felhasználók – minden felhasználó oldalon a kiemelt felhasználó törlése](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

A felhasználó törölve lett, és már nem jelenik meg a **felhasználók – minden felhasználó** lapon. A felhasználó a **törölt felhasználók** lapon a következő 30 napra megtekinthető, és ebben az időszakban visszaállítható. További információ a felhasználók visszaállításáról: [nemrég törölt felhasználó visszaállítása vagy eltávolítása Azure Active Directory használatával](active-directory-users-restore.md).

Ha töröl egy felhasználót, a felhasználó által felhasznált összes licenc elérhetővé válik más felhasználók számára.

>[!Note]
>A Windows Server Active Directory használatával frissítenie kell az identitást, a kapcsolattartási adatokat vagy a feladatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. Miután elvégezte a frissítést, meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt a módosítások láthatja.

## <a name="next-steps"></a>Következő lépések

A felhasználók hozzáadása után a következő alapvető folyamatokat végezheti el:

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Alapszintű csoport létrehozása és Tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók használata](../users-groups-roles/groups-create-rule.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a [vendég felhasználóinak egy másik címtárból való hozzáadásával](../b2b/what-is-b2b.md) vagy [egy törölt felhasználó visszaállításával](active-directory-users-restore.md). További információ az egyéb elérhető műveletekről: [Azure Active Directory felhasználói kezelés dokumentációja](../users-groups-roles/index.yml).
