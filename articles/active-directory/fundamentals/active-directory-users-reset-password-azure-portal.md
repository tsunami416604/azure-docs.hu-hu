---
title: Felhasználó jelszavának alaphelyzetbe állítása – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a felhasználó jelszavának alaphelyzetbe állításához az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032664"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Felhasználó jelszavának alaphelyzetbe állítása az Azure Active Directory használatával

Rendszergazdaként alaphelyzetbe állíthatja a felhasználó jelszavát, ha a jelszó elfelejtődik, ha a felhasználó kiszorul egy eszközből, vagy ha a felhasználó soha nem kapott jelszót.

>[!Note]
>Kivéve, ha az Azure AD-bérlő a felhasználó kezdőkönyvtára, nem fogja tudni alaphelyzetbe állítani a jelszavát. Ez azt jelenti, hogy ha a felhasználó egy másik szervezetből, Microsoft-fiókból vagy Google-fiókból származó fiókkal jelentkezik be a szervezetbe, akkor nem tudja visszaállítani a jelszavát.<br><br>Ha a felhasználó windows Server Active Directory ként rendelkezik jogosultságforrással, csak akkor állíthatja vissza a jelszót, ha bekapcsolta a jelszó-visszaírást.<br><br>Ha a felhasználó rendelkezik egy külső Azure AD-szolgáltató, nem fogja tudni alaphelyzetbe állítani a jelszót. Csak a felhasználó, vagy egy adminsitrator külső Azure AD, alaphelyzetbe állíthatja a jelszót.

>[!Note]
>Ha ön nem rendszergazda, és ehelyett a saját munkahelyi vagy iskolai jelszavának alaphelyzetbe állításával kapcsolatos utasításokat keresi, olvassa el a Munkahelyi vagy iskolai jelszó visszaállítása című [témakört.](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="to-reset-a-password"></a>Jelszó alaphelyzetbe állítása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) felhasználói rendszergazdaként vagy jelszó-rendszergazdaként. A rendelkezésre álló szerepkörökről további információt a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban című témakörben talál.](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, keresse meg és jelölje ki az alaphelyzetbe állítást igényelő felhasználót, majd válassza a **Jelszó alaphelyzetbe állítása lehetőséget.**

    Az **Alain Charon - Profil** lap a Jelszó **alaphelyzetbe állítása** beállítással jelenik meg.

    ![A felhasználó profillapja, kiemelve a Jelszó alaphelyzetbe állítása beállítással](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. A **Jelszó alaphelyzetbe állítása** lapon válassza a **Jelszó alaphelyzetbe állítása lehetőséget.**

    > [!Note]
    > Az Azure Active Directory használatakor egy ideiglenes jelszó automatikusan létrejön a felhasználó számára. Ha az Active Directoryt a helyszínen használja, a felhasználó jelszavát hozza létre.

4. Másolja a jelszót, és adja át a felhasználónak. A felhasználónak módosítania kell a jelszót a következő bejelentkezési folyamat során.

    >[!Note]
    >Az ideiglenes jelszó soha nem jár le. A következő alkalommal, amikor a felhasználó bejelentkezik, a jelszó továbbra is működni fog, függetlenül attól, hogy mennyi idő telt el az ideiglenes jelszó létrehozása óta.

## <a name="next-steps"></a>További lépések

Miután visszaállította a felhasználó jelszavát, a következő alapvető folyamatokat hajthatja végre:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profiladatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

Összetettebb felhasználói forgatókönyveket is végrehajthat, például meghatalmazottakat, házirendeket használhat, és megoszthat felhasználói fiókokat. Az egyéb elérhető műveletekről az [Azure Active Directory felhasználói felügyeleti dokumentációjában](../users-groups-roles/index.yml)olvashat bővebben.
