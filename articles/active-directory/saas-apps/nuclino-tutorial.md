---
title: 'Oktatóanyag: Azure Active Directory integráció a Nuclino-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Nuclino között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: cce4857dd270f6868b8ae4d0de2117e46c20ce7c
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826300"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>Oktatóanyag: Azure Active Directory integráció a Nuclino

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Nuclino a Azure Active Directory (Azure AD) szolgáltatással.
A Nuclino és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Nuclino.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Nuclino (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Nuclino való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Nuclino egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Nuclino támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

* A Nuclino **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-nuclino-from-the-gallery"></a>Nuclino hozzáadása a gyűjteményből

A Nuclino Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Nuclino a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Nuclino szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Nuclino**kifejezést, válassza a **Nuclino** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Nuclino az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Nuclino-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Nuclino kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Nuclino való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Nuclino egyszeri bejelentkezés konfigurálása](#configure-nuclino-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Nuclino-teszt felhasználót](#create-nuclino-test-user)** – hogy a Nuclino Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Nuclino való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Nuclino** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Nuclino tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a **hitelesítés** szakasz tényleges azonosító és válasz URL-címével, amelyet az oktatóanyag későbbi részében ismertet.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Nuclino tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [Nuclino](mailto:contact@nuclino.com) ügyfélszolgálatával. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A Nuclino alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. A fentieken kívül a Nuclino alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név) |  Forrás attribútum|
    | ---------------| --------- |
    | first_name | User. givenName |
    | last_name | felhasználó. vezetéknév |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. A **Nuclino beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-nuclino-single-sign-on"></a>Nuclino egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Nuclino vállalati webhelyre rendszergazdaként.

2. Kattintson az **ikonra**.

    ![Nuclino-konfiguráció](./media/nuclino-tutorial/configure1.png)

3. Kattintson az **Azure ad SSO** elemre, és válassza a **csoport beállításai** lehetőséget a legördülő menüből.

    ![Nuclino-konfiguráció](./media/nuclino-tutorial/configure2.png)

4. Válassza a bal oldali navigációs ablaktábla **hitelesítés** elemét.

    ![Nuclino-konfiguráció](./media/nuclino-tutorial/configure3.png)

5. A **hitelesítés** szakaszban hajtsa végre a következő lépéseket:

    ![Nuclino-konfiguráció](./media/nuclino-tutorial/configure4.png)

    a. Válassza ki az **SAML-alapú egyszeri bejelentkezést (SSO)** .

    b. Másolja az **ACS URL-címet (másolja és illessze be az SSO-szolgáltatóba)** , és illessze be a Azure Portal alapszintű **SAML-konfigurációs** szakaszának **Válasz URL-** szövegmezőbe.

    c. Másolja ki az **entitás azonosítóját (másolja és illessze be az SSO-szolgáltatóba)** , majd illessze be a Azure Portal alapszintű **SAML-konfigurációs** szakaszának **azonosító** szövegmezőbe.

    d. Az **egyszeri bejelentkezés URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    e. Az **entitás-azonosító** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    f. Nyissa meg a letöltött **tanúsítvány (Base64)** fájlt a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be a **nyilvános tanúsítvány** szövegmezőbe.

    g. Kattintson a **módosítások mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Nuclino hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Nuclino**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Nuclino**lehetőséget.

    ![Az Nuclino hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-nuclino-test-user"></a>Nuclino-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Nuclino-ben. A Nuclino támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Nuclino-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Nuclino támogatási csapatával](mailto:contact@nuclino.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Nuclino csempére kattint, automatikusan be kell jelentkeznie arra a Nuclino, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

