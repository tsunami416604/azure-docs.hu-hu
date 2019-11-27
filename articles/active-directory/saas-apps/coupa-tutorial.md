---
title: 'Oktatóanyag: Azure Active Directory-integráció a Coupa szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Coupa között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709f3a5b66db660ade482660cd9b3930ff0b7141
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227650"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Oktatóanyag: Azure Active Directory a Coupa-integrációval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Coupa Azure Active Directory (Azure AD) szolgáltatással.
A Coupa az Azure AD-vel való integrálása a következő előnyöket biztosítja:

* Megadhatja az Azure AD-t, aki hozzáfér a Coupa szolgáltatáshoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Coupa (egyszeri bejelentkezés) szolgáltatásba az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Coupa konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* Coupa egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A coupa támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-coupa-from-the-gallery"></a>A Coupa hozzáadása a katalógusból

A Coupa Azure AD-integrációjának konfigurálásához hozzá kell adnia a Coupa elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Coupa a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **coupa**kifejezést, válassza a **coupa** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![A coupa az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Coupa szolgáltatással egy **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Coupa-beli kapcsolódó felhasználó közötti kapcsolati kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a Coupa szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[coupa egyszeri bejelentkezésének konfigurálása](#configure-coupa-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Coupa-teszt felhasználó létrehozása](#create-coupa-test-user)** – ha a coupa-ben a felhasználó Azure ad-képviseletéhez kapcsolódó Britta Simon partnere van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Coupe használatával szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **Coupe** Application Integration oldalon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![A coupa tartomány és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [coupa ügyfél-támogatási csapatához](https://success.coupa.com/Support/Contact_Us?) .

    b. Az **azonosító** mezőbe írjon be egy URL-címet:

    | Környezet  | URL |
    |:-------------|----|
    | Védőfal | `sso-stg1.coupahost.com`|
    | Éles üzem | `sso-prd1.coupahost.com`|
    | | |

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:

    | Környezet | URL |
    |------------- |----|
    | Védőfal | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Éles üzem | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A **coupa beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-coupa-single-sign-on"></a>A Coupa egyszeri bejelentkezésének konfigurálása

1. Jelentkezzen be a Coupa vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **telepítő \> biztonsági vezérlőt**.

    ![Biztonsági vezérlők](./media/coupa-tutorial/ic791900.png "Biztonsági vezérlők")

3. A **Bejelentkezés a coupa hitelesítő adatokkal** szakaszban hajtsa végre a következő lépéseket:

    ![Coupa SP-metaadatok](./media/coupa-tutorial/ic791901.png "Coupa SP-metaadatok")

    a. Válassza **a bejelentkezés az SAML használatával**lehetőséget.

    b. Kattintson a **Tallózás** gombra a Azure Portal letöltött metaadatok feltöltéséhez.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon\@yourcompanydomain. Extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Coupa elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **coupa**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **coupa**elemet.

    ![A Coupa hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-coupa-test-user"></a>Coupa-teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Coupa-be, a Coupa-be kell építeni.  

* A Coupa esetében a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **coupa** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **telepítés**elemre, majd kattintson a **felhasználók**elemre.

    ![Felhasználók](./media/coupa-tutorial/ic791908.png "Felhasználók")

3. Kattintson a **Létrehozás** elemre.

    ![Felhasználók létrehozása](./media/coupa-tutorial/ic791909.png "Felhasználók létrehozása")

4. A **felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Felhasználó adatai](./media/coupa-tutorial/ic791910.png "Felhasználó adatai")

    a. Írja be a **bejelentkezési nevet**, az **Utónév**, a **vezetéknév**, az **egyszeri bejelentkezési azonosító**, a kapcsolódó szövegmezőbe felvenni kívánt érvényes Azure Active Directory fiók **e-mail** attribútumait.

    b. Kattintson a **Létrehozás** elemre.

    >[!NOTE]
    >A Azure Active Directory fiók tulajdonosa egy e-mailt fog kapni, amely egy hivatkozással megerősíti a fiókot, mielőtt az aktívvá válna.
    >

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez a Coupa által biztosított bármely egyéb felhasználói fiók létrehozási eszközét vagy API-t használhatja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a Coupa csempére kattint, automatikusan be kell jelentkeznie arra a Puccsba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

