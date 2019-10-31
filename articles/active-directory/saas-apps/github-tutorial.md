---
title: 'Oktatóanyag: Azure Active Directory integráció a GitHubmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a GitHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159218"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Oktatóanyag: Azure Active Directory integráció a GitHubtal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a GitHubot Azure Active Directory (Azure AD-val).
A GitHub és az Azure AD integrálásával a következő előnyöket nyújtja:

* A GitHubhoz hozzáférő Azure AD-ben is szabályozható.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a GitHubba (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a GitHubral való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* GitHub [vállalati felhőben](https://help.github.com/articles/github-s-products/#github-enterprise)létrehozott GitHub-szervezet, amelyhez a [GitHub Enterprise számlázási csomag](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) szükséges

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A GitHub támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A GitHub támogatja a [felhasználók **automatikus** kiépítési felállítását](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>A GitHub hozzáadása a gyűjteményből

A GitHub Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a GitHubot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A GitHub a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **GitHub**kifejezést, válassza a **GitHub.com** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![GitHub az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a GitHubon egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a GitHubon lévő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a GitHub használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[GitHub egyszeri bejelentkezés konfigurálása](#configure-github-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre GitHub-teszt felhasználót](#create-github-test-user)** – hogy rendelkezzen egy, a Britta Simon in githubon lévő, a felhasználó Azure ad-beli képviseletéhez kapcsolódó ügyféllel.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a GitHub használatával történő konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **GitHub** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![GitHub-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/orgs/<entity-id>/sso`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Vegye figyelembe, hogy ezek nem valódi értékek. Ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval kell frissítenie. Itt javasoljuk, hogy a karakterlánc egyedi értékét használja az azonosítóban. Az értékek beolvasásához lépjen a GitHub-adminisztrátor szakaszra.

5. A GitHub-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A GitHub-alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail**használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és módosítja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **GitHub beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-cím

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-github-single-sign-on"></a>GitHub egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a GitHub-szervezeti webhelyre rendszergazdaként.

2. Navigáljon a **beállításokhoz** , és kattintson a **Biztonság** elemre.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Jelölje be az **SAML-hitelesítés engedélyezése** jelölőnégyzetet, amely az egyszeri bejelentkezés konfigurációs mezőit mutatja be. Ezután az egyszeri bejelentkezési URL-cím használatával frissítse az egyszeri bejelentkezési URL-címet az Azure AD-konfigurációban.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Konfigurálja a következő mezőket:

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    b. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. Nyissa meg a letöltött tanúsítványt Azure Portal a Jegyzettömbben, illessze be a tartalmat a **nyilvános tanúsítvány** szövegmezőbe.

    d. Kattintson az **Edit (Szerkesztés** ) ikonra **az aláírási módszer** és a **kivonatoló módszer** **RSA-SHA1** és **SHA1** – **RSA-sha256** és **sha256** szerinti szerkesztéséhez az alább látható módon.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Kattintson a **SAML-konfiguráció tesztelése** elemre annak ellenőrzéséhez, hogy nincs-e érvényesítési hiba vagy hiba az egyszeri bejelentkezés során.

    ![Beállítások](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az egyszeri bejelentkezés a GitHubon egy adott szervezetbe történik a GitHubon, és nem helyettesíti a GitHub hitelesítését. Ezért ha a felhasználó github.com-munkamenete lejárt, előfordulhat, hogy az egyszeri bejelentkezés során a GitHub AZONOSÍTÓját/jelszavát kell hitelesítenie.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure egyszeri bejelentkezést a GitHubhoz való hozzáférés megadásával engedélyezi a Britta Simon számára.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **GitHub**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **GitHub**elemet.

    ![A GitHub-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-github-test-user"></a>GitHub-teszt felhasználó létrehozása

A szakasz célja, hogy létrehozzon egy Britta Simon nevű felhasználót a GitHubban. A GitHub támogatja a felhasználók automatikus üzembe helyezését, amely alapértelmezés szerint engedélyezve van. További részletekért tekintse [meg az automatikus](github-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a GitHub vállalati webhelyére rendszergazdaként.

2. Kattintson a **személyek**elemre.

    ![Személyek](./media/github-tutorial/tutorial_github_config_github_08.png "Emberek")

3. Kattintson a **tag meghívása**elemre.

    ![Felhasználók meghívása](./media/github-tutorial/tutorial_github_config_github_09.png "Felhasználók meghívása")

4. A **tag meghívása** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Az **e-mail** szövegmezőbe írja be a Britta Simon-fiók e-mail-címét.

    ![Személyek meghívása](./media/github-tutorial/tutorial_github_config_github_10.png "Személyek meghívása")

    b. Kattintson a **Meghívás küldése**gombra.

    ![Személyek meghívása](./media/github-tutorial/tutorial_github_config_github_11.png "Személyek meghívása")

    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a GitHub csempére kattint, automatikusan be kell jelentkeznie a GitHubba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
