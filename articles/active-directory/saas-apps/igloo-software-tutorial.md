---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Igloo szoftverrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Igloo Software között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100582"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Oktatóanyag: Az Azure Active Directory integrációja az Igloo szoftverrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Igloo szoftvert az Azure Active Directoryval (Azure AD).
Az Igloo szoftver integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Igloo szoftverhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve igloo szoftver (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és az Igloo Software konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Igloo Software egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Igloo szoftver támogatja **SP** kezdeményezett SSO
* Az Igloo Software támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-igloo-software-from-the-gallery"></a>Hozzátéve, Igloo szoftver a galériából

Az Igloo Software Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Igloo szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az Igloo szoftver galériából való hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Igloo Software**( Igloo Software ) **parancsot** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Igloo Szoftver az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Igloo Software szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó az Igloo Software közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Igloo Szoftverrel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja Igloo Software Single Sign-On](#configure-igloo-software-single-sign-on)** - konfigurálni az egyszeri bejelentkezés ibeállítások at alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Igloo Software teszt felhasználó](#create-igloo-software-test-user)** -, hogy egy megfelelője Britta Simon igloo szoftver, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Igloo szoftverrel való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Igloo Software** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Igloo Software Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<company name>.igloocommmunities.com`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<company name>.igloocommmunities.com/saml.digest`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [az Igloo Software Client támogatási csapatával,](https://www.igloosoftware.com/services/support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Igloo szoftver beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-igloo-software-single-sign-on"></a>Az Igloo szoftver egyszeri bejelentkezése

1. Egy másik böngészőablakban jelentkezzen be az Igloo Software cég webhelyére rendszergazdaként.

2. Nyissa meg a **Vezérlőpultot**.

     ![Vezérlőpult](./media/igloo-software-tutorial/ic799949.png "Vezérlőpult")

3. A **Tagság** lapon kattintson a **Bejelentkezési beállítások gombra.**

    ![Bejelentkezési beállítások](./media/igloo-software-tutorial/ic783968.png "Bejelentkezési beállítások")

4. Az SAML konfigurációja csoportban kattintson az **SAML-hitelesítés konfigurálása gombra.**

    ![SAML konfiguráció](./media/igloo-software-tutorial/ic783969.png "SAML konfiguráció")

5. Az **Általános konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Általános konfiguráció](./media/igloo-software-tutorial/ic783970.png "Általános konfiguráció")

    a. A **Kapcsolat név** mezőbe írja be a konfiguráció egyéni nevét.

    b. Az **IdP-bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Az **IdP-kijelentkezés URL-címmezőjébe** illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. Válassza **a Kijelentkezési válasz és a HTTP-típus kérése** lehetőséget **POST**néven.

    e. Nyissa meg az **alap-64** kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a **Nyilvános tanúsítvány** szövegdobozába.

6. A **Válasz és hitelesítés konfigurációja csoportban**hajtsa végre a következő lépéseket:

    ![Válasz és hitelesítés konfigurációja](./media/igloo-software-tutorial/IC783971.png "Válasz és hitelesítés konfigurációja")
  
    a. **Identitásszolgáltatóként**válassza a **Microsoft ADFS**lehetőséget.

    b. **Azonosítótípusként**válassza az **E-mail cím lehetőséget.** 

    c. Az **E-mail attribútum** mezőbe írja be az **e-mail címet.**

    d. Az Utónév attribútum szövegmezőbe írja be a givenname **(Utónév attribútum)** mezőbe a **givenname (Utónév**attribútum) mezőbe.

    e. A **Vezetéknév attribútum** szövegmezőbe írja be a **vezetéknevet.**

7. A konfiguráció befejezéséhez hajtsa végre az alábbi lépéseket:

    ![Felhasználó létrehozása a bejelentkezéskor](./media/igloo-software-tutorial/IC783972.png "Felhasználó létrehozása a bejelentkezéskor") 

    a. A **Bejelentkezés kor a Felhasználó létrehozása**kor válassza az Új felhasználó létrehozása a webhelyen **bejelentkezéskor**lehetőséget.

    b. A **Bejelentkezési beállítások mezőben**válassza **az SAML használata gombot a "Bejelentkezés" képernyőn.**

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
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést az Igloo szoftverhez való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az **Igloo Software lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Igloo Software lehetőséget.**

    ![Az Igloo Szoftver hivatkozás az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-igloo-software-test-user"></a>Igloo szoftver tesztfelhasználó létrehozása

Nincs olyan műveletelem, amely konfigurálna a felhasználók kiépítését az Igloo szoftverbe.  

Amikor egy kijelölt felhasználó megpróbál bejelentkezni az Igloo Software-be a hozzáférési panelen, az Igloo Software ellenőrzi, hogy a felhasználó létezik-e.  Ha még nincs elérhető felhasználói fiók, azt az Igloo Software automatikusan létrehozza.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Igloo Szoftver csempére kattint, automatikusan be kell jelentkeznie az Iglu szoftverbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)