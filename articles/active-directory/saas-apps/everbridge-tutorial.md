---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Everbridge-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Everbridge között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103255"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Oktatóanyag: Az Azure Active Directory integrációja az Everbridge-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Everbridge-et az Azure Active Directoryval (Azure AD).
Ha integrálja az Everbridge-et az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Everbridge-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek az Everbridge-be az Azure AD-fiókjukkal. Ezt a hozzáférés-vezérlést egyszeri bejelentkezésnek (SSO) nevezzük.
* Egyetlen központi helyen kezelheti fiókjait az Azure Portal használatával.
A szoftverszolgáltatásként (SaaS) alkalmazásintegrációaz Azure AD-vel kapcsolatos további tudnivalókat a [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az Everbridge-lel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* Egy Everbridge-előfizetés, amely egyszeri bejelentkezést használ.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Everbridge támogatja az IDP által kezdeményezett egyszeri szolgáltató t.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Everbridge hozzáadása az Azure Piactérről

Az Everbridge Azure AD-be való integrációjának konfigurálásához adja hozzá az Everbridge-et az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

Everbridge hozzáadása az Azure Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be **everbridge**. Válassza az **Everbridge** elemet az eredménypanelen, és válassza a **Hozzáadás**lehetőséget.

     ![Everbridge az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az Everbridge-lel britta Simon tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, hozzon létre egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó Everbridge.For single sign-on to work, establish a link relationship between a Azure AD user and the related user in Everbridge.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Everbridge-el hajtsa végre a következő építőelemeket:

- [Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on) hogy a felhasználók használhassák ezt a funkciót.
- [Konfigurálja az Everbridge-t az Everbridge-kezelő portálként egyetlen bejelentkezési portálként](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
- [Konfigurálja az Everbridge-t everbridge-i tagportálként egyetlen bejelentkezéskor](#configure-everbridge-as-everbridge-member-portal-single-sign-on) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
- [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
- [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
- [Hozzon létre egy Everbridge-tesztfelhasználót,](#create-an-everbridge-test-user) hogy az Everbridge-ben britta Simon megfelelője legyen, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
- [Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on) ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezéskonfigurálásához az Everbridge-lel hajtsa végre az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com/)az **Everbridge-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen jelölje ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza a **Szerkesztés** lehetőséget az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

    >[!NOTE]
    >Konfigurálja az alkalmazást kezelői portálként *vagy* tagportálként az Azure Portalon és az Everbridge portálon.

4. Ha az **Everbridge** alkalmazást **Az Everbridge-kezelő portálként**szeretné konfigurálni, az Egyszerű **SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Everbridge tartomány és URL-címek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely követi a mintát`https://sso.everbridge.net/<API_Name>`

    b. A **Válasz URL-címe** mezőbe írjon be egy URL-t, amely követi a mintát`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-értékekkel. Ezeknek az értékeknek a bekerüléséhez forduljon az [Everbridge támogatási csapatához.](mailto:support@everbridge.com) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákra is hivatkozhat.

5. Ha az **Everbridge** alkalmazást **Everbridge-tagportálként**szeretné konfigurálni, az Egyszerű **SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

  * Ha az alkalmazást IDP által kezdeményezett módban szeretné konfigurálni, kövesse az alábbi lépéseket:

     ![Everbridge tartomány és URL-címek egyszeri bejelentkezési információk IDP által kezdeményezett módban](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely követi a mintát`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. A **Válasz URL-címe** mezőbe írjon be egy URL-t, amely követi a mintát`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni, válassza **a További URL-ek beállítása lehetőséget,** és kövesse ezt a lépést:

     ![Everbridge tartomány és URL-címek egyszeri bejelentkezési információk sp-kezdeményezésű módban](common/both-signonurl.png)

     a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely követi a mintát`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító, válasz URL-cím és bejelentkezés i URL-értékekkel. Ezeknek az értékeknek a bekerüléséhez forduljon az [Everbridge támogatási csapatához.](mailto:support@everbridge.com) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákra is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** válassza a **Letöltés** lehetőséget az **összevonási metaadat-XML**letöltéséhez. Mentse a számítógépre.

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. Az **Everbridge beállítása** szakaszban másolja a követelményekhez szükséges URL-címeket:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure Hirdetés-azonosító
    - Kijelentkezés URL-címe

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Az Everbridge konfigurálása Everbridge-kezelőként egyetlen bejelentkezés

Ha az SSO-t **az Everbridge-en** **Everbridge-kezelő portálalkalmazásként** szeretné konfigurálni, kövesse az alábbi lépéseket.
 
1. Egy másik böngészőablakban jelentkezzen be az Everbridge-be rendszergazdaként.

1. A felső menüben válassza a **Beállítások** lapot. A **Biztonság**csoportban válassza **az Egyszeri bejelentkezés**lehetőséget.
   
     ![Egyszeri bejelentkezés konfigurálása](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. A **Név** mezőbe írja be az azonosítószolgáltató nevét. Ilyen például a vállalat neve.
   
     b. Az **API-név** mezőbe írja be az API nevét.
   
     c. Válassza **a Fájl kiválasztása lehetőséget** az Azure Portalról letöltött metaadatfájl feltöltéséhez.
   
     d. Az **SAML-identitás helyéhez**válassza **az Identitás elem a Tárgy utasítás NameIdentifier elemében**elemet.
   
     e. Az **Identitásszolgáltató bejelentkezési URL-címét** illessze be az Azure Portalról másolt **bejelentkezési URL-címértékbe.**
   
     f. A **Szolgáltató által kezdeményezett kérelemkötés**esetén válassza a HTTP **Átirányítás**lehetőséget.

     g. Kattintson a **Mentés** gombra.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Az Everbridge konfigurálása Everbridge-tagként egyetlen bejelentkezés

Ha az **Everbridge-en** egyszeri bejelentkezést **Everbridge-tagportálként**szeretné konfigurálni, küldje el a letöltött **összevonási metaadat-XML-t** az [Everbridge támogatási csapatának.](mailto:support@everbridge.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

A tesztfelhasználó britta Simon az Azure Portalon, kövesse az alábbi lépéseket.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![Felhasználók és minden felhasználó hivatkozásai](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen kövesse az alábbi lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőben adja meg a következőt: `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Írja le a **Jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Engedélyezze Britta Simon számára az Azure egyszeri bejelentkezést az Everbridge-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications** > **All Applications** >**Everbridge**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Everbridge**lehetőséget.

    ![Everbridge link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![Felhasználók és csoportok hivatkozása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Hozzárendelés hozzáadása párbeszédpanel](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájában **a Britta Simon** elemet. Válassza a **Kijelölés** lehetőséget a képernyő alján.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Válassza a **Kijelölés** lehetőséget a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-an-everbridge-test-user"></a>Everbridge tesztfelhasználó létrehozása

In this section, you create the test user Britta Simon in Everbridge. Ha felhasználókat szeretne hozzáadni az Everbridge platformon, működjön együtt az [Everbridge támogatási csapatával.](mailto:support@everbridge.com) Az egyszeri bejelentkezés használata előtt a felhasználókat létre kell hozni és aktiválni kell az Everbridge-ben. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Tesztelje az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával.

Amikor kiválasztja az Everbridge csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra az Everbridge-fiókra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

