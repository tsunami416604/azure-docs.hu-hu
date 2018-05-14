---
title: Az Azure portál – az Azure Active Directory B2B együttműködés felhasználó hozzáadása |} Microsoft Docs
description: Bemutatja, hogyan rendszergazda is hozzáadhatja vendégfelhasználók a címtár egy fiókpartner-szervezet Azure Active Directory (Azure AD) B2B együttműködés használata.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 9d0565468d953c83ca5fee864b3079fbfee9bbf1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure-portálon

A globális rendszergazdaként, vagy olyan felhasználó, aki hozzá van rendelve, a korlátozott rendszergazdai directory szerepkörökben használhatja az Azure-portálon a meghívott B2B együttműködés felhasználóknak. Felajánlhatja a vendégfelhasználók a könyvtárhoz, csoporthoz, vagy egy alkalmazáshoz. Követően a felhasználó az alábbi módszereket keresztül hívhat meg, a meghívott felhasználó fiók kerül Azure Active Directory (Azure AD), felhasználói típusú *vendég*. A Vendég felhasználó kell majd beváltani a meghívó erőforrások eléréséhez.

A Vendég felhasználó felvétele a könyvtárban, vagy elküldheti a Vendég felhasználói egy közvetlen hivatkozást egy megosztott alkalmazást, vagy a Vendég felhasználó kattinthat a meghívó e-mail érvényesítési URL-cím. Az érvényesítési folyamattal kapcsolatos további információkért lásd: [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md).

> [!IMPORTANT]
> Kövesse a lépéseket [Útmutató: vegye fel a szervezet adatvédelmi információ az Azure Active Directoryban](https://aka.ms/adprivacystatement) hozzáadása a szervezet adatvédelmi nyilatkozat URL-CÍMÉT. Az első alkalommal meghívó érvényesítési folyamat részeként a meghívott felhasználó bele kell egyeznie a adatvédelmi feltételeket, hogy továbbra is. 

## <a name="add-guest-users-to-the-directory"></a>Adja hozzá a vendégfelhasználók számára a könyvtárba

A directory B2B együttműködés felhasználók hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure AD-rendszergazdaként.
2. A navigációs ablaktáblán válassza ki a **Azure Active Directory**.
3. A **kezelése**, jelölje be **felhasználók és csoportok** > **minden felhasználó**.
4. Válassza ki **új Vendég felhasználó**.

   ![Látható, ahol új Vendég felhasználó a felhasználói felületen](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. A **hívhat meg vendégként**, adja meg a külső felhasználó e-mail címét. Választhatóan az üdvözlő üzenet. Példa:

   ![Látható, ahol új Vendég felhasználó a felhasználói felületen](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Válassza ki **meghívása** automatikusan küldéséhez a meghívót a Vendég felhasználó számára. Az a **értesítési** terület, keresse meg a **sikeresen a meghívott felhasználó** üzenet. 
 
Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a könyvtár vendégként.


![A Vendég felhasználói típusú B2B felhasználói jeleníti meg](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Vendég felhasználók hozzáadása egy csoporthoz
Ha a B2B együttműködés felhasználók manuális felvétele az Azure AD rendszergazdai csoporthoz van szüksége, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure AD-rendszergazdaként.
2. A navigációs ablaktáblán válassza ki a **Azure Active Directory**.
3. A **kezelése**, jelölje be **felhasználók és csoportok** > **összes csoport**.
4. Válasszon ki egy csoportot (vagy kattintson a **új csoport** egy új létrehozása). Érdemes felvenni a csoport leírását, hogy a csoport tartalmazza a vendégfelhasználók B2B.
5. Válassza ki **tagok** > **tagok hozzáadása**. 
6. A következők közül:
   - Ha a Vendég felhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Válassza ki a felhasználó > kattintson **válasszon** a felhasználó hozzáadása a csoporthoz.
   - Ha a Vendég felhasználó már nem létezik a címtárban, válassza ki a **meghívása**.
   ![Hozzáadás a meghívás gomb Vendég tagok felvétele](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      A **hívhat meg vendégként**, az e-mail címet, és egy opcionális személyes üzenetet > Válasszon **meghívása**. Kattintson a **válasszon** a felhasználó hozzáadása a csoporthoz.

      A meghívó automatikusan kerül ki a meghívott felhasználó. Az a **értesítési** terület, keresse meg a sikeres **Invited felhasználói** üzenet. 

Azure AD B2B együttműködés dinamikus csoportok is használható. További információkért lásd: [dinamikus csoportok és az Azure Active Directory B2B együttműködés](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Vendégfelhasználók hozzáadása egy alkalmazáshoz

Egy alkalmazás az Azure AD-rendszergazdaként B2B együttműködés felhasználók hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure AD-rendszergazdaként.
2. A navigációs ablaktáblán válassza ki a **Azure Active Directory**.
3. A **kezelése**, jelölje be **vállalati alkalmazások** > **összes alkalmazás**.
4. Válassza ki a vendégfelhasználók hozzáadni kívánt alkalmazást.
5. A **kezelése**, jelölje be **felhasználók és csoportok**.
6. Válassza ki **felhasználó hozzáadása**.
7. A **hozzáadása hozzárendelés**, jelölje be **felhasználók és csoportok**.
8. A következők közül:
   - Ha a Vendég felhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Válassza ki a felhasználót, és kattintson **válasszon** a felhasználó hozzáadása az alkalmazáshoz.
   - Ha a Vendég felhasználó már nem létezik a címtárban, válassza ki a **meghívása**.
   ![Hozzáadás a meghívás gomb Vendég tagok felvétele](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      A **hívhat meg vendégként**, az e-mail címet, és egy opcionális személyes üzenetet > Válasszon **meghívása**. Kattintson a **válasszon** a felhasználó hozzáadása az alkalmazáshoz.

      A meghívó automatikusan kerül ki a meghívott felhasználó. Az a **értesítési** terület, keresse meg a sikeres **Invited felhasználói** üzenet.

9. A **hozzárendelés hozzáadása**, kattintson a **Szerepkörválasztás** > kiválasztása a (ha van ilyen) a kijelölt felhasználóra alkalmazni > Válassza ki **OK**.
10. Kattintson a **Hozzárendelés** gombra.
 
## <a name="resend-invitations-to-guest-users"></a>A vendégfelhasználók számára meghívókat

Ha a Vendég felhasználó még nem sikerült beváltani a meghívót, elküldheti a meghívó e-mail.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) az Azure AD-rendszergazdaként.
2. A navigációs ablaktáblán válassza ki a **Azure Active Directory**.
3. A **kezelése**, jelölje be **felhasználók és csoportok**.
4. Válassza ki **minden felhasználó**.
5. Válassza ki a felhasználói fiókot.
6. A **kezelése**, jelölje be **profil**.
7. Ha a felhasználó még nem fogadta el a meghívást egy **küldje el újból a meghívó** beállítás érhető el. Jelölje ki erre a gombra kattintva küldje el újra.

   ![Küldje el újból a meghívó a beállítás a felhasználói profil](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> Ha újraküldi meghívót, amely eredetileg irányítja a felhasználót, hogy egy adott alkalmazást, ismerje meg, hogy az új ajánlati hivatkozás a felhasználó a legfelső szintű hozzáférési panel helyette.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan adhat hozzá rendszergazdák nem Azure AD B2B vendégfelhasználók, lásd: [hogyan információkkal dolgozó szakemberek hozzá B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
- A meghívó e-mail kapcsolatos információkért lásd: [a B2B együttműködés meghívó e-mail elemeinek](active-directory-b2b-invitation-email.md).

