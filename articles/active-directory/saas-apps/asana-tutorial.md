---
title: 'Oktatóanyag: Azure Active Directory a (z) és a (z) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a legördülő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: dd2c02f25584fd32acd32277a1dd2300c6a98e73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549534"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Oktatóanyag: Azure Active Directory integráció

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) segítségével.
A (z) és az Azure AD integrálásával a következő előnyöket nyújtja:

* Megadhatja, hogy az Azure AD-ban mely felhasználók férhetnek hozzá.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a (z) és az egyszeri bejelentkezésre az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációt a (z) a következő elemekre kell konfigurálnia:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Az egyszeri bejelentkezés engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A az **SP** által kezdeményezett egyszeri bejelentkezést támogatja

* A és a [felhasználók **automatikus** kiépítés támogatása](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>A gyűjtemény hozzáadása a katalógusból

A famentes integrációjának az Azure AD-be való konfigurálásához fel kell vennie a gyűjteményt a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A következő lépések végrehajtásával veheti fel a falemezt a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A **keresőmezőbe írja be**a következőt: a legördülő **lista az eredmények** panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A lista az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a **Britta Simon**nevű felhasználói teszt alapján konfigurálja és teszteli.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a következő kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a következő építőelemek használatával konfigurálható és tesztelhető:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[egyszeri bejelentkezés konfigurálása](#configure-asana-single-sign-on)** – az Sign-On beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. A felhasználók Azure AD-beli képviseletéhez kapcsolódó, a Britta Simon-t tartalmazó partneri kapcsolat **[létrehozása](#create-asana-test-user)** a felhasználó számára.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD-beli egyszeri bejelentkezés a következő lépések végrehajtásával konfigurálható:

1. A [Azure Portal](https://portal.azure.com/)a ( **z) a**(z) és a (z), a (z) **, a (** z)

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A legördülő tartományok és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet: `https://app.asana.com/`

    b. Az **azonosító (entitás azonosítója)** szövegmezőbe írja be az URL-címet: `https://app.asana.com/`

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A (z) **beállításnál adja** meg a megfelelő URL (eke) t a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-asana-single-sign-on"></a>Egy Sign-On

1. Egy másik böngészőablakban jelentkezzen be a-ba. Az egyszeri bejelentkezés a következőben való konfigurálásához a képernyő jobb felső sarkában lévő munkaterület nevére kattintva nyissa meg a munkaterület beállításait. Ezután kattintson a ** \<your workspace name\> Beállítások**elemre.

    ![Az egyszeri bejelentkezés beállításai](./media/asana-tutorial/tutorial_asana_09.png)

2. A **szervezeti beállítások** ablakban kattintson az **Adminisztráció**elemre. Ezután kattintson a **tagoknak az SAML használatával történő bejelentkezéshez** az egyszeri bejelentkezés konfigurációjának engedélyezéséhez. A hajtsa végre a következő lépéseket:

    ![Önálló Sign-On szervezeti beállítások konfigurálása](./media/asana-tutorial/tutorial_asana_10.png)  

    a. A bejelentkezési **oldal URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**.

    b. Kattintson a jobb gombbal a Azure Portal letöltött tanúsítványra, majd nyissa meg a fájlt a Jegyzettömb vagy a kívánt szövegszerkesztő használatával. Másolja a tartalmat a BEGIN és a end Certificate cím között, és illessze be az **X. 509 tanúsítvány** szövegmezőbe.

3. Kattintson a **Mentés** gombra. Ha további segítségre van szüksége, nyissa meg a [következőt: az egyszeri bejelentkezés beállítása](https://asana.com/guide/help/premium/authentication#gl-saml) .

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
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a (z)-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a leválasztás **lehetőséget.**

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások **listában válassza a**leválasztás lehetőséget.

    ![Az alkalmazások listája](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-asana-test-user"></a>A felhasználó létrehozása a felhasználók számára

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása A az alapértelmezés szerint engedélyezve van az automatikus felhasználó-kiépítés. További részletekért tekintse [meg az automatikus](asana-provisioning-tutorial.md) felhasználó-kiépítés konfigurálását ismertető témakört.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre a következő lépéseket:**

Ebben a szakaszban egy Britta Simon nevű felhasználó létrehozását látja el a (m)-ben.

1. A **következő**oldalon lépjen a **csapatok** szakaszra a bal oldali panelen. Kattintson a plusz jelre.

    ![Azure AD-tesztkörnyezet létrehozása](./media/asana-tutorial/tutorial_asana_12.png)

2. Írja be a felhasználó e-mail-címét, például **Britta. simon \@ contoso.com** a szövegmezőbe, majd válassza a **meghívás**lehetőséget.

3. Kattintson a **Meghívás küldése**gombra. Az új felhasználó e-mailt fog kapni az e-mail-fiókjába. a felhasználónak létre kell hoznia és ellenőriznie kell a fiókot.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen rákattint a a-ra, az egyszeri bejelentkezés beállítása után automatikusan be kell jelentkeznie a következőre:. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A felhasználók üzembe helyezésének konfigurálása](asana-provisioning-tutorial.md)
