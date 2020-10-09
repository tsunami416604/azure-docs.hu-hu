---
title: 'Oktatóanyag: Azure Active Directory integráció a Zoho One szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Zoho között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: baa33e79b3bba8eccb2d0fc03e110e1a3c4defde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546005"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Oktatóanyag: Azure Active Directory integráció a Zoho One-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zoho One-t Azure Active Directory (Azure AD) használatával.
A Zoho One és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Zohohoz.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Zoho One-ba (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Zoho-nal szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Zoho egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zoho One támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-zoho-one-from-the-gallery"></a>A Zoho hozzáadása a katalógusból

A Zoho egy Azure AD-integrációjának konfigurálásához hozzá kell adnia a Zoho One-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A következő lépések végrehajtásával adhat hozzá egy Zoho-t a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zoho One**kifejezést, válassza ki a **Zoho One** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A Zoho egyike az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Zoho-vel egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Zoho kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Zoho használatával történő konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[Zoho egyszeri bejelentkezés konfigurálása](#configure-zoho-one-single-sign-on)** – az alkalmazás oldalán az Sign-On beállításainak konfigurálása.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre a Zoho One test User](#create-zoho-one-test-user)** -to have a Britta Simon in Zoho, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Zoho használatával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Zoho One** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Zoho egy tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-relay.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet: `one.zoho.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Az előző **Válasz URL-címének** értéke nem valódi. A következőt kell megadnia `<saml-identifier>` : #step4 a **Zoho egyszeri bejelentkezés** szakasza, amelyet az oktatóanyag későbbi részében ismertetünk.

    c. Kattintson a **további URL-címek beállítása**elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet: `https://one.zoho.com`

5. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:


    ![Zoho egy tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Az előző **bejelentkezési URL-cím** értéke nem valódi. A rendszer a tényleges Sign-On URL-címmel frissíti az értéket a következőt: a **Zoho egyszeri bejelentkezés** szakasza, amelyet az oktatóanyag későbbi részében ismertetünk. 

6. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. A **Zoho One szakasz beállítása** lapon másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-zoho-one-single-sign-on"></a>A Zoho beállítása egyetlen Sign-On

1. Egy másik böngészőablakban jelentkezzen be a Zoho One vállalati webhelyre rendszergazdaként.

2. A **szervezet** lapon kattintson a **telepítés** SAML- **hitelesítés**alatt elemre.

    ![Zoho One org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Az előugró oldalon hajtsa végre a következő lépéseket:

    ![Zoho One SIG](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    b. A kijelentkezési **URL** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-cím**értékét, amelyet a Azure Portalból másolt.

    c. Kattintson a **Tallózás** gombra a Azure Portalról letöltött **tanúsítvány (Base64)** feltöltéséhez.

    d. Kattintson a **Mentés** gombra.

4. Miután mentette az SAML-hitelesítés telepítését, másolja az **SAML-azonosító** értéket, és fűzze hozzá a **Válasz URL-címéhez** a helyett `<saml-identifier>` , `https://accounts.zoho.com/samlresponse/one.zoho.com` és illessze be a generált értéket a **Válasz URL-címe** szövegmezőbe az **alapszintű SAML-konfiguráció** szakaszban.

    ![Zoho egy SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Lépjen a **tartományok** lapra, majd kattintson a **tartomány hozzáadása**lehetőségre.

    ![Zoho egy tartomány](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. A **tartomány hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Zoho egy tartomány hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. A **tartománynév** szövegmezőbe írja be a következőt: tartomány, például contoso.com.

    b. Kattintson a **Hozzáadás** parancsra.

    >[!Note]
    >A tartomány hozzáadása után kövesse az [alábbi](https://www.zoho.com/one/help/admin-guide/domain-verification.html) lépéseket a tartomány ellenőrzéséhez. A tartomány ellenőrzése után használja a tartománynevet a **bejelentkezési URL-cím** mezőben a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszában.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Zoho One-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Zoho One**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zoho One**elemet.

    ![Az alkalmazások listájában található Zoho One-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-zoho-one-test-user"></a>Zoho egy tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Zoho One-ba, a Zoho One-ba kell kiépíteni őket. A Zoho One-ban a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Zoho-ba egy biztonsági rendszergazdaként.

2. A **felhasználók** lapon kattintson a **felhasználói embléma**elemre.

    ![Zoho egy felhasználó](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. A **felhasználó hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Zoho One felhasználó hozzáadása](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. A **név** szövegmezőbe írja be a felhasználó nevét, például a **Britta Simon**nevet.
    
    b. Az **e-mail-cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail-címet brittasimon@contoso.com .

    >[!Note]
    >Válassza ki az ellenőrzött tartományt a tartomány listából.

    c. Kattintson a **Hozzáadás** parancsra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Zoho One csempére kattint, akkor automatikusan be kell jelentkeznie a Zoho-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

