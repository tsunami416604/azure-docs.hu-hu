---
title: 'Rövid útmutató: Vendégfelhasználók hozzáadása az Azure portálon'
description: Ez a rövid útmutató bemutatja, hogyan vehetnek fel az Azure AD rendszergazdák B2B vendégfelhasználókat az Azure portálon és végigvezet a B2B-meghívási munkafolyamat lépésein.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: ed49eed6dbfce9a71f13770e3ddcaec6557cf875
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45986545"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rövid útmutató: Vendégfelhasználók felvétele a címtárhoz az Azure portálon

A címtárba vendégfelhasználóként felvéve bárkit meghívhat a szervezetben végzett közös munkára. Utána küldhet érvényesítési hivatkozást tartalmazó meghívó e-mailt de küldhet közvetlen hivatkozást is a megosztani kívánt alkalmazáshoz. A vendégfelhasználók saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkezhetnek be.

Ebben a rövid útmutatóban, felvesz egy új vendégfelhasználót az Azure AD-ben, meghívót küld és láthatja, hogyan néz ki a vendégfelhasználó meghívó érvényesítési folyamata.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

 - Egy szerepkör, amely lehetővé teszi felhasználók létrehozását a bérlő címtárban, mint amilyen például a globális rendszergazdai szerepkör vagy bármely korlátozott rendszergazdai címtári szerepkör.
 - Érvényes e-mail-fiók, amit a bérlő címtárhoz adhat, és amelyben fogadni tudja a teszt meghívó e-mailt.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Új vendégfelhasználó felvétele Azure AD-ben

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/) Azure AD rendszergazdaként.
2. A bal oldali panelen válassza az **Azure Active Directory** gombot.
3.  A **Kezelés** alatt válassza a **Felhasználókat**.

    ![Válassza az Azure Active Directory elemet.](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Válassza az **Új vendégfelhasználót**.

    ![Válassza az Azure Active Directory elemet.](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5.  A **Felhasználónév** alatt adja meg a külső felhasználó e-mail-címét. A **Személyes üzenet hozzáadása a meghívóhoz** részen írja be az üdvözlő üzenetet. 

    ![Válassza az Azure Active Directory elemet.](media/quickstart-add-users-portal/quickstart-users-portal-user-4.png)

6. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. A jobb felső sarokban megjelenik az értesítés a **Felhasználó meghívása sikerült** üzenettel. 
7.  Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="assign-an-app-to-the-guest-user"></a>Alkalmazás a hozzárendelése a vendégfelhasználóhoz
Adja hozzá a Salesforce alkalmazást a teszt bérlőhöz és rendelje hozzá a vendégfelhasználót az alkalmazáshoz.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet.
3.  Válassza az **Új alkalmazás** lehetőséget.
4. A **Hozzáadás a katalógusból** részen keresse meg és válassza ki a **Salesforce** elemet.

    ![Válassza az Azure Active Directory elemet.](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Válassza a **Hozzáadás** lehetőséget.
6. A **Kezelés** részen jelölje be az **Egyszeri bejelentkezést**, majd az **Egyszeri bejelentkezési mód** részen válassza a **Jelszóalapú bejelentkezést** és kattintson a **Mentés** gombra.
7. A **Kezelés** részen válassza a **Felhasználók és csoportok** > **Felhasználó hozzáadása** > **Felhasználók és csoportok** pontot.
8. A keresőmezőt használva keressen rá a tesztfelhasználóra (ha szükséges), és válassza ki a listából. Ezután kattintson a **Kiválasztás** elemre.
9. Válassza a **Hozzárendelés** elemet. 

## <a name="accept-the-invitation"></a>A meghívás elfogadása
Most jelentkezzen be vendégfelhasználóként, hogy lássa a meghívót.
1.  Jelentkezzen be a teszt vendégfelhasználó e-mail-fiókjába.
2.  A Beérkezett üzenetek mappában található a „Meghívást kapott” e-mail.

    ![B2B meghívó e-mail](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Az e-mail törzsében válassza az **Első lépések** elemet. Az **Engedélyek felülvizsgálata** lap megnyílik a böngészőben. 

    ![B2B meghívó elfogadási oldal](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Válassza ki az **Elfogadás** lehetőséget. Megnyílik a Hozzáférési Panel, amely megjeleníti azokat az alkalmazásokat, amelyekhez a vendégfelhasználó hozzáférhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a tesztelése használt vendégfelhasználót és a teszt alkalmazást.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza az **Azure Active Directory** gombot.
3.  A **Kezelés** résznél válassza a **Vállalati alkalmazások** elemet.
4.  Nyissa meg a **Salesforce** alkalmazást, majd válassza a **Törlést**.
5.  A bal oldali panelen válassza az **Azure Active Directory** gombot.
6.  A **Kezelés** alatt válassza a **Felhasználókat**.
7.  Válassza ki a tesztfelhasználót, majd utána a **Felhasználó törlése** lehetőséget.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy vendégfelhasználót az Azure portálon és meghívót küldött az alkalmazások megosztására. Ezután megtekintette az érvényesítési folyamatot a vendégfelhasználó szempontjából, és ellenőrizte, hogy az alkalmazás valóban megjelent a vendégfelhasználó számára a Hozzáférési panelen. Az együttműködő vendégfelhasználók hozzáadásáról további tudnivalók: [Azure Active Directory B2B együttműködő felhasználók hozzáadása az Azure portálon](add-users-administrator.md).