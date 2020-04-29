---
title: 'Oktatóanyag: Azure Active Directory integráció a Fluxx Labs szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Fluxx Labs között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102391"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Oktatóanyag: Azure Active Directory integráció a Fluxx Labs szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Fluxx Labs-t Azure Active Directory (Azure AD) használatával.
A Fluxx Labs és az Azure AD integrálásával a következő előnyöket nyújtja:

* A Fluxx Labs szolgáltatáshoz hozzáféréssel rendelkező Azure AD-t is vezérelheti.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Fluxx Labsbe (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Fluxx Labs szolgáltatással való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Fluxx Labs egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Fluxx Labs támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs hozzáadása a gyűjteményből

A Fluxx Labs Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Fluxx Labs szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Fluxx Labs a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Fluxx Labs**kifejezést, válassza az **Fluxx Labs** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Fluxx Labs az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Fluxx Labs szolgáltatással konfigurálhatja és tesztelheti egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Fluxx Labs-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Fluxx Labs szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[Fluxx Labs egyszeri bejelentkezésének konfigurálása](#configure-fluxx-labs-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Fluxx Labs-tesztkörnyezet létrehozása](#create-fluxx-labs-test-user)** – ha a Fluxx Labs szolgáltatásban a felhasználó Azure ad-Britta kapcsolódó, a Labs-ben található partneri jogosultsággal rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az Fluxx Labs szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Fluxx Labs** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon hajtsa végre a következő lépéseket:

    ![Fluxx Labs-tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Környezet | URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | Környezet | URL-minta|
    |-------------|------------|
    | Production | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Üzem előtti | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek megszerzéséhez forduljon a [Fluxx Labs](mailto:travis@fluxxlabs.com) ügyfélszolgálatához. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Fluxx Labs beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-fluxx-labs-single-sign-on"></a>Az Fluxx Labs egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhelyre.

2. Válassza a **rendszergazda** lehetőséget a **Beállítások** szakaszban.

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config1.png)

3. A felügyeleti panelen válassza a **beépülő modulok** > **integrációk** elemet, majd válassza az **SAML SSO-(letiltva) lehetőséget.**

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config2.png)

4. Az attribútum szakaszban hajtsa végre a következő lépéseket:

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config3.png)

    a. Jelölje be az **SAML SSO** jelölőnégyzetet.

    b. A **kérelem elérési útja** szövegmezőbe írja be a következőt: **/Auth/SAML**.

    c. A **visszahívási útvonal** szövegmezőbe írja be a következőt: **/Auth/SAML/callback**.

    d. Adja meg a válasz URL-címét **(az egyszeri bejelentkezési URL** -cím) szövegmezőbe írja be az Azure Portalban megadott **Válasz URL** -címet.

    e. A **célközönség (SP-entitás azonosítója)** szövegmezőbe írja be az **azonosító** értékét, amelyet a Azure Portal megadott.

    f. Illessze be a bejelentkezési URL-cím szövegmezőbe a Azure Portalból másolt **bejelentkezési URL** - **címet** .

    g. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    h. A **név azonosítójának formázása** szövegmezőbe írja be `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`az értéket.

    i. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A tartalom mentése után a mező üresen jelenik meg a biztonság esetében, az érték azonban a konfigurációban lett mentve.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extensiona nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az Fluxx Labs elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Fluxx Labs**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Fluxx Labs**elemet.

    ![Az Fluxx Labs hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs-tesztkörnyezet létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Fluxx Labs szolgáltatásba, azokat a Fluxx Labs szolgáltatásban kell kiépíteni. A Fluxx Labs esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be rendszergazdaként a Fluxx Labs vállalati webhelyére.

2. Kattintson a lent látható **ikonra**.

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config6.png)

3. Az irányítópulton kattintson az alábbi megjelenő ikonra az **új személyek** kártya megnyitásához.

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config4.png)

4. Az **új személyek** szakaszban hajtsa végre a következő lépéseket:

    ![Fluxx Labs-konfiguráció](./media/fluxxlabs-tutorial/config5.png)

    a. A Fluxx Labs az egyszeri bejelentkezéses bejelentkezések egyedi azonosítójaként használja az e-maileket. Töltse fel az **SSO UID** mezőt a felhasználó e-mail-címével, amely megfelel az e-mail-címnek, amelyet bejelentkezésként használ az SSO-ban.

    b. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Fluxx Labs csempére kattint, automatikusan be kell jelentkeznie a Fluxx Labs szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

