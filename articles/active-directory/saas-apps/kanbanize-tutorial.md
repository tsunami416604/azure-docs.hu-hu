---
title: 'Oktatóanyag: Azure Active Directory integráció a Kanbanize-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Kanbanize között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69103ea0e6088b4a823df34ebd982c67e2502cb3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879489"
---
# <a name="tutorial-integrate-kanbanize-with-azure-active-directory"></a>Oktatóanyag: A Kanbanize integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Kanbanize a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Kanbanize-t az Azure AD-vel, a következőket teheti:

* A Kanbanize-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Kanbanize az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Kanbanize egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Kanbanize támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Kanbanize **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-kanbanize-from-the-gallery"></a>Kanbanize hozzáadása a gyűjteményből

A Kanbanize Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Kanbanize a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Kanbanize** kifejezést a keresőmezőbe.
1. Válassza ki a **Kanbanize** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a Kanbanize a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Kanbanize-ben.

Az Azure AD SSO és a Kanbanize konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[KANBANIZE SSO konfigurálása](#configure-kanbanize-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre Kanbanize-teszt felhasználót](#create-kanbanize-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Kanbanize rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Kanbanize** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com/saml/acs`

    c. Kattintson a **további URL-címek beállítása**elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be az URL-címet:`/ctrl_login/saml_login`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a [Kanbanize](mailto:support@ms.kanbanize.com) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Kanbanize alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a NameIdentifier a **User. userPrincipalName**leképezéssel van leképezve. A Kanbanize alkalmazás a NameIdentifier a **User. mail**használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a Szerkesztés ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **Kanbanize beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-kanbanize-sso"></a>Kanbanize SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Kanbanize biztonsági rendszergazdaként.

2. Kattintson a lap jobb felső sarkában található **Beállítások** emblémára.

    ![Kanbanize-beállítások](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

3. A menü bal oldalán található adminisztráció panel oldalon kattintson az integrációk elemre, majd engedélyezze **az egyszeri bejelentkezést**.

    ![Kanbanize-integrációk](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

4. Az integrációk szakaszban kattintson a **Konfigurálás** elemre az **egyszeri bejelentkezés integrációs** oldalának megnyitásához.

    ![Kanbanize-konfiguráció](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

5. Az **egyszeri bejelentkezés integrációs** lapján a **konfigurációk**területen hajtsa végre a következő lépéseket:

    ![Kanbanize-integrációk](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Az **identitásszolgáltató entitás-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portal másolt.

    b. A **identitásszolgáltató bejelentkezési végpont** szövegmezőben illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portal másolt.

    c. A **identitásszolgáltató kijelentkezési végpont** szövegmezőbe illessze be a kijelentkezési **URL-cím**értékét, amelyet a Azure Portal másolt.

    d. Adja meg ezt az értéket az E-mail szövegmezőben az **attribútum neve** mezőben.`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Az utónév szövegmezőbe írja be ezt az értéket az **attribútum neve** szövegmezőbe.`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Az **Utónév** szövegmezőbe írja be ezt az értéket az attribútum nevében.`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Ezeket az értékeket úgy érheti el, hogy a megfelelő attribútum névterét és nevét a Azure Portal felhasználói attribútumok szakaszában egyesíti.

    g. Nyissa meg a Jegyzettömbben a Azure Portal letöltött Base-64 kódolású tanúsítványt, másolja a tartalmát (a kezdő és záró jelölő nélkül), majd illessze be a **identitásszolgáltató X. 509 tanúsítvány** mezőbe.

    h. Győződjön meg arról, **hogy az egyszeri bejelentkezés és az Kanbanize is engedélyezve van**

    i. Kattintson a **Beállítások mentése**gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Kanbanize.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Kanbanize**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-kanbanize-test-user"></a>Kanbanize-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Kanbanize-ben. A Kanbanize támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Kanbanize-ben, a rendszer egy újat hoz létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Kanbanize ügyfélszolgálati csapatához](mailto:support@ms.kanbanize.com).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Kanbanize csempére kattint, automatikusan be kell jelentkeznie arra a Kanbanize, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

