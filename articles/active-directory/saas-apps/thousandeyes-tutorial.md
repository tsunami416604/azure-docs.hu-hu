---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ThousandEyes-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ThousandEyes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373241"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a ThousandEyes-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ThousandEyes-t az Azure Active Directoryval (Azure AD). Ha integrálja a ThousandEyes-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a ThousandEyes.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezett ThousandEyes az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* ThousandEyes egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* ThousandEyes támogatja **sp és IDP** kezdeményezett SSO
* ThousandEyes támogatja [ **az automatikus** felhasználói kiépítést](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes hozzáadása a galériából

A ThousandEyes azure AD-be való integrációjának konfigurálásához hozzá kell adnia a ThousandEyes-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be az **ThousandEyes** kifejezést a keresőmezőbe.
1. Válassza a **ThousandEyes** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a ThousandEyes számára

Konfigurálja és tesztelje az Azure AD SSO-t a ThousandEyes segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó ThousandEyes.

Az Azure AD SSO konfigurálásához és teszteléséhez a ThousandEyes segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja ThousandEyes SSO](#configure-thousandeyes-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Hozzon létre ThousandEyes teszt felhasználó](#create-thousandeyes-test-user)** -, hogy egy megfelelője B.Simon thousandeyes, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **ThousandEyes** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció szakaszban** az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://app.thousandeyes.com/login/sso`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Beállítása ThousandEyes** szakaszban másolja a megfelelő URL-cím(ek) alapján a követelmény.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a ThousandEyes hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **a ThousandEyes**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-thousandeyes-sso"></a>Ezerszemű sso konfigurálása

1. Egy másik böngésző ablakban, jelentkezzen be a **ThousandEyes** cég honlapján, mint egy rendszergazda.

2. A felső menüben kattintson a **Beállítások gombra.**

    ![Beállítások](./media/thousandeyes-tutorial/ic790066.png "Beállítások")

3. Kattintson **a Fiók elemre**

    ![Fiók](./media/thousandeyes-tutorial/ic790067.png "Fiók")

4. Kattintson a **Biztonsági & hitelesítés** fülre.

    ![Biztonsági & hitelesítés](./media/thousandeyes-tutorial/ic790068.png "Biztonsági & hitelesítés")

5. Az **egyszeri bejelentkezés beállítása szakaszban** hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés beállítása](./media/thousandeyes-tutorial/ic790069.png "Egyszeri bejelentkezés beállítása")

    a. Válassza **az Egyszeri bejelentkezés engedélyezése**lehetőséget.

    b. A **Bejelentkezési lap URL-címmezőjében** illessze be **a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    c. A **Kijelentkezési lap URL-címmezőjébe** illessze be **a kijelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    d. **Identitásszolgáltató** kiállítószövege, beillesztés **Az Azure AD-azonosító**, amelyet az Azure Portalról másolt.

    e. Az **Ellenőrzési tanúsítvány ban**kattintson a Fájl **kiválasztása**elemre, majd töltse fel az Azure Portalról letöltött tanúsítványt.

    f. Kattintson a **Mentés** gombra.

### <a name="create-thousandeyes-test-user"></a>Create ThousandEyes teszt felhasználó

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű Britta Simon ThousandEyes. ThousandEyes támogatja az automatikus felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](thousandeyes-provisioning-tutorial.md) kiépítés konfigurálásáról itt olvashat bővebben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a ThousandEyes cég webhelyére rendszergazdaként.

2. Kattintson a **Beállítások** elemre.

    ![Beállítások](./media/thousandeyes-tutorial/IC790066.png "Beállítások")

3. Kattintson **a Fiók gombra.**

    ![Fiók](./media/thousandeyes-tutorial/IC790067.png "Fiók")

4. Kattintson **a Fiókok & felhasználók** fülre.

    ![Fiókok & felhasználók](./media/thousandeyes-tutorial/IC790073.png "Fiókok & felhasználók")

5. A **Felhasználók hozzáadása & fiókok csoportban** hajtsa végre az alábbi lépéseket:

    ![Felhasználói fiókok hozzáadása](./media/thousandeyes-tutorial/IC790074.png "Felhasználói fiókok hozzáadása")

    a. A **Név** mezőbe írja be a felhasználó nevét, például **B.Simon**.

    b. Az **E-mail** mezőbe írja be b.simon@contoso.coma felhasználó e-mail címét, például .

    b. Kattintson **az Új felhasználó hozzáadása a fiókhoz gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, amely egy hivatkozást a fiók megerősítéséhez és aktiválásához.

> [!NOTE]
> Az Azure Active Directory felhasználói fiókok kiépítéséhez használhatja bármely más ThousandEyes felhasználói fiók készítő eszközöket vagy API-kat, amelyeket a ThousandEyes biztosít.


## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha rákattint a ThousandEyes csempe a Hozzáférési panelen, akkor automatikusan be kell jelentkeznie a ThousandEyes, amelyre beállította SSO. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a ThousandEyes-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Felhasználói kiépítés konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)