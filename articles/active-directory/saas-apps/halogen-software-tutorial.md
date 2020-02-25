---
title: 'Oktatóanyag: Azure Active Directory-integráció Saba TalentSpace | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Saba TalentSpace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561338"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Saba TalentSpace

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Saba TalentSpace-t Azure Active Directory (Azure AD) használatával. Ha a Saba TalentSpace-t az Azure AD-vel integrálja, a következőket teheti:

* A Saba TalentSpace hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Saba TalentSpace az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Saba TalentSpace egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Saba TalentSpace támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Saba TalentSpace konfigurálása után kényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak kiszűrése és beszivárgását valós időben teszi elérhetővé. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Saba TalentSpace hozzáadása a gyűjteményből

A Saba TalentSpace az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Saba TalentSpace a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a " **Saba TalentSpace** " kifejezést a keresőmezőbe.
1. Válassza ki a **Saba TalentSpace** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Saba TalentSpace

Konfigurálja és tesztelje az Azure AD SSO-t a Saba TalentSpace egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Saba TalentSpace-ben.

Az Azure AD SSO a Saba TalentSpace való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Saba TALENTSPACE SSO konfigurálása](#configure-saba-talentspace-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Saba TalentSpace-teszt felhasználót](#create-saba-talentspace-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli párja van a Saba TalentSpace-ben.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Saba TalentSpace** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. A **Válasz URL-címe (a fogyasztói szolgáltatás URL-címe)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez lépjen kapcsolatba a [Saba TalentSpace-ügyfél támogatási csapatával](https://support.saba.com/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **Saba TalentSpace beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Saba TalentSpace-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza ki a **Saba TalentSpace**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **Saba TalentSpace** alkalmazásba rendszergazdaként.

2. Kattintson a **Beállítások** fülre.
  
    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. A bal oldali navigációs ablaktáblán kattintson az **SAML-konfiguráció**elemre.
  
    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Az **SAML-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. **Egyedi azonosítóként**válassza a **NameID**lehetőséget.

    b. Az **egyedi azonosító leképezése a**következőre: **Felhasználónév**.
  
    c. A letöltött metaadat-fájl feltöltéséhez kattintson a **Tallózás** gombra a fájl kiválasztásához, majd **töltse fel a fájlt**.

    d. A konfiguráció teszteléséhez kattintson a **teszt futtatása**gombra.

    > [!NOTE]
    > A következő üzenetre kell várnia: "*az SAML-teszt befejeződött. Zárja be ezt az ablakot*". Ezután zárjuk be a megnyitott böngészőablakot. Az **SAML engedélyezése** jelölőnégyzet csak akkor engedélyezett, ha a teszt befejeződött.

    e. Válassza az **SAML engedélyezése**lehetőséget.

    f. Kattintson a **módosítások mentése**gombra.

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace-teszt felhasználó létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a Saba TalentSpace-ben.

**Ha Britta Simon nevű felhasználót szeretne létrehozni a Saba TalentSpace-ben, hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Saba TalentSpace** -alkalmazásba rendszergazdaként.

2. Kattintson a **felhasználói központ** fülre, majd a **felhasználó létrehozása**elemre.

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Az **új felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Mi az az Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" nevű felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

    c. A Felhasználónév szövegmezőbe írja be a **B. Simon** **nevet** , a felhasználónevet pedig a Azure Portal.

    d. A **jelszó** szövegmezőbe írja be a következőt: B. Simon.

    e. Kattintson a **Save** (Mentés) gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a Saba TalentSpace csempére kattint, automatikusan be kell jelentkeznie a Saba TalentSpace, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Saba TalentSpace kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)