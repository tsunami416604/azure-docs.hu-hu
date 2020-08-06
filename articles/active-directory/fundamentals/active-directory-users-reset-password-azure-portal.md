---
title: Felhasználó jelszavának alaphelyzetbe állítása – Azure Active Directory | Microsoft Docs
description: Útmutató a felhasználók jelszavának alaphelyzetbe állításához a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69da6aa0253f92f9242a988c6b46de873df0677e
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797203"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Felhasználói jelszó visszaállítása az Azure Active Directoryval

Rendszergazdaként alaphelyzetbe állíthatja a felhasználó jelszavát, ha a jelszót elfelejtik, ha a felhasználó ki van zárva egy eszközről, vagy ha a felhasználó nem kapott jelszót.

>[!Note]
>Ha az Azure AD-bérlő nem a felhasználó kezdőkönyvtárának, akkor nem fogja tudni visszaállítani a jelszavát. Ez azt jelenti, hogy ha a felhasználó egy másik szervezettől, egy Microsoft-fiók vagy egy Google-fióktól származó fiókkal jelentkezik be a szervezetbe, nem fogja tudni visszaállítani a jelszavát.<br><br>Ha a felhasználó rendelkezik Windows Server Active Directory-szolgáltatói forrással, akkor csak akkor tudja visszaállítani a jelszót, ha bekapcsolta a visszaírási.<br><br>Ha a felhasználó rendelkezik külső Azure AD-szolgáltatói forrással, nem fogja tudni visszaállítani a jelszót. Csak a felhasználó vagy egy külső Azure AD-beli rendszergazda állíthatja alaphelyzetbe a jelszót.

>[!Note]
>Ha Ön nem rendszergazda, és ehelyett a saját munkahelyi vagy iskolai jelszavának alaphelyzetbe állítására vonatkozó utasításokat keres, tekintse [meg a munkahelyi vagy iskolai jelszó alaphelyzetbe állítása](../user-help/active-directory-passwords-update-your-own-password.md)című témakört.

## <a name="to-reset-a-password"></a>Jelszó alaphelyzetbe állítása

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként vagy jelszó-rendszergazdaként. További információ az elérhető szerepkörökről: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**lehetőséget, keresse meg és válassza ki az alaphelyzetbe állítani kívánt felhasználót, majd válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

    Megjelenik az **Alain Charon-profil** lap a **jelszó alaphelyzetbe állítása** beállítással.

    ![Felhasználói profil lapja, a jelszó alaphelyzetbe állítása lehetőség kiemelve](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. A **jelszó alaphelyzetbe** állítása lapon válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

    > [!Note]
    > Azure Active Directory használatakor a rendszer automatikusan létrehoz egy ideiglenes jelszót a felhasználó számára. Active Directory helyszíni használatakor hozza létre a felhasználó jelszavát.

4. Másolja a jelszót, és adja meg a felhasználónak. A következő bejelentkezési folyamat során a felhasználónak meg kell változtatnia a jelszót.

    >[!Note]
    >Az ideiglenes jelszó soha nem jár le. Amikor a felhasználó legközelebb bejelentkezik, a jelszó továbbra is működni fog, függetlenül attól, hogy mennyi idő telt el az ideiglenes jelszó létrehozása óta.

## <a name="next-steps"></a>További lépések

A felhasználó jelszavának alaphelyzetbe állítása után a következő alapvető folyamatokat végezheti el:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

Vagy összetettebb felhasználói forgatókönyveket is végrehajthat, például delegálásokat rendelhet hozzá, házirendeket használhat, és megoszthatja a felhasználói fiókokat. További információ az egyéb elérhető műveletekről: [Azure Active Directory felhasználói kezelés dokumentációja](../users-groups-roles/index.yml).
