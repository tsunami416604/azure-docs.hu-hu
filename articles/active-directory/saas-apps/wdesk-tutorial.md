---
title: 'Oktatóanyag: Azure Active Directory integráció a Wdesk-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Wdesk között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 9e64d76e29ba98181aac12e1e3167351a9c1aa95
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Wdesk

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Wdesk a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Wdesk-t az Azure AD-vel, a következőket teheti:

* A Wdesk-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Wdesk az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Wdesk egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Wdesk támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO
* A Wdesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Wdesk hozzáadása a gyűjteményből

A Wdesk Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Wdesk a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Wdesk** kifejezést a keresőmezőbe.
1. Válassza ki a **Wdesk** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Wdesk-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Wdesk kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD SSO és a Wdesk konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[WDESK SSO konfigurálása](#configure-wdesk-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre Wdesk-teszt felhasználót](#create-wdesk-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Wdesk rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Wdesk való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Wdesk** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![Wdesk tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![Wdesk tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Ezeket az értékeket a WDesk-portálról szerezheti be az egyszeri bejelentkezés konfigurálásakor.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Wdesk beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

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
  
    b. A Felhasználónév mezőbe írja be a **nevet** brittasimon@yourcompanydomain.extension . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Wdesk hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Wdesk**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Wdesk**lehetőséget.

    ![Az Wdesk hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-wdesk-sso"></a>Wdesk SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Wdesk biztonsági rendszergazdaként.

2. A bal alsó sarokban kattintson a **rendszergazda** elemre, és válassza a **fiók rendszergazdája**elemet:
 
     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. A Wdesk-rendszergazda területen navigáljon a **Biztonság**, majd az **SAML**  >  **SAML-beállítások**elemre:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Az **SAML felhasználói azonosító beállításai**területen győződjön meg arról, **hogy az SAML felhasználói azonosító Wdesk Felhasználónév**.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/wdesk-username.png)

4. Az **általános beállítások**területen jelölje be az **SAML egyszeri bejelentkezés engedélyezése**:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. A **szolgáltató részletei**területen hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Másolja a **bejelentkezési URL-címet** , és illessze be a **bejelentkezési url** szövegmezőbe Azure Portal.
   
      b. Másolja a **metaadatok URL-címét** , és illessze be az **azonosító** szövegmezőbe Azure Portal.
       
      c. Másolja a **felhasználói URL-címet** , és illessze be a **Válasz URL-** szövegmezőbe Azure Portal.
   
      d. A módosítások mentéséhez kattintson a Azure Portal **Mentés** gombra.      

6. A **identitásszolgáltató beállításainak konfigurálása** elemre kattintva nyissa meg a **identitásszolgáltató-beállítások szerkesztése** párbeszédpanelt. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portalból mentett **Metadata.xml** fájl megkereséséhez, majd töltse fel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Kattintson a **módosítások mentése**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesk-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a Wdesk, a Wdesk kell kiépíteni őket. A Wdesk-ben a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a Wdesk biztonsági rendszergazdaként.

2. Navigáljon a **rendszergazdai**  >  **fiók rendszergazdájához**.

     ![Egyszeri bejelentkezés konfigurálása](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kattintson **Members** a tagok **elemre**.

4. Most kattintson a **tag hozzáadása** elemre a **tag hozzáadása** párbeszédpanel megnyitásához. 
   
    ![Azure AD-tesztkörnyezet létrehozása](./media/wdesk-tutorial/createuser1.png)  

5. A **felhasználó** szövegmezőben adja meg a felhasználóhoz hasonló felhasználónevet, b.simon@contoso.com majd kattintson a **Folytatás** gombra.

    ![Azure AD-tesztkörnyezet létrehozása](./media/wdesk-tutorial/createuser3.png)

6.  Adja meg a részleteket az alábbi ábrán látható módon:
  
    ![Azure AD-tesztkörnyezet létrehozása](./media/wdesk-tutorial/createuser4.png)
 
    a. Az **e-mail** szövege mezőben adja meg a felhasználóhoz hasonló e-mail címet b.simon@contoso.com .

    b. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például **Simon**nevet.

7. Kattintson a **tag mentése** gombra.  

    ![Azure AD-tesztkörnyezet létrehozása](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Wdesk csempére kattint, automatikusan be kell jelentkeznie arra a Wdesk, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)