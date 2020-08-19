---
title: 'Oktatóanyag: Azure Active Directory-integráció a tabló online-nal | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a tabló online között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542517"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a tabló online-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tablót online Azure Active Directory (Azure AD) szolgáltatással. A tabló online és az Azure AD integrálásával a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a tabló online-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a tabló Online szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Tabló online egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A tabló online támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A tabló online konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Tabló online hozzáadása a katalógusból

A tabló online-integrációjának az Azure AD-be való konfigurálásához hozzá kell adnia a tablót az online katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **tabló online** kifejezést a keresőmezőbe.
1. Válassza a **tabló online** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a tabló Online szolgáltatással konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a tabló-online kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD SSO és a tabló online konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[tabló online SSO konfigurálása](#configure-tableau-online-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[tabló online tesztelési felhasználójának létrehozása](#create-tableau-online-test-user)** – a felhasználó Azure ad-beli képviseletéhez kapcsolódó B. Simon-beli partneri kapcsolattal rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a tabló Online szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **tabló online** alkalmazás-integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A tabló online tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > `<entityid>`Ebben az oktatóanyagban megjelenik az érték a **tabló online beállítása** szakaszban. Az entitás-azonosító érték az **Azure ad-azonosító** érték lesz a **tabló online beállítása** szakaszban.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **tabló online beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon \@ yourcompanydomain. Extension** nevet  
    Például: BrittaSimon \@ contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a tabló online-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **tabló online**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tabló online**lehetőséget.

    ![A tabló online hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-tableau-online-sso"></a>A tabló online SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a tabló online alkalmazásba. Lépjen a beállítások, majd a **hitelesítés** **menüpontra** .

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Az SAML engedélyezéséhez a **hitelesítési típusok** szakaszban. Jelölje be **a további hitelesítési módszer engedélyezése** jelölőnégyzetet, majd jelölje be az **SAML** jelölőnégyzetet.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Görgessen le a **Metaadatok importálása a tabló online** szakaszba.  Kattintson a Tallózás gombra, és importálja a metaadat-fájlt, amelyet az Azure AD-ből töltött le. Ezután kattintson az **alkalmaz**gombra.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Az **egyeztetési érvényesítések** szakaszban szúrja be a megfelelő identitás-szolgáltatói **jogcímet az e-mail-cím**, a **keresztnév**és a **vezetéknév**mezőbe. Az információk lekérése az Azure AD-ből: 
  
    a. A Azure Portal lépjen a **tabló online** alkalmazás-integráció oldalára.

    b. A **felhasználói attribútumok & jogcímek** szakaszban kattintson a Szerkesztés ikonra.

   ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection.png)

    c. Másolja a névtér értékét ezekhez az attribútumokhoz: givenName, e-mail-cím és vezetéknév a következő lépések segítségével:

   ![Azure AD egyszeri bejelentkezés](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Kattintson a **User. givenName** értékre

    e. Másolja az értéket a **névtér** szövegmezőből.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/attributesection2.png)

    f. Az e-mailek és a vezetéknév névteri értékeinek másolásához ismételje meg a fenti lépéseket.

    : Váltson a tabló online alkalmazásra, majd állítsa be a **felhasználói attribútumok & jogcímek** szakaszt a következőképpen:

    * **E-mail: e-mail** vagy **userPrincipalName**

    * Keresztnév: **givenName**

    * **Vezetéknév** : név

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tabló online tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a tabló online-ban.

1. A **tabló online**oldalon kattintson a **Beállítások** , majd a **hitelesítés** szakaszra. Görgessen le a **felhasználók kezelése** szakaszhoz. Kattintson a **felhasználók hozzáadása** elemre, majd az **e-mail-címek megadása**lehetőségre.
  
    ![Azure AD-tesztkörnyezet létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Válassza **a felhasználók hozzáadása (SAML) hitelesítés**lehetőséget. Az **e-mail címek megadása** szövegmezőben adja hozzá a Britta. Simon \@ contoso.com
  
    ![Azure AD-tesztkörnyezet létrehozása](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Kattintson a **felhasználók hozzáadása**elemre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a tabló online csempére kattint, automatikusan be kell jelentkeznie a tabló online-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)