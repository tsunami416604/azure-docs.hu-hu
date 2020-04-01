---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a 123ContactForm alkalmazással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a 123ContactForm között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8328ac4008950d67cf5645c6a48c48d1196085
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154930"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Oktatóanyag: Az Azure Active Directory integrációja a 123ContactForm programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a 123ContactForm űrlapot az Azure Active Directoryval (Azure AD).
A 123ContactForm integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a 123ContactForm-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve 123ContactForm (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció 123ContactForm alkalmazással történő konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* 123ContactForm egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* 123ContactForm támogatja **az SP-t és az IDP** által kezdeményezett sso-t
* 123ContactForm támogatja **just in time** felhasználói kiépítés

## <a name="adding-123contactform-from-the-gallery"></a>A 123ContactForm hozzáadása a gyűjteményből

A 123ContactForm azure AD-be való integrálásának konfigurálásához hozzá kell adnia a 123ContactForm-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a 123ContactForm űrlapot a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **123ContactForm parancsot,** válassza a **123ContactForm elemet** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![123ContactForm az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a 123ContactForm alkalmazással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat 123ContactForm létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a 123ContactForm alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a 123ContactForm Single Sign-On](#configure-123contactform-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre 123ContactForm teszt felhasználó](#create-123contactform-test-user)** - egy megfelelője Britta Simon 123ContactForm, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a 123ContactForm alkalmazással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **123ContactForm** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![123ContactForm tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![123ContactForm tartomány és URL-címek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges URL-címekből és azonosítóból kell frissítenie, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Set up 123ContactForm (123ContactForm)** csoportban másolja a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-123contactform-single-sign-on"></a>Konfigurálja a 123ContactForm egyszeri bejelentkezést

1. Az egyszeri bejelentkezés **123ContactForm** oldalán történő [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) konfigurálásához nyissa meg a következő lépéseket, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/submit.png) 

    a. Az **E-mail** mezőbe írja be a felhasználó e-mail címét, például **\@BrittaSimon Contoso.com**.

    b. Kattintson **a Töltse fel** és böngésszen a letöltött metaadat-XML-fájlban, amelyet az Azure Portalról töltött le.

    c. Kattintson **a ŰRLAP küldése gombra.**

2. A **Microsoft Azure AD – Egyszeri bejelentkezés – Az alkalmazásbeállítások konfigurálása hajtsa** végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/123contactform-tutorial/url3.png)

    a. Ha az alkalmazást **IDP által kezdeményezett módban**szeretné konfigurálni, másolja a példány **azonosító** értékét, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában az **Azonosító** szövegmezőbe.

    b. Ha az alkalmazást **IDP által kezdeményezett módban**szeretné konfigurálni, másolja a **példány válasz URL-értékének** másolatát, és illessze be a **Válasz URL-szövegmezőbe** az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    c. Ha az alkalmazást SP **által kezdeményezett módban**szeretné konfigurálni, másolja a példány **SIGN ON URL-értékét,** és illessze be **a Bejelentkezési URL-cím** beírt szövegmezőbe az Azure Portal **alapszintű SAML-konfigurációs** szakaszában.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a 123ContactForm hozzáférést biztosít.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **123ContactForm lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **123ContactForm lehetőséget.**

    ![A 123ContactForm hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-123contactform-test-user"></a>123ContactForm tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a 123ContactForm alkalmazásban. 123ContactForm támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a 123ContactForm alkalmazásban, a hitelesítés után új felhasználó jön létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a 123ContactForm csempére kattint, automatikusan be kell jelentkeznie arra a 123ContactForm-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)