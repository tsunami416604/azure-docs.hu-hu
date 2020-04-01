---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a hosted graphite-tal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a hosted graphite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6c5b689d00bd1adad820043840c43f49666655c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158081"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Oktatóanyag: Az Azure Active Directory integrációja a hosted graphite-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a hosted graphite-t az Azure Active Directoryval (Azure AD).
A hosted graphite integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a hosted graphite.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve hosted graphite (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a hosted graphite,a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Hosted Graphite egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Hosted Graphite támogatja **az SP és IDP** által kezdeményezett SSO
* Hosted Graphite támogatja **just in time** felhasználói kiépítés

## <a name="adding-hosted-graphite-from-the-gallery"></a>Hosted Graphite hozzáadása a galériából

A hosted graphite azure AD-be való integrálásának konfigurálásához hozzá kell adnia a felügyelt SaaS-alkalmazások listájához a tárni kívánt grafitot a katalógusból.

**Ha a tárból szeretne üzemeltetett grafitot hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Hosted Graphite ( Hosted Graphite ) parancsot,** válassza a Hosted **Graphite** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Hosted Graphite az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Hosted Graphite-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó hosted graphite létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a hosted graphite-val a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a hosted graphite single sign-on](#configure-hosted-graphite-single-sign-on)** -, hogy konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hosted Graphite teszt felhasználó](#create-hosted-graphite-test-user)** – egy megfelelője Britta Simon hosted graphite, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Hosted Graphite-val hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Hosted Graphite** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Hosted Grafit tartomány és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.hostedgraphite.com/metadata/<user id>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.hostedgraphite.com/complete/saml/<user id>`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Hosted Grafit tartomány és URL egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Kérjük, vegye figyelembe, hogy ezek nem a valós értékek. Ezeket az értékeket a tényleges azonosítóval, a válasz URL-jével és a bejelentkezési URL-címmel kell frissítenie. Ezeknek az értékeknek a lekért, akkor nyissa meg az Access->SAML telepítő az alkalmazás oldalán, vagy kapcsolat [házigazdája Graphite támogatási csapat](mailto:help@hostedgraphite.com).

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Beállított hosted graphite** szakaszban másolja a megfelelő URL-cím(ek) szerint a követelmény.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-hosted-graphite-single-sign-on"></a>Hosted Grafit egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be a házigazdája grafit-bérlőrendszergazdaként.

2. Nyissa meg az **oldalsáv SAML telepítőlapját** (**Access -> SAML telepítő**).

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Ellenőrizze, hogy ezek az RI-k megfelelnek-e az Azure Portal **alapszintű SAML konfigurációs** szakaszában végzett konfigurációnak.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Az **entitás- vagy kiállítóazonosító** és **az sso-bejelentkezési URL-címszövegeibe** illessze be az Azure Hirdetési **azonosító** és **a bejelentkezési URL értékét,** amelyet az Azure Portalról másolt.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Válassza **az Írásvédett beállítást** alapértelmezett felhasználói **szerepkörként.**

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Nyissa meg az alap-64 kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **X.509 tanúsítvány** szövegdobozába.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a hosted graphite hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Hosted Graphite**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Hosted Graphite**lehetőséget.

    ![A hosted graphite hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-hosted-graphite-test-user"></a>Hosted Graphite tesztfelhasználó létrehozása

In this section, a user called Britta Simon is created in Hosted Graphite. Hosted Graphite támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a hosted graphite, egy új jön létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a Hosted Graphite támogatási csapatával a . <mailto:help@hostedgraphite.com>

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Hosted Graphite csempére kattint, automatikusan be kell jelentkeznie a szolgáltatott grafitba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

