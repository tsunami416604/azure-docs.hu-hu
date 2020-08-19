---
title: 'Oktatóanyag: Azure Active Directory-integráció a Kantega SSO-val a FishEye/tégelyek esetében | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Kantega SSO között a halszem/tégelyek számára.
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
ms.openlocfilehash: ce5572c4c5aca6f48496250c942d50b90551f36f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546927"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Oktatóanyag: Azure Active Directory integráció a Kantega SSO-val a FishEye/tégelyek esetében

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kantega SSO-t a halszem/tégelyekhez Azure Active Directory (Azure AD) használatával.
A Kantega SSO/tégelyek Azure AD-vel való integrálása az alábbi előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy a Kantega SSO-t vagy a tégelyt is hozzáférhessen.
* Engedélyezheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Kantega egyszeri bejelentkezésre a FishEye/tégely (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Kantega SSO-hoz szeretné konfigurálni a halszem/tégelyekhez, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Kantega SSO a halszem/tégely egyszeri bejelentkezésre engedélyezett előfizetéshez

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Kantega SSO a FishEye/tégelyek esetében támogatja az **SP és a identitásszolgáltató** által kezdeményezett SSO-t

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Kantega SSO hozzáadása a halszem/tégelyekhez a katalógusból

A Kantega SSO és a tégelyek Azure AD-ba való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Kantega SSO/tégelyek gyűjteményét.

**A következő lépések végrehajtásával adhat hozzá Kantega SSO-t a FishEye/tégelyekhez a katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **KANTEGA SSO a halszem/tégelyekhez**, válassza a **Kantega egyszeri bejelentkezés a halszem/tégelyekhez** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Kantega SSO az eredmények listájában a halszem/tégelyek esetében](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést kell konfigurálnia és tesztelni a Kantega SSO-val a FishEye/tégelyek esetében egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolati kapcsolatot kell létrehozni a Kantega SSO/tégelyek esetében.

Az Azure AD egyszeri bejelentkezés és a halszem/tégelyek Kantega egyszeri bejelentkezésének konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[KANTEGA SSO konfigurálása a halszem/tégelyes egyszeri bejelentkezéshez](#configure-kantega-sso-for-fisheyecrucible-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[KANTEGA SSO létrehozása a FISHEYE/tégely tesztelési felhasználóhoz](#create-kantega-sso-for-fisheyecrucible-test-user)** – ha a felhasználó Azure ad-Britta kapcsolódó, a Kantega SSO-hoz tartozó "
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

A következő lépések végrehajtásával konfigurálhatja az Azure AD egyszeri bejelentkezést a Kantega SSO-val a FishEye/tégelyek esetében:

1. A [Azure Portal](https://portal.azure.com/)a **Kantega SSO for FishEye/tégelyek** alkalmazás-integrációja lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Kantega egyszeri bejelentkezés a halszem/tégelyek tartományhoz és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Kantega egyszeri bejelentkezés a halszem/tégelyek tartományhoz és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezek az értékek a halszem/tégely beépülő modul konfigurációja során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **KANTEGA SSO beállítása a halszem/tégelyek számára** szakaszban másolja a megfelelő URL-címeket (ka) t a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-kantega-sso-for-fisheyecrucible-single-sign-on"></a>Kantega SSO konfigurálása a halszem/tégelyes egyszeri bejelentkezéshez

1. Egy másik böngészőablakban jelentkezzen be a FishEye/tégely a helyszíni kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **bővítmények**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. A rendszerbeállítások szakaszban kattintson az **új bővítmények keresése**elemre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Keressen **KANTEGA SSO-t a tégelyekhez** , majd kattintson a **telepítés** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Elindul a beépülő modul telepítése. 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. A telepítés befejezését követően. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Az új beépülő modul konfigurálásához kattintson a **Konfigurálás** elemre. 

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. Az **SAML** szakaszban. Válassza az **Azure Active Directory (Azure ad)** elemet az **identitás-szolgáltató hozzáadása** legördülő listából.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Válassza az előfizetési szint **alapszintű**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. Az **alkalmazás tulajdonságai** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Másolja az **alkalmazás-azonosító URI** -értékét, és használja **azonosítóként, válasz URL-címként és bejelentkezési URL-címként** a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    b. Kattintson a **Tovább** gombra.

1. A **Metaadatok importálása** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Válassza ki a **metaadatokat a számítógépen**, és töltse fel a metaadat-fájlt, amelyet a Azure Portalról töltött le.

    b. Kattintson a **Tovább** gombra.

1. A **név és az SSO hely** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Adja hozzá az Identitáskezelő nevét a **személyazonosság-szolgáltató neve** szövegmezőben (például Azure ad).

    b. Kattintson a **Tovább** gombra.

1. Ellenőrizze az aláíró tanúsítványt, és kattintson a **tovább**gombra.   

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. A **halszem felhasználói fiókok** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Ha szükséges, válassza a **felhasználók létrehozása a FishEye belső könyvtárában** lehetőséget, és írja be a csoport megfelelő nevét a felhasználók számára (több nem lehet. a csoportok vesszővel elválasztva).

    b. Kattintson a **Tovább** gombra.

1. Kattintson a **Befejezés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. Az **Azure ad ismert tartományai** szakaszban hajtsa végre a következő lépéseket:  

    ![Egyszeri bejelentkezés konfigurálása](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Az oldal bal oldali paneljén válassza az **ismert tartományok** elemet.

    b. Adja meg a tartomány nevét az **ismert tartományok** szövegmezőben.

    c. Kattintson a **Mentés** gombra.

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

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a Kantega egyszeri bejelentkezéshez a halszem/tégelyekhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Kantega egyszeri bejelentkezés a FishEye/tégelyhez**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Kantega egyszeri bejelentkezés a FishEye/tégelyhez**lehetőséget.

    ![Az alkalmazások listájában a halszem/tégelyek hivatkozásához tartozó Kantega SSO](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kantega-sso-for-fisheyecrucible-test-user"></a>Kantega SSO létrehozása a halszem/tégelyek tesztelésére szolgáló felhasználó számára

Annak engedélyezéséhez, hogy az Azure AD-felhasználók a FishEye/tégelybe jelentkezzenek be, a halszem/tégelybe kell azokat kiépíteni. A Kantega SSO/tégelyek esetében a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a tégelybe a helyszíni kiszolgálóra rendszergazdaként.

1. Mutasson a fogaskerékre, majd kattintson a **felhasználók**elemre.

    ![Alkalmazott hozzáadása](./media/kantegassoforfisheyecrucible-tutorial/user1.png)

1. A **felhasználók** lap szakaszban kattintson a **felhasználó hozzáadása**elemre.

    ![Alkalmazott hozzáadása](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. Az **új felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/kantegassoforfisheyecrucible-tutorial/user3.png)

    a. A **Felhasználónév** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    b. A **megjelenítendő név** szövegmezőbe írja be a felhasználó megjelenítendő nevét (például Britta Simon).

    c. Az **e-mail cím** szövegmezőbe írja be a felhasználóhoz hasonló e-mail címet Brittasimon@contoso.com .

    d. A **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. A **Jelszó megerősítése** szövegmezőbe írja be újra a felhasználó jelszavát.

    f. Kattintson a **Hozzáadás** parancsra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Kantega egyszeri bejelentkezés a halszem/tégelyek csempére kattint, automatikusan be kell jelentkeznie a Kantega SSO/tégelybe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)