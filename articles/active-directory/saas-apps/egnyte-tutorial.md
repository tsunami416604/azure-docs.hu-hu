---
title: 'Oktatóanyag: Azure Active Directory integráció a Egnyte-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Egnyte között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 512b96b6b785c0694bb41fab657f0a45c4321f10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544332"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Egnyte

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Egnyte a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Egnyte-t az Azure AD-vel, a következőket teheti:

* A Egnyte-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Egnyte az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egnyte egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Egnyte támogatja az **SP** által KEZDEMÉNYEZett SSO-t
* A Egnyte konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte hozzáadása a gyűjteményből

A Egnyte Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Egnyte a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Egnyte** kifejezést a keresőmezőbe.
1. Válassza ki a **Egnyte** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Egnyte-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Egnyte kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Egnyte való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

Az Azure AD SSO és a Egnyte konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[EGNYTE SSO konfigurálása](#configure-egnyte-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Egnyte-teszt felhasználót](#create-egnyte-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Egnyte rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Egnyte való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Egnyte** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Egnyte tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a Egnyte ügyfélszolgálati [csapatához](https://www.egnyte.com/corp/contact_egnyte.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

5. A **Egnyte beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

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

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Egnyte hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Egnyte**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Egnyte**lehetőséget.

    ![Az Egnyte hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-egnyte-sso"></a>Egnyte SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Egnyte vállalati webhelyre rendszergazdaként.

2. Kattintson a **Beállítások** elemre.
   
    ![Beállítások](./media/egnyte-tutorial/ic787819.png "Beállítások")

3. A menüben kattintson a **Beállítások**elemre.

    ![Beállítások](./media/egnyte-tutorial/ic787820.png "Beállítások")

4. Kattintson a **konfiguráció** lapra, majd a **Biztonság**elemre.

    ![Biztonság](./media/egnyte-tutorial/ic787821.png "Biztonság")

5. Az **egyszeri bejelentkezéses hitelesítés** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezéses hitelesítés](./media/egnyte-tutorial/ic787822.png "Egyszeri bejelentkezéses hitelesítés")   
    
    a. **Egyszeri bejelentkezéses hitelesítésként**válassza az **SAML 2,0**lehetőséget.
   
    b. **Identitás-szolgáltatóként**válassza a **AzureAD**lehetőséget.
   
    c. Illessze be Azure Portalba másolt **bejelentkezési URL-címet** az **Identity Provider bejelentkezési URL-címe** szövegmezőbe.
   
    d. Illessze be a Azure Portalból másolt **Azure ad-azonosítót** az **Identity Provider entitás-azonosító** szövegmezőbe.
      
    e. Nyissa meg az alap-64 kódolású tanúsítványt a Jegyzettömbben, Azure Portal letöltve, másolja ki a tartalmat a vágólapra, majd illessze be az **Identity Provider tanúsítvány** szövegmezőbe.
   
    f. **Alapértelmezett felhasználó-hozzárendelésként**válassza az **e-mail-cím**elemet.
   
    : A **tartományhoz tartozó kibocsátó értékének használatakor**válassza a **Letiltva**lehetőséget.
   
    h. Kattintson a **Mentés** gombra.

### <a name="create-egnyte-test-user"></a>Egnyte-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Egnyte, a Egnyte kell kiépíteni őket. Egnyte esetén a kiépítés manuális feladat.

**Felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Egnyte** vállalati webhelyre rendszergazdaként.

2. Lépjen a **Beállítások \> felhasználók & csoportok menüpontra**.

3. Kattintson az **új felhasználó hozzáadása**elemre, majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
    ![Felhasználók](./media/egnyte-tutorial/ic787824.png "Felhasználók")

4. Az **új Kiemelt felhasználó** szakaszban hajtsa végre a következő lépéseket:
    
    ![Új általános jogú felhasználó](./media/egnyte-tutorial/ic787825.png "Új általános jogú felhasználó")   

    a. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **Brittasimon \@ contoso.com**-e-mail-címét.

    b. A **Felhasználónév** szövegmezőbe írja be a **Brittasimon**, például a következőt: Felhasználónév.

    c. Válassza az **egyszeri bejelentkezés** **hitelesítési típusként**lehetőséget.
   
    d. Kattintson a **Mentés** gombra.
    
    >[!NOTE]
    >A Azure Active Directory fiók tulajdonosának értesítési e-mailt fog kapni.
    >

>[!NOTE]
>Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Egnyte által biztosított Egnyte felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.
>

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Egnyte csempére kattint, automatikusan be kell jelentkeznie arra a Egnyte, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
