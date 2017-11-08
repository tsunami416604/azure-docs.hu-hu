---
title: "Jelszó alaphelyzetbe állítása, az Azure Active Directoryban |} Microsoft Docs"
description: "Rendszergazdai jelszó alaphelyzetbe állítása, az Azure Active Directoryban egy felhasználó által kezdeményezett"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Az Azure Active Directoryban a felhasználó jelszavának visszaállítása

Rendszergazdák a felhasználói jelszó azokban az esetekben, ahol elfelejtette, előfordulhat, hogy szükség adta ki, vagy más forgatókönyvekre vonatkozóan. A következő lépések végigvezetik a jelszó alaphelyzetbe állítása.

## <a name="how-to-reset-the-password-for-a-user"></a>A felhasználó jelszavának alaphelyzetbe állítása

1. Jelentkezzen be a [Azure AD felügyeleti központban](https://aad.portal.azure.com) egy olyan fiókkal, amely directory jogosult felhasználók új jelszavainak létrehozására.
2. Válassza ki **Azure Active Directory** > **felhasználók és csoportok** > **minden felhasználó**.
3. Válassza ki a felhasználó szeretné visszaállítani a jelszavát.
2. Válassza ki a kijelölt felhasználó **jelszó-átállítási**.

    ![Az Azure AD egy felhasználó a felhasználói profilt a jelszó alaphelyzetbe állítása](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. A **jelszó-átállítási**, jelölje be **jelszó-átállítási**.
7. Egy ideiglenes jelszót, majd megadhatja a felhasználó számára jelenik meg. Majd módosíthatják a jelszavukat a következő alkalommal, azok bejelentkezési kérni fogja a a felhasználótól. 

   > [!NOTE]
   > Ezt az ideiglenes jelszót nem rendelkezik lejárati idő, amíg jelentkeznek be érvényes lesz, és a rendszer majd kényszerítve vannak-e a módosításáról. 

## <a name="next-steps"></a>Következő lépések
* [Felhasználó hozzáadása](active-directory-users-create-azure-portal.md)
* [Rendszergazdai szerepkörök hozzárendelése egy felhasználóhoz](active-directory-users-assign-role-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Az Azure AD-felhasználó törlése](active-directory-users-delete-user-azure-portal.md)
