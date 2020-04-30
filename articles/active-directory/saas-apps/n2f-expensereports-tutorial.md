---
title: 'Oktatóanyag: Azure Active Directory integráció N2F-költségekkel kapcsolatos jelentésekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és N2F között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161287"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Oktatóanyag: Azure Active Directory integráció N2F-költségelszámolás

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a N2F-jelentéseket a Azure Active Directory (Azure AD) szolgáltatással.
A N2F és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a N2F-jelentésekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a N2F (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció N2F-jelentésekkel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* N2F-költségelszámolás – egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* N2F – a költségelszámolás támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>N2F-jelentések hozzáadása a katalógusból

A N2F-jelentések Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy N2F-jelentést a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha N2F-jelentéseket szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **N2F-költségelszámolás**kifejezést, válassza ki a **N2F jelentést** az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![N2F az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést egy **Britta Simon**nevű N2F alapján konfigurálja és teszteli.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti N2F kell létrehozni.

Az Azure AD egyszeri bejelentkezés N2F-jelentésekkel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[N2F-költségelszámolás egyszeri bejelentkezés konfigurálása](#configure-n2f---expense-reports-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[N2F létrehozása – a felhasználói tesztek tesztelése](#create-n2f---expense-reports-test-user)** – a felhasználó Azure ad-Britta összekapcsolt, a N2F-hez tartozó, a felhasználói fiókokat tartalmazó jelentések.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés N2F-jelentésekkel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **N2F-költségelszámolás alkalmazás-** integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, a felhasználónak nem kell végrehajtania a lépéseket, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![N2F – a költségekről szóló jelentések tartománya és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![N2F – a költségekről szóló jelentések tartománya és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://www.n2f.com/app/`

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

7. A **MyPolicies beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-n2f---expense-reports-single-sign-on"></a>N2F-jelentésekkel kapcsolatos egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a N2F-költségelszámolás vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások** elemre, majd válassza a legördülő menüből az **előre megadott beállítások** lehetőséget.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza a **Fiókbeállítások** lapot.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/configure2.png)

4. Válassza a **hitelesítés** lehetőséget, majd válassza **a + hitelesítési módszer hozzáadása** fület.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/configure3.png)

5. Hitelesítési módszerként válassza az **SAML Microsoft Office 365** lehetőséget.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/configure4.png)

6. A **hitelesítési módszer** szakaszban hajtsa végre a következő lépéseket:

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/configure5.png)

    a. Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    b. A **metaadatok URL-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    c. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a N2F-jelentésekhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **N2F-jelentések**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **N2F-jelentések**elemet.

    ![Az N2F tartozó jelentések hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-n2f---expense-reports-test-user"></a>N2F-jelentések létrehozása – felhasználói teszt

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a N2F, N2F-jelentésekben kell kiépíteni őket. N2F-költségelszámolás esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a N2F-költségelszámolás vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások** elemre, majd válassza a legördülő menüből az **előre megadott beállítások** lehetőséget.

    ![N2F – felhasználói költség hozzáadása](./media/n2f-expensereports-tutorial/configure1.png)

3. Válassza a **felhasználók** fület a bal oldali navigációs panelen.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/user1.png)

4. Válassza az **+ új felhasználó** fület.

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/user2.png)

5. A **felhasználó** szakaszban hajtsa végre a következő lépéseket:

    ![N2F – költségekkel kapcsolatos jelentések konfigurálása](./media/n2f-expensereports-tutorial/user3.png)

    a. Az **e-mail cím** szövegmezőbe írja be a felhasználó e-mail címét, például **brittasimon\@contoso.com**.

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta**nevet.

    c. A **név** szövegmezőbe írja be a felhasználó nevét (például **BrittaSimon**).

    d. Válassza a **szerepkör, a Direct Manager (N + 1)** lehetőséget, és a szervezeti igényeknek megfelelő **osztást** .

    e. Kattintson **az Érvényesítés gombra, és küldje el a meghívót**.

    > [!NOTE]
    > Ha problémák merülnek fel a felhasználó hozzáadásakor, forduljon a [N2F támogatási csapatához](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a N2F-költségelszámolás csempére kattint, automatikusan be kell jelentkeznie azokra a N2F-jelentésekre, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

