---
title: 'Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a Solution GmbH összefolyásánál | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SAML SSO között a Solution GmbH összefolyásánál.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161220"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory integráció az SAML SSO-nal a Solution GmbH összefolyásánál

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAML SSO-t a Solution GmbH Azure Active Directory (Azure AD) szolgáltatással való összefolyásánál.
Az SAML SSO az Azure AD-vel való összevonáshoz való integrálásával az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy az SAML SSO-nal Hogyan férhet hozzá a Resolution GmbH összefolyásánál.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAML SSO-ba az Azure AD-fiókkal való összefolyásánál (egyszeri bejelentkezés).
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt az SAML SSO-nal szeretné konfigurálni a Solution GmbH összevonásához, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SAML SSO a Resolution GmbH egyszeri bejelentkezésre engedélyezett előfizetéssel való összefolyásánál

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Resolution GmbH által összefolyásánál az SAML SSO támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>SAML SSO hozzáadása az összefolyásánál

Az SAML SSO az Azure AD-be való összevonáshoz való integrálásának konfigurálásához az SAML SSO-t hozzá kell adnia a katalógusból a Managed SaaS-alkalmazások listájához.

**A következő lépések végrehajtásával adhat hozzá SAML SSO-t a Resolution GmbH által összefolyásánál a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML SSO-t a Resolution GmbH összefolyásánál**: válassza ki az **SAML SSO-t a Resolution GmbH általi összefolyásánál** az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAML SSO az eredmények listájában a megoldási szolgáltatáshoz való összefolyásánál](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SAML SSO-nal konfigurálja és teszteli az **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni az SAML SSO-hoz a Resolution GmbH összefolyásánál.

Ha az Azure AD egyszeri bejelentkezést az SAML SSO-nal konfigurálja és tesztelni kívánja a Solution GmbH összefolyásánál, a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML SSO-t a megoldáshoz](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)** való összekapcsoláshoz az egyszeri bejelentkezéssel – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[SAML SSO létrehozása a Solution GmbH által végzett összekapcsoláshoz](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó Britta Simon-nek a SAML SSO-ban való összekapcsolásához.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

A következő lépésekkel konfigurálhatja az Azure AD egyszeri bejelentkezést az SAML SSO-nal a Solution GmbH összefolyásánál:

1. A [Azure Portal](https://portal.azure.com/), az **SAML SSO for Resolution GmbH** Application Integration (az egyszeri bejelentkezés kiválasztása) lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni:

    ![SAML SSO a Solution GmbH-tartomány és az URL-címek egyszeri bejelentkezési adatainak összefolyásánál](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást SP-ben kezdeményezett módban szeretné konfigurálni:

    ![SAML SSO a Solution GmbH-tartomány és az URL-címek egyszeri bejelentkezési adatainak összefolyásánál](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Vegye fel a kapcsolatot az [SAML SSO-val a megoldási](https://www.resolution.de/go/support) feladatokkal való összefolyásánál. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>SAML SSO konfigurálása a Resolution GmbH egyszeri bejelentkezéssel való összefolyásánál

1. Egy másik böngészőablakban jelentkezzen be az SAML SSO-ba a **Resolution GmbH felügyeleti portáljának** rendszergazdájaként.

2. Mutasson a fogaskerékre, és kattintson a **bővítmények**elemre.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon1.png)

3. A rendszer átirányítja a rendszergazdai hozzáférés lapra. Adja meg a jelszót, és kattintson a **Confirm (megerősítés** ) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon2.png)

4. Az **ATLASSIAN-piactér** lapon kattintson az **új bővítmények keresése**elemre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon.png)

5. Keressen **SAML egyszeri bejelentkezést (SSO) a torkolatánál** , és kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon7.png)

6. A beépülő modul telepítése megkezdődik. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon9.png)

7.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon11.png)

9. Ez az új beépülő modul a **felhasználók & biztonság** lapon is megtalálható.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon3.png)
    
10. Az **SAML SingleSignon beépülő modul konfigurálása** lapon kattintson az **új identitásszolgáltató hozzáadása** gombra az Identitáskezelő beállításainak konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon4.png)

11. Az **SAML-identitás szolgáltatójának kiválasztása** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Állítsa be az **Azure ad** -t identitásszolgáltató típusként.
    
    b. Adja meg az identitás-szolgáltató **nevét** (például Azure ad).
    
    c. Adja meg az identitás-szolgáltató (például az Azure AD) **leírását** .
    
    d. Kattintson a **Tovább** gombra.
    
12. Az **identitás-szolgáltató konfigurációja** lapon kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5b.png)

13. Az **SAML-identitásszolgáltató metaadatainak importálása** oldalon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, és válassza ki az 5. lépésben letöltött metaadatokat tartalmazó XML-fájlt.

    b. Kattintson az **Importálás** gombra.
    
    c. Várjon röviden, amíg az importálás sikeres lesz.
    
    d. Kattintson a **tovább** gombra.
    
14. A **felhasználói azonosító attribútum és átalakítás** lapon kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. A **felhasználók létrehozása és frissítése** lapon kattintson a **Mentés gombra &** a beállítások mentése elem mellett.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. A **beállítások tesztelése** lapon kattintson a **teszt kihagyása & a manuális konfigurálás** lehetőségre a felhasználói teszt azonnali kihagyása érdekében. Ez a következő szakaszban lesz elvégezve, és néhány beállítást igényel a Azure Portalban. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. A megjelenő párbeszédablakban a **teszt kihagyása azt jelenti**, hogy..., kattintson **az OK**gombra.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson az SAML SSO-hoz a Solution GmbH összefolyásánál.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza ki **az SAML egyszeri bejelentkezést a feloldási GmbH általi összefolyásánál**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be a következőt: és válassza ki az **SAML SSO-t a Resolution GmbH összefolyásánál**.

    ![Az SAML SSO az alkalmazások listája](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO létrehozása a Resolution GmbH által végzett összefolyásánál

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek az SAML SSO-ba a megoldási szolgáltatással való összefolyásánál, ezeket az SAML SSO-nal kell kiépíteni a Solution GmbH összefolyásánál.  
Az SAML SSO a Solution GmbH összefolyásánál a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az SAML SSO-ba, hogy az a megoldás a vállalat webhelye rendszergazdaként való összefolyásánál.

2. Mutasson a fogaskerékre, és kattintson a **felhasználó-felügyelet**elemre.

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user1.png) 

3. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. A **"felhasználó hozzáadása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user2.png) 

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználó e-mail-címét, például Britta Simon.

    b. A **teljes név** szövegmezőbe írja be a felhasználó teljes nevét, például a Britta Simon nevet.

    c. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló Brittasimon@contoso.come-mail címet.

    d. A **jelszó** szövegmezőbe írja be a Britta Simon jelszavát.

    e. Kattintson a **Jelszó megerősítése** gombra a jelszó újbóli megadásához.
    
    f. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az SAML SSO-hoz való összefolyásánál a Resolution GmbH csempére kattint, automatikusan be kell jelentkeznie az SAML SSO-ba a Resolution GmbH összefolyásánál, amelyhez az SSO-t beállította. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

