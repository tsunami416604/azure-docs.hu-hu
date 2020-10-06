---
title: 'Oktatóanyag: Azure Active Directory integráció a AirWatch-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AirWatch között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 2baff2ac2af10caf857fd08b0d7e7df5030220e4
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758141"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Oktatóanyag: a AirWatch és a Azure Active Directory integrálása

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AirWatch a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AirWatch-t az Azure AD-vel, a következőket teheti:

* A AirWatch-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a AirWatch az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* AirWatch egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A AirWatch támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch hozzáadása a gyűjteményből

A AirWatch Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a AirWatch a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **AirWatch** kifejezést a keresőmezőbe.
1. Válassza ki a **AirWatch** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a AirWatch a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a AirWatch-ben.

Az Azure AD SSO és a AirWatch konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[AIRWATCH SSO konfigurálása](#configure-airwatch-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Hozzon létre AirWatch-teszt felhasználót](#create-airwatch-test-user)** – hogy a AirWatch Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
5. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **AirWatch** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Az **azonosító (Entity ID)** szövegmezőbe írja be az értéket a következőként: `AirWatch`

    > [!NOTE]
    > Ez az érték nem az igazi. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [AirWatch](https://www.air-watch.com/company/contact-us/) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A AirWatch alkalmazás megadott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név) |  Forrás attribútum|
    |---------------|----------------|
    | UID | User. userPrincipalName |
    | | |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    : Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a metaadatok XML-kódjának letöltéséhez, majd mentse azt a számítógépre.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **AirWatch beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>AirWatch SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a AirWatch vállalati webhelyre rendszergazdaként.

1. A beállítások lapon. Válassza a **beállítások > Vállalati integráció > Directory Services**elemet.

   ![Beállítások](./media/airwatch-tutorial/ic791921.png "Beállítások")

1. Kattintson a **felhasználó** lapra, az **Alap DN** szövegmezőbe írja be a tartomány nevét, majd kattintson a **Mentés**gombra.

   ![Képernyőfelvétel: az alapszintű DN szövegmező kiemelése.](./media/airwatch-tutorial/ic791922.png "Felhasználó")

1. Kattintson a **kiszolgáló** fülre.

   ![Kiszolgáló](./media/airwatch-tutorial/ic791923.png "Kiszolgáló")

1. Hajtsa végre a következő lépéseket az **LDAP** szakaszban:

    ![Képernyőkép, amely az LDAP szakaszban szükséges módosításokat mutatja.](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. A **könyvtár típusa**mezőben válassza a **nincs**lehetőséget.

    b. Válassza **az SAML használata hitelesítéshez**lehetőséget.

1. A **SAML 2,0** szakaszban a letöltött tanúsítvány feltöltéséhez kattintson a **feltöltés**gombra.

    ![Feltöltés](./media/airwatch-tutorial/ic791932.png "Feltöltés")

1. A **kérelem** szakaszban hajtsa végre a következő lépéseket:

    ![Kérés](./media/airwatch-tutorial/ic791925.png "Kérés")  

    a. **Kérelem kötésének típusaként**válassza a **post**lehetőséget.

    b. A Azure Portal az **egyszeri bejelentkezés konfigurálása a AirWatch** párbeszédpanelen másolja a **bejelentkezési URL-címet** , majd illessze be az **identitás-szolgáltató egyszeri bejelentkezési URL-címe** szövegmezőbe.

    c. **NameID formátumban**válassza az **e-mail cím**elemet.

    d. A **hitelesítési kérések biztonsága**lehetőségnél válassza a **nincs**lehetőséget.

    e. Kattintson a **Mentés** gombra.

1. Kattintson ismét a **felhasználó** lapra.

    ![Felhasználó](./media/airwatch-tutorial/ic791926.png "Felhasználó")

1. Az **attribútum** szakaszban hajtsa végre a következő lépéseket:

    ![Attribútum](./media/airwatch-tutorial/ic791927.png "Attribútum")

    a. Az **objektumazonosító** szövegmezőbe írja be a következőt: `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. A **Felhasználónév** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. A **megjelenítendő név** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. A **Utónév** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. A **vezetéknév** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Az **e-mail** szövegmezőbe írja be a következőt: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    : Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a AirWatch.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **AirWatch**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-airwatch-test-user"></a>AirWatch-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a AirWatch, a AirWatch kell kiépíteni őket. AirWatch esetén a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **AirWatch** vállalati webhelyre rendszergazdaként.

2. A bal oldali navigációs ablaktáblán kattintson a **fiókok**elemre, majd kattintson a **felhasználók**elemre.
  
   ![Felhasználók](./media/airwatch-tutorial/ic791929.png "Felhasználók")

3. A **felhasználók** menüben kattintson a **listanézet**lehetőségre, majd kattintson a **Hozzáadás gombra > felhasználó hozzáadása**lehetőségre.
  
   ![Képernyőkép, amely kiemeli a felhasználók hozzáadása és hozzáadása gombokat.](./media/airwatch-tutorial/ic791930.png "Felhasználó hozzáadása")

4. A **felhasználó hozzáadása/szerkesztése** párbeszédpanelen hajtsa végre a következő lépéseket:

   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791931.png "Felhasználó hozzáadása")

   a. Írja be a **felhasználónevet**, a **jelszót**, a **Jelszó megerősítése**, az **utónév**, a **vezetéknév**, az érvényes Azure Active Directory fiók **e-mail-címét** a kapcsolódó szövegmezőbe.

   b. Kattintson a **Mentés** gombra.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a AirWatch által biztosított AirWatch felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a AirWatch csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a AirWatch, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
