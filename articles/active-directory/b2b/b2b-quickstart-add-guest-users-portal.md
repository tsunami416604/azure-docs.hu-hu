---
title: 'Rövid útmutató: Vendégfelhasználók hozzáadása az Azure Portalon – Azure AD'
description: Ez a rövid útmutató bemutatja, hogyan vehetnek fel az Azure AD rendszergazdák B2B vendégfelhasználókat az Azure portálon és végigvezet a B2B-meghívási munkafolyamat lépésein.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 01/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f3d96926221a69e1cf216be81368a2eb87c938a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76758333"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rövid útmutató: Vendégfelhasználók felvétele a címtárhoz az Azure portálon

A címtárba vendégfelhasználóként felvéve bárkit meghívhat a szervezetben végzett közös munkára. Utána küldhet érvényesítési hivatkozást tartalmazó meghívó e-mailt de küldhet közvetlen hivatkozást is a megosztani kívánt alkalmazáshoz. A vendégfelhasználók saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkezhetnek be.

Ebben a rövid útmutatóban, felvesz egy új vendégfelhasználót az Azure AD-ben, meghívót küld és láthatja, hogyan néz ki a vendégfelhasználó meghívó érvényesítési folyamata.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

 - Egy szerepkör, amely lehetővé teszi felhasználók létrehozását a bérlő címtárban, mint amilyen például a globális rendszergazdai szerepkör vagy bármely korlátozott rendszergazdai címtári szerepkör.
 - Érvényes e-mail-fiók, amit a bérlő címtárhoz adhat, és amelyben fogadni tudja a teszt meghívó e-mailt.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Új vendégfelhasználó felvétele Azure AD-ben

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) Azure AD-rendszergazdaként.
2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** alatt válassza a **Felhasználókat**.

    ![Képernyőkép afelhasználók beállításának helyének kiválasztásáról](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Válassza az **Új vendégfelhasználót**.

    ![Képernyőkép akövetkezőben az Új vendégfelhasználó beállítás hol választható.](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Az **Új felhasználó** lapon válassza a **Felhasználó meghívása** lehetőséget, majd adja meg a vendégfelhasználó adatait. 

   - **név.** A vendégfelhasználó vezeték- és keresztneve.
   - **E-mail cím (kötelező)**. A vendégfelhasználó e-mail címe.
   - **Személyes üzenet (nem kötelező)** Személyes üdvözlőüzenetet is mellékeljen a vendégfelhasználónak.
   - **Csoportok**: A vendégfelhasználót hozzáadhatja egy vagy több meglévő csoporthoz, vagy később is megteheti.
   - **Címtárszerepkör:** Ha a felhasználónak Azure AD felügyeleti engedélyeket kell megadnia, hozzáadhatja őket egy Azure AD-szerepkörhöz. 

6. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. A jobb felső sarokban megjelenik az értesítés a **Felhasználó meghívása sikerült** üzenettel. 
7.  Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="assign-an-app-to-the-guest-user"></a>Alkalmazás a hozzárendelése a vendégfelhasználóhoz
Adja hozzá a Salesforce alkalmazást a teszt bérlőhöz és rendelje hozzá a vendégfelhasználót az alkalmazáshoz.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet.
3.  Válassza az **Új alkalmazás** lehetőséget.
4. A **Hozzáadás a katalógusból** részen keresse meg és válassza ki a **Salesforce** elemet.

    ![Képernyőkép a Hozzáadás a gyűjtemény keresőmezőjéből](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Válassza a **Hozzáadás** lehetőséget.
6. A **Kezelés** részen jelölje be az **Egyszeri bejelentkezést**, majd az **Egyszeri bejelentkezési mód** részen válassza a **Jelszóalapú bejelentkezést** és kattintson a **Mentés** gombra.
7. A **Kezelés** részen válassza a **Felhasználók és csoportok** > **Felhasználó hozzáadása** > **Felhasználók és csoportok** pontot.
8. A keresőmezőt használva keressen rá a tesztfelhasználóra (ha szükséges), és válassza ki a listából. Ezután kattintson a **Kiválasztás** elemre.
9. Válassza a **Hozzárendelés** elemet. 

## <a name="accept-the-invitation"></a>A meghívás elfogadása
Most jelentkezzen be vendégfelhasználóként, hogy lássa a meghívót.
1.  Jelentkezzen be a teszt vendégfelhasználó e-mail-fiókjába.
2.  A Beérkezett üzenetek mappában található a „Meghívást kapott” e-mail.

    ![Képernyőkép a B2B meghívó e-mailcímről](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Az e-mail törzsében válassza az **Első lépések** elemet. Az **Engedélyek felülvizsgálata** lap megnyílik a böngészőben. 

    ![Az Engedélyek ellenőrzése lapot bemutató képernyőkép](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Válassza ki az **Elfogadás** lehetőséget. Megnyílik a Hozzáférési Panel, amely megjeleníti azokat az alkalmazásokat, amelyekhez a vendégfelhasználó hozzáférhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a tesztelése használt vendégfelhasználót és a teszt alkalmazást.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** résznél válassza a **Vállalati alkalmazások** elemet.
4.  Nyissa meg a **Salesforce** alkalmazást, majd válassza a **Törlést**.
5.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
6.  A **Kezelés** alatt válassza a **Felhasználókat**.
7.  Válassza ki a tesztfelhasználót, majd utána a **Felhasználó törlése** lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy vendégfelhasználót az Azure portálon és meghívót küldött az alkalmazások megosztására. Ezután megtekintette az érvényesítési folyamatot a vendégfelhasználó szempontjából, és ellenőrizte, hogy az alkalmazás valóban megjelent a vendégfelhasználó számára a Hozzáférési panelen. Az együttműködő vendégfelhasználók hozzáadásáról további tudnivalók: [Azure Active Directory B2B együttműködő felhasználók hozzáadása az Azure portálon](add-users-administrator.md).
