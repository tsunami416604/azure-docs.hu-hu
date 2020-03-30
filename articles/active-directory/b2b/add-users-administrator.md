---
title: B2B együttműködési felhasználók hozzáadása az Azure Portalon – Azure AD
description: Bemutatja, hogyan adhat hozzá egy rendszergazda vendégfelhasználókat a címtárukhoz egy partnerszervezettől az Azure Active Directory (Azure AD) B2B együttműködés használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263503"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B együttműködési felhasználók hozzáadása az Azure Portalon

A korlátozott rendszergazdai címtárszerepkörök bármelyikéhez hozzárendelt felhasználóként az Azure Portal segítségével meghívhat B2B együttműködési felhasználókat. Meghívhat vendégfelhasználókat a címtárba, egy csoportba vagy egy alkalmazásba. Miután meghívja a felhasználót ezen módszerek bármelyikén keresztül, a meghívott felhasználó fiókja hozzáadódik az Azure Active Directoryhoz (Azure AD), egy *vendég*típusú felhasználóval. A vendégfelhasználónak ezután be kell váltania a meghívót az erőforrások eléréséhez. A felhasználó meghívása nem jár le.

Miután hozzáadott egy vendégfelhasználót a címtárhoz, közvetlen hivatkozást küldhet a vendégfelhasználónak egy megosztott alkalmazásra, vagy a vendégfelhasználó a beváltási URL-re kattintva a meghívó e-mailben. A beváltási folyamatról további információt a [B2B együttműködési meghívás-visszaváltás című](redemption-experience.md)témakörben talál.

> [!IMPORTANT]
> Kövesse az [Útmutató: Adja meg a szervezet adatvédelmi adatait](https://aka.ms/adprivacystatement) az Azure Active Directoryban a szervezet adatvédelmi nyilatkozatának URL-címének hozzáadásához. Az első alkalommal meghívásos beváltási folyamat részeként a meghívott felhasználónak hozzá kell járulnia az Ön adatvédelmi feltételeihez a folytatáshoz. 

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy a szervezet külső együttműködési beállításai úgy vannak konfigurálva, hogy meghívhatja a vendégeket. Alapértelmezés szerint minden felhasználó és rendszergazda meghívhat vendégeket. Előfordulhat azonban, hogy a szervezet külső együttműködési szabályzatai úgy vannak beállítva, hogy bizonyos típusú felhasználók vagy rendszergazdák ne hívjanak vendégeket. A házirendek megtekintéséről és beállításáról a B2B külső együttműködés engedélyezése és annak kezelése című témakörben [talál, hogy ki hívhat meg vendégeket.](delegate-invitations.md)

## <a name="add-guest-users-to-the-directory"></a>Vendégfelhasználók hozzáadása a címtárhoz

Ha B2B együttműködési felhasználókat szeretne hozzáadni a címtárhoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy korlátozott rendszergazdai címtárszerepkörrel vagy a vendégmeghívó szerepkörrel rendelkező felhasználóként.
2. Keresse meg, és válassza ki az **Azure Active Directory** bármely oldalon.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. Válassza az **Új vendégfelhasználót**.

   ![Megmutatja, hogy hol van az új vendégfelhasználó a felhasználói felületen](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > Az **Új vendégfelhasználó** beállítás a **Szervezeti kapcsolatok** lapon is elérhető. Az **Azure Active Directory ban**a **Kezelés**csoportban válassza a Szervezeti **kapcsolatok**lehetőséget.

5. Az **Új felhasználó** lapon válassza a **Felhasználó meghívása** lehetőséget, majd adja meg a vendégfelhasználó adatait. 

    > [!NOTE]
    > A csoport e-mail címei nem támogatottak; adja meg az egyén e-mail címét. Emellett egyes e-mail szolgáltatók lehetővé teszik a felhasználók számára, hogy plusz szimbólumot (+) és további szöveget adjanak e-mail címükhöz, hogy segítsenek például a beérkezett üzenetek szűrésében. Az Azure AD azonban jelenleg nem támogatja a plusz szimbólumokat az e-mail címekben. A szállítási problémák elkerülése érdekében hagyja ki a plusz szimbólumot és a @ jelig követő karaktereket.

   - **név.** A vendégfelhasználó vezeték- és keresztneve.
   - **E-mail cím (kötelező)**. A vendégfelhasználó e-mail címe.
   - **Személyes üzenet (nem kötelező)** Személyes üdvözlőüzenetet is mellékeljen a vendégfelhasználónak.
   - **Csoportok**: A vendégfelhasználót hozzáadhatja egy vagy több meglévő csoporthoz, vagy később is megteheti.
   - **Címtárszerepkör:** Ha a felhasználónak Azure AD felügyeleti engedélyeket kell megadnia, hozzáadhatja őket egy Azure AD-szerepkörhöz. 

7. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. 
 
Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.


![B2B felhasználó megjelenítése Vendég felhasználó típussal](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Vendégfelhasználók hozzáadása egy csoporthoz
Ha manuálisan kell hozzáadnia a B2B együttműködési felhasználókat egy csoporthoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) Azure AD-rendszergazdaként.
2. Keresse meg, és válassza ki az **Azure Active Directory** bármely oldalon.
3. A **Kezelés csoportban**válassza a **Csoportok lehetőséget.**
4. Jelöljön ki egy csoportot (vagy kattintson az **Új csoport** gombra új létrehozása érdekében). Célszerű felvenni a csoport leírásába, hogy a csoport B2B vendégfelhasználókat tartalmaz.
5. Válassza ki a **Members** (Tagok) elemet. 
6. Tegye a következők egyikét:
   - Ha a vendégfelhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Jelölje ki a felhasználót, majd a **Kijelölés gombra** kattintva adja hozzá a felhasználót a csoporthoz.
   - Ha a vendégfelhasználó még nem létezik a címtárban, hívja meg őket a csoportba úgy, hogy beírja az e-mail címét a keresőmezőbe, beírja a választható személyes üzenetet, majd kattintson a **Kijelölés**gombra. A meghívó automatikusan megjelenik a meghívott felhasználó számára.
     
     ![Meghívás hozzáadása gomb vendégtagok hozzáadásához](./media/add-users-administrator/GroupInvite.png)
   
Dinamikus csoportokat is használhat az Azure AD B2B együttműködéssel. További információ: [Dinamikus csoportok és az Azure Active Directory B2B együttműködés.](use-dynamic-groups.md)

## <a name="add-guest-users-to-an-application"></a>Vendégfelhasználók hozzáadása egy alkalmazáshoz

Ha B2B együttműködési felhasználókat szeretne hozzáadni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) Azure AD-rendszergazdaként.
2. Keresse meg, és válassza ki az **Azure Active Directory** bármely oldalon.
3. A **Kezelés csoportban**válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**
4. Válassza ki azt az alkalmazást, amelyhez vendégfelhasználókat szeretne hozzáadni.
5. Az alkalmazás irányítópultján válassza az **Összes felhasználó lehetőséget** a Felhasználók és **csoportok** ablaktábla megnyitásához.

    ![A Felhasználók összesen gombja a megnyitott felhasználók és csoportok hozzáadásához](./media/add-users-administrator/AppUsersAndGroups.png)

6. Válassza **a Felhasználó hozzáadása**lehetőséget.
7. A **Hozzárendelés hozzáadása**csoportban válassza a Felhasználó és csoportok **lehetőséget.**
8. Tegye a következők egyikét:
   - Ha a vendégfelhasználó már létezik a címtárban, keresse meg a B2B felhasználót. Jelölje ki a felhasználót, kattintson a **Kijelölés gombra,** majd a **Hozzárendelés gombra** kattintva vegye fel a felhasználót az alkalmazásba.
   - Ha a vendégfelhasználó még nem létezik a címtárban, a **Tag kiválasztása vagy külső felhasználó meghívása**csoportban írja be a felhasználó e-mail címét. Az üzenetmezőbe írjon be egy nem kötelező személyes üzenetet. Az üzenetmező alatt kattintson a **Meghívás gombra.**
           
       ![Meghívás hozzáadása gomb vendégtagok hozzáadásához](./media/add-users-administrator/AppInviteUsers.png)
   
      Kattintson **a Kijelölés**gombra, majd a **Hozzárendelés gombra** a felhasználó alkalmazáshoz való hozzáadásához. A meghívó automatikusan megjelenik a meghívott felhasználó számára.

9. A vendégfelhasználó megjelenik az alkalmazás **Felhasználók és csoportok** listájában az Alapértelmezett **hozzáférés**hozzárendelt szerepkörével. Ha módosítani szeretné a szerepkört, tegye a következőket:
   - Jelölje ki a vendégfelhasználót, majd kattintson a **Szerkesztés gombra.** 
   - A **Hozzárendelés szerkesztése**csoportban kattintson a **Szerepkör kiválasztása**gombra, és jelölje ki a kijelölt felhasználóhoz rendelni kívánt szerepkört.
   - Kattintson a **Kiválasztás** gombra.
   - Kattintson a **Hozzárendelés** gombra.
 
## <a name="resend-invitations-to-guest-users"></a>Meghívók újraküldése a vendégfelhasználóknak

Ha egy vendégfelhasználó még nem váltotta be a meghívót, küldje el újra a meghívó e-mailt.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) Azure AD-rendszergazdaként.
2. Keresse meg, és válassza ki az **Azure Active Directory** bármely oldalon.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
5. Válassza ki a felhasználói fiókot.
6. A **Kezelés csoportban**válassza a **Profil**lehetőséget.
7. Ha a felhasználó még nem fogadta el a meghívást, elérhető az **Újraküldés lehetőség.** Az újraküldéshez válassza ezt a gombot.

   ![Meghívás újraküldése a felhasználói profilban](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Ha olyan meghívót küld újra, amely eredetileg egy adott alkalmazásra irányította a felhasználót, akkor az új meghívóban lévő hivatkozás a legfelső szintű hozzáférési panelre irányítja a felhasználót.

## <a name="next-steps"></a>További lépések

- Ha meg szeretné tudni, hogy a nem Azure-beli AD-rendszergazdák hogyan vehetnek fel B2B vendégfelhasználókat, olvassa el a [Hogyan adhatnak hozzá az informatikai dolgozók B2B együttműködési felhasználókat?](add-users-information-worker.md)
- A meghívó e-mailről a [B2B együttműködési meghívó e-mail elemei című témakörben](invitation-email-elements.md)talál további információt.

