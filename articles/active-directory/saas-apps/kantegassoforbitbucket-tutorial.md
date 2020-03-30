---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Kantega SSO for Bitbucket szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Kantega SSO for Bitbucket között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099214"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Oktatóanyag: Az Azure Active Directory integrációja a Kantega SSO for Bitbucket szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Kantega SSO for Bitbucket szolgáltatást az Azure Active Directoryval (Azure AD).
A Kantega SSO integrálása a Bitbucket és az Azure AD számára a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki rendelkezik hozzáféréssel a Kantega SSO bitbucket.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve kantega sso for Bitbucket (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Kantega SSO for Bitbucket szolgáltatáshoz a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Kantega SSO a Bitbucket egyszeri bejelentkezésre engedélyezett előfizetéshez

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Kantega SSO for Bitbucket támogatja **sp és IDP** kezdeményezett SSO

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Kantega SSO hozzáadása a Bitbucket-hez a galériából

A Kantega SSO for Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Kantega SSO-t a Bitbucket-hez a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Bővítményhez hozzá szeretné adni a Kantega SSO-t a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Kantega SSO for Bitbucket kifejezést**, válassza **a Bővítmény kantega sso-ját** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO a Bitbucket az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését a Kantega SSO for Bitbucket szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó között Kantega SSO a Bitbucket létre kell hozni.

Konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés kantega sso a Bitbucket, a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Kantega Egyszeri bejelentkezés t -](#configure-kantega-sso-for-bitbucket-single-sign-on)** konfigurálja az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Kantega SSO bitbucket teszt felhasználó](#create-kantega-sso-for-bitbucket-test-user)** - egy megfelelője Britta Simon Kantega SSO a Bitbucket, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Kantega Egyszeri bejelentkezés a Bitbucket alkalmazáshoz hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Kantega SSO for Bitbucket** alkalmazásintegrációs lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Kantega SSO bitbucket domain és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Kantega SSO bitbucket domain és URL egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezek az értékek a Bitbucket plugin konfigurációja során érkeznek, amelyet a bemutató későbbi részében ismertetünk.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **Set up Kantega SSO for Bitbucket (Webhely beállítása bitbucket)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Kantega SSO konfigurálása bitbucket egyszeri bejelentkezéshez

1. Egy másik böngészőablakban jelentkezzen be a Bitbucket felügyeleti portálra rendszergazdaként.

1. Kattintson a fogaskerék re, majd az **Új bővítmények keresése gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Keresés **Kantega SSO a Bitbucket SAML & Kerberos** és kattintson **a Telepítés** gombra telepíteni az új SAML plugin.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. A plugin telepítés elindul.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. A telepítés befejezése után. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Az új bővítmény konfigurálásához kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. Az **SAML** szakaszban. Válassza ki az **Azure Active Directory (Azure AD)** az **identitásszolgáltató hozzáadása** legördülő.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Válassza ki az előfizetési szintet **alapszintűként.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Az **Alkalmazás tulajdonságai** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Másolja az **Alkalmazásazonosító URI-értékét,** és használja **azonosítóként, válasz URL-címként és bejelentkezési URL-címként** az Azure Portal **alapszintű SAML-konfigurációja** szakaszban.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Válassza a **Metaadat-fájlt a számítógépen,** és töltse fel a metaadatfájlt, amelyet az Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A Név és az **SSO hely** szakaszában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Adja hozzá az identitásszolgáltató nevét az **identitásszolgáltató nevének** szövegmezőjében (például Az Azure AD).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **Tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. A **Bitbucket felhasználói fiókok csoportban** hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Válassza **a Felhasználók létrehozása a Bitbucket belső könyvtárában lehetőséget, ha szükséges,** és adja meg a csoport megfelelő nevét a felhasználók számára (többszörös nem is lehet. vesszővel elválasztott csoportok száma).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Az **Azure AD ismert tartományai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforbitbucket-tutorial/addon12.png)

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Kantega SSO a Bitbucket.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a Kantega SSO for Bitbucket**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Kantega SSO for Bitbucket lehetőséget.**

    ![A Kantega SSO for Bitbucket link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Kantega SSO létrehozása a Bitbucket teszt felhasználószámára

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Bitbucketbe, ki kell építeni őket a Bitbucketbe. A Bitbucket Kantega SSO esetén a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Bitbucket vállalati webhelyére rendszergazdaként.

1. Kattintson a beállítások ikonra.

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. A **Felügyelet** lap csoportban kattintson a **Felhasználók gombra.**

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kattintson **a Felhasználó létrehozása gombra.**

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. A **Felhasználó létrehozása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. A **Felhasználónév** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például Britta Simon.

    c. Az **E-mail cím** mezőbe írja be Brittasimon@contoso.coma felhasználó e-mail címét, például .

    d. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    e. A **Jelszó megerősítése** mezőbe írja be újra a felhasználó jelszavát.

    f. Kattintson **a Felhasználó létrehozása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Bitbucket-hez készült Kantega SSO csempére kattint, automatikusan be kell jelentkeznie a Tárolóeszköz-tároló Kantega SSO-jába, amelyhez be állítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

