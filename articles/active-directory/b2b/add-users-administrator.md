---
title: Az Azure Portal – Azure Active Directory B2B együttműködés felhasználók hozzáadása |} A Microsoft Docs
description: Bemutatja, hogyan egy rendszergazda adhat hozzá vendégfelhasználókat a címtárhoz, egy fiókpartner-szervezet az Azure Active Directory (Azure AD) B2B együttműködés segítségével.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: 43cceeb9b65bcee97855f96c781c5d157b32346a
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080923"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure Portalon

Globális rendszergazda, vagy egy felhasználót, aki a korlátozott rendszergazda címtárbeli szerepkörök van hozzárendelve, mint az Azure portal segítségével meghívása a B2B-együttműködés felhasználók. Meghívhat vendégfelhasználókat arra a könyvtárra, csoporthoz vagy alkalmazáshoz. Miután a felhasználó meghívása a keresztül az alábbi módszereket, a meghívott felhasználó fiókot hozzáadnak a Azure Active Directory (Azure AD), felhasználói típusú *vendég*. A vendégfelhasználó majd be kell váltaniuk erőforrások eléréséhez a meghívót.

Vendégfelhasználó felvétele a címtárban, vagy elküldheti a vendégfelhasználó közvetlen hivatkozás egy megosztott alkalmazás, vagy a vendégfelhasználó kattintson a meghívó e-mailben a beváltási URL-CÍMÉT. Az érvényesítési folyamattal kapcsolatos további információkért lásd: [B2B együttműködés vendégmeghívás beváltása](redemption-experience.md).

> [!IMPORTANT]
> Kövesse a lépéseket a [Útmutató: Adja hozzá a szervezet adatvédelmi információ az Azure Active Directoryban](https://aka.ms/adprivacystatement) hozzáadása a szervezet adatvédelmi nyilatkozat URL-CÍMÉT. Az első alkalommal meghívó érvényesítési folyamat részeként egy meghívott felhasználó jóvá kell hagynia az adatvédelmi szabályzat feltételeit a folytatáshoz. 

## <a name="add-guest-users-to-the-directory"></a>Vendég felhasználók hozzáadása a címtárhoz

B2B-együttműködés felhasználók hozzáadása a címtárhoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) Azure AD rendszergazdaként.
2. A navigációs panelen válassza ki **Azure Active Directory**.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Válassza az **Új vendégfelhasználót**.

   ![Látható, ahol új vendégfelhasználó a felhasználói felületen](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > A **új vendégfelhasználó** beállítás akkor is elérhető a **szervezeti kapcsolatok** lapot. A **Azure Active Directory**alatt **kezelés**válassza **szervezeti kapcsolatok**.

5. A **Felhasználónév** alatt adja meg a külső felhasználó e-mail-címét. Ha szeretné, üdvözlő üzenetet is megadhat. Példa:

   ![Látható, ahol új vendégfelhasználó a felhasználói felületen](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Néhány e-mail-szolgáltatók engedélyezése a felhasználók hozzáadása a plusz jelet (+) és az e-mail címeket, például a Beérkezett üzenetek szűrése érdekében további szöveget. Azure ad-ben azonban jelenleg nem támogatja e-mail-címek szereplő szimbólumok jelentései a plusz. Kézbesítési problémák elkerülése érdekében hagyja ki ezt a szimbólumot, és akár a következő karaktereket a @ karaktert.

6. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. 
 
Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


![Megjeleníti a B2B-felhasználó a Vendég felhasználó típusa](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Vendégfelhasználók hozzáadása csoporthoz
Ha a B2B-együttműködés felhasználók manuális hozzáadása egy csoporthoz, az Azure AD-rendszergazdaként van szüksége, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) Azure AD rendszergazdaként.
2. A navigációs panelen válassza ki **Azure Active Directory**.
3. A **kezelés**válassza **csoportok**.
4. Válasszon ki egy csoportot (vagy kattintson a **új csoport** hozzon létre egy újat). Érdemes felvenni a csoport leírását, hogy a csoport tartalmazza-e a B2B vendégfelhasználókat.
5. Válassza ki **tagok**. 
6. Tegye a következők valamelyikét:
   - Ha a Vendég felhasználó már létezik a címtárban, keresse meg a B2B-felhasználó. Válassza ki a felhasználót, és kattintson a **kiválasztása** a felhasználó hozzáadása a csoporthoz.
   - Ha a vendégfelhasználó még nem létezik a címtárban, felkínáljuk számukra az a csoport e-mail-címükkel írja be a keresőmezőbe, írja be egy személyes üzenetet, majd kattintson **kiválasztása**. A meghívó automatikusan kerül ki a meghívott felhasználó.
     
     ![A meghívás gombra kattintva adhat hozzá a Vendég tagok hozzáadása](./media/add-users-administrator/GroupInvite.png)
   
Dinamikus csoportok Azure AD B2B együttműködés is használhatja. További információkért lásd: [dinamikus csoportok és az Azure Active Directory B2B együttműködés](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Vendég felhasználók hozzáadása az alkalmazáshoz

B2B együttműködési felhasználókat ad hozzá egy alkalmazást az Azure AD-rendszergazdaként, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) Azure AD rendszergazdaként.
2. A navigációs panelen válassza ki **Azure Active Directory**.
3. Alatt **kezelés**válassza **vállalati alkalmazások** > **minden alkalmazás**.
4. Válassza ki a vendégfelhasználók hozzáadása kívánt alkalmazást.
5. Az alkalmazás irányítópulton, válassza ki a **felhasználók teljes** megnyitásához a **felhasználók és csoportok** ablaktáblán.

    ![Összes felhasználók gombra kattintva nyissa meg felhasználók és csoportok hozzáadása](./media/add-users-administrator/AppUsersAndGroups.png)

6. Válassza ki **felhasználó hozzáadása**.
7. A **hozzárendelés hozzáadása**válassza **felhasználók és csoportok**.
8. Tegye a következők valamelyikét:
   - Ha a Vendég felhasználó már létezik a címtárban, keresse meg a B2B-felhasználó. Válassza ki a felhasználót, kattintson a **kiválasztása**, és kattintson a **hozzárendelése** a felhasználó hozzáadása az alkalmazáshoz.
   - Ha a vendégfelhasználó még nem létezik a címtárban található **tag kiválasztása vagy egy külső felhasználó meghívása**, írja be a felhasználó e-mail címét. Az üzenet mezőbe írja be egy személyes üzenetet. Az üzenet mező alatt kattintson **meghívása**.
           
       ![A meghívás gombra kattintva adhat hozzá a Vendég tagok hozzáadása](./media/add-users-administrator/AppInviteUsers.png)
   
      Kattintson a **kiválasztása**, és kattintson a **hozzárendelése** a felhasználó hozzáadása az alkalmazáshoz. Meghívó automatikusan kerül ki a meghívott felhasználó.

9. Az alkalmazás megjelenik a vendégfelhasználó **felhasználók és csoportok** listában hozzárendelt szerepkörű **alapértelmezett hozzáférési**. Ha azt szeretné, módosíthatja a szerepkört, tegye a következőket:
   - A vendégfelhasználó, majd válassza ki és **szerkesztése**. 
   - A **hozzárendelés szerkesztése**, kattintson a **Szerepkörválasztás**, és válassza ki a szerepkört szeretné hozzárendelni a kiválasztott felhasználóhoz.
   - Kattintson a **Kiválasztás** gombra.
   - Kattintson a **Hozzárendelés** gombra.
 
## <a name="resend-invitations-to-guest-users"></a>Vendégfelhasználók meghívókat

Ha a vendégfelhasználó még nem sikerült beváltani a meghívót, elküldheti a a meghívó e-mailt.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com) Azure AD rendszergazdaként.
2. A navigációs panelen válassza ki **Azure Active Directory**.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
5. Válassza ki a felhasználói fiókot.
6. A **kezelés**válassza **profil**.
7. Ha a felhasználó még nem fogadta el a meghívást, egy **meghívó újraküldése** lehetőség érhető el. Válassza ki az erre a gombra kattintva küldje el újra.

   ![Küldje el újra a meghívás lehetőséget a felhasználói profil](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Ha meghívót, amely eredetileg átirányítja a felhasználót, hogy egy adott alkalmazás újraküldi, ismerje meg, hogy a hivatkozás az új ajánlati vesz igénybe a felhasználó a legfelső szintű hozzáférési panel helyette.

## <a name="next-steps"></a>További lépések

- Című cikk nyújt tájékoztatást, hogyan adhat hozzá nem Azure AD-rendszergazdák B2B vendégfelhasználók, [hogyan az információkkal dolgozó szakemberek hozzá B2B együttműködési felhasználókat?](add-users-information-worker.md)
- A meghívó e-mailek kapcsolatos információkért lásd: [a B2B-együttműködés meghívó e-mail elemeinek](invitation-email-elements.md).

