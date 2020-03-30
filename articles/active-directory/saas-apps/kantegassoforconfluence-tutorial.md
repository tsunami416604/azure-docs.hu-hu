---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Kantega SSO-val összefolyásesetén | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Kantega Egyszeri bejelentkezés között a összefolyáshoz.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 27fa0567eefbb50907c0ed6952333230e874c21d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099034"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Oktatóanyag: Az Azure Active Directory integrációja a Kantega SSO-val a folyásfolyáshoz

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Kantega SSO-t az Azure Active Directoryval (Azure AD) való összefolyáshoz.
A Kantega SSO integrálása az Azure AD-vel való összefolyáshoz a következő előnyökkel jár:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Kantega SSO összefolyása.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve kantega egyszeri bejelentkezés a confluence (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Kantega SSO-val a confluence esetében a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Kantega Egyszeri bejelentkezés egyszeri bejelentkezésre engedélyezett előfizetéshez

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Kantega SSO a torkolatánál támogatja **sp és IDP** kezdeményezett SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Kantega SSO hozzáadása a galéria összefolyása esetén

A Kantega SSO összefolyása az Azure AD-be történő integrálásának konfigurálásához hozzá kell adnia a Kantega SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Kantega SSO hozzáadása a gyűjteményből való összefolyáshoz hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Kantega SSO for Confluence (Kantega SSO for Confluence)** kifejezést az eredménypanelösszefolyásához, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához. **Kantega SSO for Confluence**

    ![Kantega SSO az eredményjegyzékben szereplő összefolyáshoz](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését a Kantega Egyszeri bejelentkezéssel a Confluence-nel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó kantegai egyszeri bejelentkezés a confluence közötti kapcsolat létrehozásához.

Konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés kantega egyszeri bejelentkezés a összefolyása, a következő építőelemek:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Kantega Egyszeri bejelentkezés t](#configure-kantega-sso-for-confluence-single-sign-on)** – konfigurálja az egyszeri bejelentkezés beállításait az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Kantega SSO összefolyása teszt felhasználó](#create-kantega-sso-for-confluence-test-user)** - egy megfelelője Britta Simon Kantega SSO összefolyása, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Kantega Egyszeri bejelentkezés sel a összefolyáshoz hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Kantega Egyszeri bejelentkezés** alkalmazásintegrációs lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Kantega SSO összefolyási tartomány és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Kantega SSO összefolyási tartomány és URL egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezek az értékek a Confluence plugin konfigurációja során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Set up Kantega SSO for Confluence (Összefolyás)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Kantega Egyszeri bejelentkezés konfigurálása összefolyásegyetlen bejelentkezéshez

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a **Összefolyás felügyeleti portálra.**

1. Mutasson a fogaskerékre, és kattintson **a bővítményekre.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Az **ATLASSIAN MARKETPLACE (ATLASSIAN PIACTÉR)** lap Új **bővítmények keresése gombjára.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon.png)

1. Keresés **Kantega SSO a torkolatánál SAML Kerberos** és kattintson **a Telepítés** gombra telepíteni az új SAML plugin.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon2.png)

1. A plugin telepítés elindul.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon3.png)

1. A telepítés befejezése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Az új bővítmény konfigurálásához kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Ez új dugó tud is lenni alapít alatt **HASZNÁLÓK & BIZTONSÁG** pánt.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon36.png)

1. Az **SAML** szakaszban. Válassza ki az **Azure Active Directory (Azure AD)** az **identitásszolgáltató hozzáadása** legördülő.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Válassza ki az előfizetési szintet **alapszintűként.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Az **Alkalmazás tulajdonságai** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Másolja az **Alkalmazásazonosító URI-értékét,** és használja **azonosítóként, válasz URL-címként és bejelentkezési URL-címként** az Azure Portal **alapszintű SAML-konfigurációja** szakaszban.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre az alábbi lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Válassza a **Metaadat-fájlt a számítógépen,** és töltse fel a metaadatfájlt, amelyet az Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A Név és az **SSO hely** szakaszában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Adja hozzá az identitásszolgáltató nevét az **identitásszolgáltató nevének** szövegmezőjében (például Az Azure AD).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **Tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon9.png)

1. A **Confluence felhasználói fiókok** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Válassza **a Felhasználók létrehozása lehetőséget a Confluence belső címtárában, ha szükséges,** és adja meg a csoport megfelelő nevét a felhasználók számára (többszörös nem is lehet. vesszővel elválasztott csoportok száma).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Az **Azure AD ismert tartományai** szakaszban hajtsa végre a következő lépéseket: 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Válassza az **Ismert tartományok lehetőséget** a lap bal oldali paneljén.

    b. Írja be a tartománynevet az **Ismert tartományok** mezőbe.

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
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Kantega Egyszeri bejelentkezés összefolyása hozzáférést biztosít.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Kantega SSO for Confluence lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Kantega SSO for Confluence**lehetőséget.

    ![A Kantega SSO az összefolyáshoz hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Kantega SSO létrehozása összefolyásteszt-felhasználó számára

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a folyásfolyásba, ki kell építeni őket a Confluence-be. Abban az esetben, Kantega SSO összefolyása esetén kiépítése egy manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Kantega SSO-ba a Confluence cég webhelyén rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **Felhasználókezelés parancsra.**

    ![Alkalmazott hozzáadása](./media/kantegassoforconfluence-tutorial/user1.png)

1. A Felhasználók csoportban kattintson a **Felhasználók hozzáadása** fülre. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforconfluence-tutorial/user2.png)

    a. A **Felhasználónév** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például Britta Simont.

    c. Az **E-mail** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    d. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    e. Kattintson **a Jelszó megerősítése** gombra, írja be újra a jelszót.

    f. Kattintson **a Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Kantega SSO for Confluence csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO-ba az Összefolyáshoz, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

