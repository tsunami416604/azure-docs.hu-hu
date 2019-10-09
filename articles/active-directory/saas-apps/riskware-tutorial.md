---
title: 'Oktatóanyag: Azure Active Directory integráció a riskware-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és riskware között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027116"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Oktatóanyag: Azure Active Directory integráció a riskware

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a riskware a Azure Active Directory (Azure AD) szolgáltatással.
A riskware és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a riskware.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a riskware (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció riskware való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Riskware egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A riskware támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-riskware-from-the-gallery"></a>Riskware hozzáadása a gyűjteményből

A riskware Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a riskware a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha riskware szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **riskware**kifejezést, válassza a **riskware** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Riskware az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az riskware-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a riskware kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés riskware való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Riskware egyszeri bejelentkezés konfigurálása](#configure-riskware-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre riskware-teszt felhasználót](#create-riskware-test-user)** – hogy a riskware Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés riskware való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **riskware** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Riskware tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    | Környezet| URL-minta|
    |--|--|
    | Felhasználói tesztelés|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | ÉLES| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:
    
    | Környezet| URL-minta|
    |--|--|
    | Felhasználói tesztelés| `https://riskcloud.net/uat` |
    | ÉLES| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a riskware ügyfélszolgálati [csapatához](mailto:support@pansoftware.com.au) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A **riskware beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-riskware-single-sign-on"></a>Riskware egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a riskware vállalati webhelyre rendszergazdaként.

1. A jobb felső sarokban kattintson a **karbantartás** gombra a karbantartás lap megnyitásához.

    ![Riskware-konfigurációk karbantartása](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás lapon kattintson a **hitelesítés**elemre.

    ![Riskware-konfiguráció AUTHEN](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. A **hitelesítés konfigurálása** lapon hajtsa végre a következő lépéseket:

    ![Riskware-konfiguráció authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. A hitelesítéshez válassza az **SAML** **értéket** .

    b. A **Code (kód** ) szövegmezőbe írja be a kódot, például AZURE_UAT.

    c. A **Leírás** szövegmezőbe írja be a leírást, például az Azure-konfigurációt az egyszeri bejelentkezéshez.

    d. Az **egyszeri bejelentkezés lapon** a szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    e. A kijelentkezési **oldal** szövegmezőben illessze be a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portalból másolt.

    f. A **post űrlapmező** szövegmezőbe írja be az SAML-t (például SAMLResponse) tartalmazó post Response nevű mezőnevet.

    g. Az **XML Identity tag neve** szövegmezőbe írja be a következőt: attribútum, amely az SAML-válaszban (például NameID) található egyedi azonosítót tartalmazza.

    h. Nyissa meg a letöltött **metaadatok XML-** fájlját Azure Portal a Jegyzettömbben, másolja a tanúsítványt a metaadatokból, és illessze be a **tanúsítvány** szövegmezőbe.

    i. A **felhasználói URL** szövegmezőben illessze be a **Válasz URL-cím**értékét, amelyet a támogatási csapattól kap.

    j. A **kiállító** szövegmezőbe illessze be az **azonosító**értékét, amelyet a támogatási csapattól kap.

    > [!Note]
    > Az értékek lekéréséhez forduljon a riskware ügyfélszolgálati [csapatához](mailto:support@pansoftware.com.au)

    k. Jelölje be a **post** jelölőnégyzetet.

    l. Jelölje be **az SAML-kérelem használata** jelölőnégyzetet.

    m. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: `brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a riskware hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **riskware**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **riskware**lehetőséget.

    ![Az riskware hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-riskware-test-user"></a>Riskware-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a riskware, a riskware kell kiépíteni őket. A riskware-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a riskware biztonsági rendszergazdaként.

1. A jobb felső sarokban kattintson a **karbantartás** gombra a karbantartás lap megnyitásához. 

    ![Riskware-konfiguráció megtartása](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás lapon kattintson a **személyek**elemre.

    ![Riskware-konfiguráció személyek](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Válassza a **részletek** fület, és hajtsa végre a következő lépéseket:

    ![Riskware-konfiguráció részletei](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Válassza a **személy típusa** (például alkalmazott) lehetőséget.

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta**nevet.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

1. A **Biztonság** lapon hajtsa végre a következő lépéseket:

    ![Riskware-konfiguráció biztonsága](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. A **hitelesítés** szakaszban válassza ki a **hitelesítési** módot, amelyet úgy kell beállítania, mint az egyszeri bejelentkezéses Azure-konfiguráció.

    b. A **Bejelentkezés részletei** szakasz **felhasználói azonosító** szövegmezőbe írja be a következőhöz hasonló felhasználó e-mail címét: `brittasimon@contoso.com`.

    c. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

1. A **szervezet** lapon hajtsa végre a következő lépéseket:

    ![Riskware-konfigurációs org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Válassza a **Level1** -szervezetként lehetőséget.

    b. A **személy elsődleges munkahelye** szakaszban a **hely** szövegmezőbe írja be a helyét.

    c. Az **alkalmazott** szakaszban válassza az **alkalmazotti állapot** (alkalmi) lehetőséget.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a riskware csempére kattint, automatikusan be kell jelentkeznie arra a riskware, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
