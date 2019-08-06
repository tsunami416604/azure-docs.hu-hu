---
title: 'Oktatóanyag: Azure Active Directory-integráció SD-elemekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SD elemek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: a0272de238055151e73c13687f25745c3f402eb4
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826159"
---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>Oktatóanyag: Azure Active Directory SD-elemekkel való integráció

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat SD-elemeket Azure Active Directory (Azure AD) használatával.
Az SD-elemek Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Az SD-elemekhez hozzáférő Azure AD-ben is szabályozható.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SD-elemekre (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció SD-elemekkel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* SD-elemek egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SD-elemek támogatják a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sd-elements-from-the-gallery"></a>SD-elemek hozzáadása a katalógusból

Az SD-elemek Azure AD-integrációjának konfigurálásához SD-elemeket kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha SD-elemeket szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SD-elemek**elemet, válassza az **SD-elemek** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SD-elemek az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SD-elemekkel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az SD-elemek kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés SD-elemekkel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SD-elemek egyszeri bejelentkezésének konfigurálása](#configure-sd-elements-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[SD-elemek létrehozása – teszt felhasználó](#create-sd-elements-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó SD-elemekben található Britta Simon partnere.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés SD-elemekkel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portal](https://portal.azure.com/) **SD-elemek** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon hajtsa végre a következő lépéseket:

    ![SD-elemek tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot az [SD-elemek ügyfél-támogatási csapatával](mailto:support@sdelements.com) az értékek lekéréséhez. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az SD-elemek alkalmazása egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név) |  Forrás attribútum|
    | --- | --- |
    | email |user.mail |
    | FirstName |User. givenName |
    | LastName |felhasználó. vezetéknév |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az **SD-elemek beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sd-elements-single-sign-on"></a>SD-elemek egyszeri bejelentkezésének konfigurálása

1. Ha engedélyezni szeretné az egyszeri bejelentkezést, lépjen kapcsolatba az [SD-elemek támogatási csapatával](mailto:support@sdelements.com) , és adja meg a letöltött tanúsítványfájl-fájlt.

1. Egy másik böngészőablakban jelentkezzen be az SD-elemek bérlője rendszergazdaként.

1. A felső menüben kattintson a **rendszer**, majd az **egyszeri bejelentkezés**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Az **egyszeri bejelentkezés beállításai** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. **SSO**-típusként válassza az **SAML**lehetőséget.

    b. Az **Identity Provider entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    c. Az **identitás-szolgáltató egyszeri bejelentkezési szolgáltatás** szövegmezőben illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az SD-elemek elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **SD-elemek elemet**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SD-elemek elemet**.

    ![Az SD-elemek hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sd-elements-test-user"></a>SD-elemek létrehozása teszt felhasználó

A szakasz célja, hogy létrehozzon egy Britta Simon nevű felhasználót SD-elemekben. SD-elemek esetén az SD-elemek létrehozása a felhasználók számára manuális feladat.

**Ha SD-elemekben szeretné létrehozni a Simon Britta, hajtsa végre a következő lépéseket:**

1. A webböngésző ablakban jelentkezzen be az SD-elemek vállalati webhelyre rendszergazdaként.

1. A felső menüben kattintson a **felhasználói kezelés**, majd a **felhasználók**lehetőségre.

    ![SD-elemek tesztelési felhasználójának létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Kattintson az **új felhasználó hozzáadása**lehetőségre.

    ![SD-elemek tesztelési felhasználójának létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Az **új felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![SD-elemek tesztelési felhasználójának létrehozása](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Az **e-mail** szövegmezőbe írja be a következőhöz hasonló **brittasimon@contoso.com** felhasználó e-mail címét:.

    b. Az **Utónév** szövegmezőbe írja be a felhasználó utónevét, például a **Britta**nevet.

    c. A **vezetékneve** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    d. **Szerepkörként**válassza a **felhasználó**lehetőséget.

    e. Kattintson a **felhasználó létrehozása**gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SD-elemek csempére kattint, akkor automatikusan be kell jelentkeznie azon SD-elemekre, amelyekhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)