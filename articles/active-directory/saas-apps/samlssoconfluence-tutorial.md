---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO-val a resolution GmbH összefolyása érdekében | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAML SSO között a Resolution GmbH összefolyása érdekében.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9676a71940569b26d6b0b6bfef767108ae57d953
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161220"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO-val a resolution GmbH összefolyása esetén

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAML SSO-t a Resolution GmbH megoldása irt.
Az SAML SSO integrálása a megoldásos GmbH és az Azure AD összefolyásához a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az SAML SSO-hoz a Resolution GmbH összefolyása esetén.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az SAML Egyszeri bejelentkezéshez a Resolution GmbH (Single Sign-On) által az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az SAML SSO-val a Resolution GmbH összefolyásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* SAML SSO a felbontással összefolyáshoz GmbH egyszeri bejelentkezéses előfizetéssel

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* SAML SSO a szanálással összefolyáshoz A GmbH támogatja **az SP-t** és az **IDP** által kezdeményezett SSO-t

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása a Confluence by resolution GmbH számára a galériából

Az SAML SSO-nak a megoldásos GmbH-val történő összefolyása az Azure AD-be történő integrálásához hozzá kell adnia az SAML SSO-t a felbontással történő összefolyáshoz a GmbH-t a galériából a felügyelt SaaS-alkalmazások listájához.

**Saml SSO hozzáadása a Confluence by resolution GmbH-hoz a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML SSO for Confluence by resolution GmbH**parancsot, válassza az **SAML SSO for Confluence by resolution GmbH** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAML SSO a resolution GmbH-val összefolyásesetén az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését az SAML SSO-val a Resolution GmbH-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó saml SSO a felbontással való összefolyás a GmbH.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SAML Egyszeri bejelentkezés a resolution GmbH összefolyásához a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML SSO-t a Confluence by resolution GmbH Single Sign-On felbontásra](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAML SSO-t a Confluence by resolution GmbH tesztfelhasználóhoz](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – hogy a Britta Simon megfelelője az SAML SSO-ban a Resolution GmbH-hoz kapcsolódva kapcsolódjon a felhasználó Azure AD-megjelenítéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAML SSO-val a Resolution GmbH-val, hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAML SSO a resolution GmbH alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket, ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni:

    ![SAML SSO a felbontással összefolyáshoz GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást SP által kezdeményezett módban kívánja konfigurálni:

    ![SAML SSO a felbontással összefolyáshoz GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az SAML SSO-val a Confluence by resolution GmbH Ügyfél támogatási csapatához,](https://www.resolution.de/go/support) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Saml SSO konfigurálása összefolyáshoz a GmbH Single Sign-On felbontással

1. Egy másik böngészőablakban jelentkezzen be **az SAML SSO-ba a Resolution GmbH admin portálján,** mint rendszergazda.

2. Mutasson a fogaskerékre, és kattintson **a bővítményekre.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon1.png)

3. A program átirányítja a Rendszergazdai hozzáférés lapra. Írja be a jelszót, és kattintson a **Megerősítés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon2.png)

4. Az **ATLASSIAN MARKETPLACE (ATLASSIAN PIACTÉR)** lap Új **bővítmények keresése gombjára.** 

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon.png)

5. Keressen **saml single sign on (SSO) a torkolatánál,** és kattintson **a Telepítés** gombra az új SAML plugin telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon7.png)

6. A plugin telepítés indul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon9.png)

7.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Az új bővítmény konfigurálásához kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon11.png)

9. Ez új dugó tud is lenni alapít alatt **HASZNÁLÓK & BIZTONSÁG** pánt.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Az **SAML SingleSignOn Plugin Configuration (SAML SingleSignOn plugin konfigurációja)** lapon kattintson az **Új IdP hozzáadása** gombra az Identitásszolgáltató beállításainak konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon4.png)

11. Az **SAML-identitásszolgáltató kiválasztása lapon hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Állítsa be az **Azure AD-t** IdP-típusként.
    
    b. Adja hozzá az identitásszolgáltató **nevét** (például Az Azure AD).
    
    c. Adja hozzá az identitásszolgáltató (pl. Azure AD) **leírását.**
    
    d. Kattintson a **Tovább** gombra.
    
12. Az **Identitásszolgáltató konfigurációja** lapon kattintson a **Tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5b.png)

13. Az **SAML IdP metaadatok importálása lapon hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Kattintson a **Fájl betöltése** gombra, és válassza az 5.

    b. Kattintson **az Importálás** gombra.
    
    c. Várjon röviden, amíg az importálás sikeres lesz.
    
    d. Kattintson a **Tovább** gombra.
    
14. A **User ID attribútum és az átalakítási** lapon kattintson a **Tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. A **Felhasználó létrehozása és frissítése** lapon kattintson a & Mentés a **Következő** gombra a beállítások mentéséhez.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. A **Beállítások tesztelése** lapon kattintson a **Teszt kihagyása gombra, & manuálisan konfigurálja** a felhasználói teszt kihagyásához. Ez a következő szakaszban kerül végrehajtásra, és bizonyos beállításokat igényel az Azure Portalon. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. A megjelenő párbeszédpanelen a **Teszt kihagyása azt jelenti...** kattintson az **OK**gombra.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6c.png)

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az SAML SSO-hoz a resolution GmbH összefolyása érdekében.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **SAML SSO for Confluence by resolution GmbH**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza ki **az SAML SSO-t a Resolution GmbH felbontással**.

    ![Az SAML SSO a megoldásonkénti összefolyáshoz GmbH hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása összefolyáshoz a Resolution GmbH tesztfelhasználója szerint

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az SAML SSO-ba a Resolution GmbH összefolyása érdekében, ki kell építeni őket az SAML SSO-ba a Resolution GmbH összefolyása érdekében.  
A SAML SSO a confluence by resolution GmbH, kiépítése egy manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az SAML SSO-ba a Resolution GmbH cég webhelyén, mint rendszergazda.

2. Mutasson a fogaskerékre, és kattintson a **Felhasználókezelés parancsra.**

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user1.png) 

3. A Felhasználók csoportban kattintson a **Felhasználók hozzáadása** fülre. A **"Felhasználó hozzáadása"** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user2.png) 

    a. A **Felhasználónév** mezőbe írja be a felhasználó e-mail címét, például Britta Simon.

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például Britta Simont.

    c. Az **E-mail** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    d. A **Jelszó** mezőbe írja be Britta Simon jelszavát.

    e. Kattintson **a Jelszó megerősítése** gombra, írja be újra a jelszót.
    
    f. Kattintson **a Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a SAML SSO for Confluence by resolution GmbH csempére kattint, automatikusan be kell jelentkeznie az SAML SSO-ba a Confluence-hez a Resolution GmbH által, amelyre beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

