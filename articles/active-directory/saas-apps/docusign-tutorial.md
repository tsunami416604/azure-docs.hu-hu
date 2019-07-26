---
title: 'Oktatóanyag: Azure Active Directory integráció a DocuSign-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499246"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Oktatóanyag: Azure Active Directory integráció a DocuSign

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a DocuSign a Azure Active Directory (Azure AD) szolgáltatással.
A DocuSign és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a DocuSign.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a DocuSign (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció DocuSign való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* DocuSign egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A DocuSign támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A DocuSign támogatja az [automatikus felhasználó](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial) -kiépítés használatát

## <a name="adding-docusign-from-the-gallery"></a>DocuSign hozzáadása a gyűjteményből

A DocuSign Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a DocuSign a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha DocuSign szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **DocuSign**kifejezést, válassza a **DocuSign** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![DocuSign az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az DocuSign-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a DocuSign kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés DocuSign való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[DocuSign egyszeri bejelentkezés konfigurálása](#configure-docusign-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre DocuSign-teszt felhasználót](#create-docusign-test-user)** – hogy a DocuSign Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés DocuSign való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **DocuSign** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![DocuSign tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval, amely a jelen oktatóanyagban az **SAML 2,0** -végpontok című szakaszban található meg.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A **DocuSign beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-docusign-single-sign-on"></a>DocuSign egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **DocuSign felügyeleti portálra** rendszergazdaként.

2. Az oldal jobb felső részén kattintson a profil **emblémára** , majd a **Ugrás a rendszergazdára**elemre.
  
    ![Egyszeri bejelentkezés konfigurálása][51]

3. A tartományi megoldások lapon kattintson a **tartományok** elemre.

    ![Egyszeri bejelentkezés konfigurálása][50]

4. A **tartományok** szakaszban kattintson a **jogcím tartomány**elemre.

    ![Egyszeri bejelentkezés konfigurálása][52]

5. A **jogcím a tartományhoz** párbeszédpanelen a **tartománynév** szövegmezőbe írja be a vállalati tartományt, majd kattintson a **jogcím**elemre. Győződjön meg arról, hogy a tartomány ellenőrzése megtörténik, és az állapota aktív.

    ![Egyszeri bejelentkezés konfigurálása][53]

6. A tartományi megoldások lapon kattintson az **identitás-szolgáltatók**elemre.
  
    ![Egyszeri bejelentkezés konfigurálása][54]

7. Az **identitás-szolgáltatók** szakaszban kattintson az **Identity Provider hozzáadása**elemre. 

    ![Egyszeri bejelentkezés konfigurálása][55]

8. Az **Identity Provider beállításai** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása][56]

    a. A **név** szövegmezőbe írja be a konfiguráció egyedi nevét. Ne használjon szóközt.

    b. Az **Identity Provider kiállító szövegmezőben**illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    c. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. Az **Identitáskezelő kijelentkezési URL-címe** szövegmezőbe illessze be a kijelentkezési **URL-címet**, amelyet a Azure Portalból másolt.

    e. Válassza a **AuthN-kérelem aláírása**lehetőséget.

    f. A **AuthN-kérelem küldéséhez**válassza a **post**lehetőséget.

    g. Mint a kijelentkezési **kérelem küldése a alapján**, válassza a beolvasás lehetőséget.

    h. Az **Egyéni attribútumok leképezése** szakaszban kattintson az **Új leképezés hozzáadása**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása][62]

    i. Válassza ki azt a mezőt, amelyet le szeretne képezni az Azure AD-jogcím alapján. Ebben a példában az **EmailAddress** jogcím a következő értékkel **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** van leképezve:. Ez az alapértelmezett jogcím neve az Azure AD-től az e-mail-jogcímhez, majd kattintson a **Save (Mentés**) gombra.

    ![Egyszeri bejelentkezés konfigurálása][57]

    > [!NOTE]
    > A megfelelő **felhasználói azonosító** használatával rendelje hozzá a felhasználót az Azure ad-ből DocuSign felhasználói leképezéshez. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket a szervezeti beállítások alapján.

    j. Az **Identity Provider-tanúsítványok** szakaszban kattintson a **tanúsítvány hozzáadása**elemre, majd töltse fel az Azure ad-portálról letöltött tanúsítványt, és kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása][58]

    k. Az **Identity Providers** szakaszban kattintson a **műveletek**, majd a **végpontok**elemre.

    ![Egyszeri bejelentkezés konfigurálása][59]

    l. A **DocuSign felügyeleti portál** **SAML 2,0-végpontok megtekintése** szakaszában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása][60]

    * Másolja a **szolgáltatói kiállító URL-címét**, majd illessze be a Azure Portal alapszintű **SAML-konfiguráció** szakaszának **azonosító** szövegmezőbe.

    * Másolja a **szolgáltatói bejelentkezési URL-címet**, majd illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal alapszintű **SAML-konfiguráció** szakaszában.

    * Kattintson a **Bezárás** gombra

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extensiona nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a DocuSign hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **DocuSign**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **DocuSign**lehetőséget.

    ![Az DocuSign hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-docusign-test-user"></a>DocuSign-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a DocuSign-ben. A DocuSign támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a DocuSign-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [DocuSign támogatási csapatával](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a DocuSign csempére kattint, automatikusan be kell jelentkeznie arra a DocuSign, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
