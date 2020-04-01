---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az N2F-fel – Költségjelentések | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az N2F – Költségjelentések között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161287"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Oktatóanyag: Az Azure Active Directory integrációja az N2F-fel – Költségjelentések

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az N2F – Költségjelentések et az Azure Active Directoryval (Azure AD).
Az N2F – Költségjelentések integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az N2F – költségjelentésekhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az N2F – Költségjelentések (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció n2F - költségjelentések konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* N2F – Költségjelentések egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* N2F - A költségjelentések támogatják **az SP-t** és az **IDP** által kezdeményezett egyszeri kezelést

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Hozzáadása N2F - Költségjelentések a galériából

Az N2F – Költségjelentések Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az N2F – Költségjelentéseket a katalógusból a felügyelt SaaS-alkalmazások listájához.

**N2F - Költségjelentések hozzáadásához a gyűjteményből hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **N2F – Költségjelentések parancsot,** és válassza az **N2F – Költségjelentések lehetőséget** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![N2F - Költségjelentések az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az N2F – Költségjelentések segítségével **egy Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat N2F – Költség jelentések kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az N2F – Költségjelentésekkel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az N2F – Egyszeri bejelentkezés költségjelentéseit](#configure-n2f---expense-reports-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre N2F – Költségjelentések teszt felhasználó](#create-n2f---expense-reports-test-user)** -, hogy egy megfelelője Britta Simon N2F - Költség jelentések, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az N2F – Költségjelentésekkel hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **N2F – Költségjelentések** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszerű SAML konfigurációs** szakaszban, ha **idp** által kezdeményezett módban szeretné konfigurálni az alkalmazást, a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![N2F - Költségjelentések Domain és URL-ek egyszeri bejelentkezési információk](common/preintegrated.png)

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![N2F - Költségjelentések Domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://www.n2f.com/app/`

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

7. A **MyPolicies beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F konfigurálása – Egyszeri bejelentkezésről szóló költségjelentések

1. Egy másik böngészőablakban jelentkezzen be az N2F – Költségjelentések vállalati webhelyére rendszergazdaként.

2. Kattintson a **Beállítások** gombra, majd válassza az **Előzetes beállítások lehetőséget** a legördülő menüből.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza **a Fiókbeállítások** lapot.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/configure2.png)

4. Válassza **a Hitelesítés** lehetőséget, majd válassza a + **Hitelesítési módszer hozzáadása** lapot.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/configure3.png)

5. Válassza **az SAML Microsoft Office 365** hitelesítési módszerként lehetőséget.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/configure4.png)

6. A **Hitelesítési módszer** szakaszban hajtsa végre az alábbi lépéseket:

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/configure5.png)

    a. Az **Entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. A **Metaadatok URL-címmezőjébe** illessze be az **App Federation metaadat-url-címét,** amelyet az Azure Portalról másolt.

    c. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t az N2F – Költségjelentések engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **N2F – Költségjelentések lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **N2F – Költségjelentések lehetőséget.**

    ![Az N2F - Költségjelentések hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-n2f---expense-reports-test-user"></a>N2F létrehozása – Költségjelentések tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az N2F – Költségjelentésekbe, ki kell építeni őket az N2F – Költségjelentésekbe. N2F – Költségjelentések esetén a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az N2F - Költségjelentések vállalati webhelyére rendszergazdaként.

2. Kattintson a **Beállítások** gombra, majd válassza az **Előzetes beállítások lehetőséget** a legördülő menüből.

    ![N2F - Költség felhasználó hozzáadása](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza a Bal oldali navigációs panel **Felhasználók** lapján lehetőséget.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/user1.png)

4. Válassza a **+ Új felhasználó** lapot.

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/user2.png)

5. A **Felhasználó** csoportban hajtsa végre az alábbi lépéseket:

    ![N2F - Költségjelentések Konfiguráció](./media/n2f-expensereports-tutorial/user3.png)

    a. Az **E-mail cím** mezőbe írja be a felhasználó e-mail címét, például **brittasimon\@contoso.com**.

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    c. A **Név** mezőbe írja be a felhasználó nevét, például **BrittaSimon**.

    d. Válassza **a Szerepkör, a Közvetlen kezelő (N+1)** és **a Divízió** lehetőséget a szervezet igényeinek megfelelően.

    e. Kattintson **az Érvényesítés és meghívó elküldése gombra.**

    > [!NOTE]
    > Ha bármilyen problémával szembesül a felhasználó hozzáadása során, kérjük, forduljon az [N2F - Expense reports támogatási csapatához](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelN2F – Költségjelentések csempéjére kattint, automatikusan be kell jelentkeznie az N2F – Költségjelentések be, amelyekhez be állítja az Egyszeri bejelentkezés t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

