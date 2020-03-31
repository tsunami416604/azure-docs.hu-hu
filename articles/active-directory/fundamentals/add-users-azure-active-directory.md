---
title: Felhasználók hozzáadása vagy törlése – Azure Active Directory | Microsoft dokumentumok
description: Útmutató az új felhasználók hozzáadásához vagy a meglévő felhasználók törléséhez az Azure Active Directory használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262112"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Felhasználók hozzáadása vagy törlése az Azure Active Directory használatával

Új felhasználók hozzáadása vagy meglévő felhasználók törlése az Azure Active Directory (Azure AD) szervezetből. Felhasználók hozzáadásához vagy törléséhez felhasználó vagy globális rendszergazda kell, hogy legyen.

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása

Új felhasználót hozhat létre az Azure Active Directory portálhasználatával.

Új felhasználó hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) a szervezet felhasználói rendszergazdájaként.

1. Keresse meg, és válassza ki az *Azure Active Directory* bármely oldalon.

1. Válassza a **Felhasználók**lehetőséget, majd az **Új felhasználó**lehetőséget.

    ![Felhasználó hozzáadása a felhasználókon keresztül – Az Azure AD összes felhasználója](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. A **Felhasználó** lapon adja meg a felhasználó adatait:

   - **Név**. Kötelező. Az új felhasználó vezeték- és keresztneve. Például *Mary Parker*.

   - **Felhasználónév**. Kötelező. Az új felhasználó felhasználóneve. Például: `mary@contoso.com`.

     A felhasználónév tartományrészének vagy a kezdeti alapértelmezett tartománynevet, *contoso.com* * \<a yourdomainname>.onmicrosoft.com*nevet, vagy egy egyéni tartománynevet ( például contoso.com ) kell használnia. Az egyéni tartománynevek létrehozásáról az [Egyéni tartománynév hozzáadása az Azure Active Directory portálon című témakörben](add-custom-domain.md)talál további információt.

   - **Csoportok**. Szükség esetén hozzáadhatja a felhasználót egy vagy több meglévő csoporthoz. A felhasználót később is hozzáadhatja a csoportokhoz. A felhasználók csoportokhoz való hozzáadásáról további információt az [Alapcsoport létrehozása és tagok hozzáadása az Azure Active Directory használatával című témakörben talál.](active-directory-groups-create-azure-portal.md)

   - **Címtárszerepkör:** Ha a felhasználónak Azure AD felügyeleti engedélyeket kell megadnia, hozzáadhatja őket egy Azure AD-szerepkörhöz. Hozzárendelheti a felhasználót globális rendszergazdának vagy az Azure AD egy vagy több korlátozott rendszergazdai szerepkörének. A szerepkörök hozzárendeléséről a [Szerepkörök felhasználókhoz rendelése](active-directory-users-assign-role-azure-portal.md)című témakörben talál további információt.

   - **Feladatadatai**: A felhasználóval kapcsolatos további információkat itt adhat meg, vagy később is megteheti. A felhasználói adatok hozzáadásáról a [Felhasználói profil adatainak hozzáadása és módosítása](active-directory-users-profile-azure-portal.md)című témakörben talál további információt.

1. Másolja a **Jelszó** mezőben megadott automatikusan létrehozott jelszót. Ezt a jelszót meg kell adnia a felhasználónak az első bejelentkezéshez.

1. Kattintson a **Létrehozás** gombra.

A felhasználó jön létre, és hozzáadódnak az Azure AD-szervezethez.

## <a name="add-a-new-guest-user"></a>Új vendégfelhasználó hozzáadása

Az új vendégfelhasználót is meghívhat, hogy működjön együtt a szervezettel, ha az **Új felhasználó** lap Felhasználó meghívása lehetőséget **választja.** Ha a szervezet külső együttműködési beállításai úgy vannak beállítva, hogy meghívhassa a vendégeket, a rendszer e-mailben küldi el a felhasználónak az együttműködést megkezdeni ük általuk elfogadandó meghívót. A B2B-együttműködési felhasználók meghívásáról a [B2B-felhasználók meghívása az Azure Active Directoryba](../b2b/add-users-administrator.md) című témakörben talál további információt.

## <a name="add-a-consumer-user"></a>Fogyasztói felhasználó hozzáadása

Előfordulhatnak olyan forgatókönyvek, amelyekben manuálisan szeretné létrehozni a fogyasztói fiókokat az Azure Active Directory B2C (Azure AD B2C) címtárban. A fogyasztói fiókok létrehozásáról további információt a [Fogyasztói felhasználók létrehozása és törlése az Azure AD B2C-ben című témakörben talál.](../../active-directory-b2c/manage-users-portal.md)

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Új felhasználó hozzáadása hibrid környezetben

Ha az Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) környezettel is rendelkezik, a meglévő felhasználói fiókadatok szinkronizálásával új felhasználókat vehet fel. A hibrid környezetekről és a felhasználókról a [Helyszíni könyvtárak integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)című témakörben talál további információt.

## <a name="delete-a-user"></a>Felhasználó törlése

Az Azure Active Directory portálhasználatával törölheti a meglévő felhasználókat.

Felhasználó törléséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjával.

1. Keresse meg, és válassza ki az *Azure Active Directory* bármely oldalon.

1. Keresse meg és válassza ki a törölni kívánt felhasználót az Azure AD-bérlőből. Például _Mary Parker_.

1. Válassza a **Felhasználó törlése** elemet.

    ![Felhasználók – Minden felhasználó lap kiemelt Felhasználó törlése](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

A felhasználó törlődik, és már nem jelenik meg a **Felhasználók – Minden felhasználó** lapon. A felhasználó látható a **Törölt felhasználók** oldalon a következő 30 napban, és vissza lehet állítani ez idő alatt. A felhasználó visszaállításáról további információt a [Nemrég törölt felhasználó visszaállítása vagy eltávolítása az Azure Active Directory használatával című témakörben talál.](active-directory-users-restore.md)

A felhasználó törlésekor a felhasználó által felhasznált licencek más felhasználók számára is elérhetővé válikk.

>[!Note]
>A Windows Server Active Directory használatával frissítenie kell az identitás-, kapcsolattartási adatokat vagy feladatadatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. A frissítés befejezése után meg kell várnia a következő szinkronizálási ciklus befejezését, mielőtt megjelennek a módosítások.

## <a name="next-steps"></a>További lépések

A felhasználók hozzáadása után a következő alapvető folyamatokat teheti meg:

- [Profiladatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók munkája](../users-groups-roles/groups-create-rule.md)

Vagy más felhasználókezelési feladatokat is elvégezhet, például [vendégfelhasználókat adhat hozzá egy másik könyvtárból,](../b2b/what-is-b2b.md) vagy [visszaállíthat egy törölt felhasználót.](active-directory-users-restore.md) Az egyéb elérhető műveletekről az [Azure Active Directory felhasználói felügyeleti dokumentációjában](../users-groups-roles/index.yml)olvashat bővebben.
