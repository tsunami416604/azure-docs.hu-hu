---
title: A felhasználói jelszó - Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directory felhasználó jelszavának alaphelyzetbe állítása leírja.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: c8dd387104d52075425d881363dbcbaac700461a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103370"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Az Azure Active Directory felhasználói jelszó alaphelyzetbe állítása
A rendszergazdák, ha elfelejti a jelszavát, ha a felhasználó-eszköz lekérdezi kizárva, vagy ha a felhasználó soha nem kapott jelszót alaphelyzetbe állíthatja a felhasználó jelszavát.

>[!Note]
>Kivéve, ha az Azure AD-bérlő egy felhasználó a kezdőkönyvtárba, nem lehet új jelszót kérnek. Ez azt jelenti, hogy a felhasználó a szervezet egy másik szervezet, a Microsoft-fiók vagy a Google-fiók egy olyan fiókkal jelentkezik be, ha nem tudja alaphelyzetbe állítani a jelszavukat.<br><br>Ha a felhasználó rendelkezik egy, a Windows Server Active Directory mérvadó forrás, csak fogja tudni új jelszót, ha bekapcsolta a jelszóvisszaíró.<br><br>Ha a felhasználó rendelkezik egy, az Azure AD külső mérvadó forrás, nem lehet új jelszót kérhet. Csak a felhasználó vagy egy külső Azure Active Directory, a rendszergazda alaphelyzetbe állíthatja a jelszót.

>[!Note]
>Ha Ön nem rendszergazda, és további útmutatás a saját munkahelyi vagy iskolai jelszó helyett keres, tekintse meg a [a munkahelyi vagy iskolai jelszó visszaállítása](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Jelszó alaphelyzetbe állítása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy globális rendszergazdai, a felhasználó rendszergazda vagy a jelszókezelő. Az elérhető szerepkörök kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, keresse meg és válassza ki az alaphelyzetbe állítás szükséges, és válassza **jelszó alaphelyzetbe állítása**.

    A **Alain Charon - profil** ablak, amely a **jelszó alaphelyzetbe állítása** lehetőséget.

    ![Felhasználói profil oldala, amelyen Reset jelszó opció kiemelésével](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Az a **jelszó alaphelyzetbe állítása** lapon jelölje be **jelszó alaphelyzetbe állítása**.

    Ideiglenes jelszó jön létre automatikusan a felhasználó számára.

4. Másolja a jelszót, és adjon meg a felhasználó számára. A felhasználónak kell módosítani a jelszót a következő bejelentkezési folyamat során.

    >[!Note]
    >Az ideiglenes jelszó sohasem jár le. Amikor legközelebb a felhasználó bejelentkezik, a jelszó továbbra is működni fog, függetlenül hogyan sok idő telt, mivel az ideiglenes jelszó lett létrehozva.

## <a name="next-steps"></a>További lépések
Miután a felhasználó jelszavát is alaphelyzetbe állítja, az alábbi alapszintű folyamatok hajthatja végre:

- [Adja hozzá, vagy a felhasználók törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

Vagy a felhasználó az összetettebb esetekhez, például delegáltak hozzárendelése, házirendekkel és megosztása, felhasználói fiókok is végezhet. Egyéb elérhető műveletekkel kapcsolatos további információkért lásd: [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
