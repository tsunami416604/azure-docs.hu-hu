---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció munkanapokkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és munkanap között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f39b6f58b250d68a3b2ce962f158c7df36d812
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046603"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció munkanapokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a munkanapokat Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a munkanapokat, a következőket teheti:

* A munkanapokhoz hozzáféréssel rendelkező Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a munkanapokra az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A munkaidő egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A munkanap támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

* A munkanap konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Munkanapok hozzáadása a katalógusból

A munkanapoknak az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a munkaterületet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **munkanap** kifejezést a keresőmezőbe.
1. Válassza ki a **munkanapokat** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése munkanapokon

Konfigurálja és tesztelje az Azure AD SSO-t munkanapokon egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között munkanapokon.

Az Azure AD SSO munkanapokkal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[munkanap beállítása](#configure-workday)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre egy munkanap-tesztelési felhasználót](#create-workday-test-user)** , hogy a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli munkatársaival rendelkezzen.
3. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **munkanap** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `http://www.workday.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Ezek az értékek nem valódiak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. A válasz URL-címének rendelkeznie kell egy altartománnyal, például: www, WD2, wd3, wd3-Impl, wd5, wd5-Impl).
    > Például `http://www.myworkday.com` működik, de a `http://myworkday.com` nem. Az értékek lekéréséhez forduljon a [munkanapokhoz tartozó ügyfél-támogatási csapathoz](https://www.workday.com/en-us/partners-services/services/support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A munkanap alkalmazása egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A munkanap-alkalmazás azt várja, hogy a **NameIdentifier** a **User. mail**, **UPN**stb. használatával legyen leképezve, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy rákattint a **Szerkesztés** ikonra, és megváltoztatja az attribútum-hozzárendelést.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Itt a name ID-t az UPN (User. userPrincipalName) értékkel rendelte hozzá alapértelmezettként. Az egyszeri bejelentkezés sikeres működéséhez le kell képeznie a tényleges felhasználói azonosító nevét (e-mail-címe, UPN stb.).

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Ha módosítani szeretné az **aláírási** beállításokat a követelmény szerint, kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Válassza az **SAML-válasz aláírása és** az **aláírási beállítás**lehetőséget.

    b. Kattintson a **Mentés** gombra.

1. A **munkanap beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a munkanapokhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **munkanap**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-workday"></a>Munkanapok konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a munkanap vállalati webhelyre rendszergazdaként.

2. A keresőmezőbe keresse meg a Kezdőlap bal felső részén található **bérlő beállítása – biztonság** **jelölőnégyzetet** .

    ![Bérlői biztonság szerkesztése](./media/workday-tutorial/IC782925.png "Bérlői biztonság szerkesztése")

3. Az **átirányítási URL-címek** szakaszban hajtsa végre a következő lépéseket:

    ![Átirányítási URL-címek](./media/workday-tutorial/IC7829581.png "Átirányítási URL-címek")

    a. Kattintson a **sor hozzáadása**gombra.

    b. A **bejelentkezési átirányítási URL-cím**, az **időtúllépés átirányítása URL** -cím és a **mobil átirányítási URL-cím** szövegmezőbe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portal munkaterületének **beállítása** szakaszában másolt

    c. A **kijelentkezési átirányítás URL-címe** szövegmezőbe illessze be azt a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal munkaterületének **beállítása** szakaszában másolt.

    d. A **környezetekhez használt** szövegmezőben válassza ki a környezet nevét.  

   > [!NOTE]
   > A környezeti attribútum értéke a bérlői URL-cím értékéhez van kötve:  
   > – Ha a munkanap bérlői URL-címének tartományneve a Impl-val kezdődik, például: *https://www.myworkday.com/"bérlő"/login-saml2.htmld*), a **környezeti** attribútumot a megvalósítás értékre kell beállítani.  
   > – Ha a tartománynév egy másikkal kezdődik, kapcsolatba kell lépnie a [munkanap ügyfél-támogatási csapatával](https://www.workday.com/en-us/partners-services/services/support.html) , hogy a megfelelő **környezeti** értéket kapja.

4. Az **SAML beállítása** szakaszban hajtsa végre a következő lépéseket:

    ![SAML-telepítés](./media/workday-tutorial/IC782926.png "SAML-telepítés")

    a.  Válassza az **SAML-hitelesítés engedélyezése**lehetőséget.

    b.  Kattintson a **sor hozzáadása**gombra.

5. Az **SAML Identity Providers** szakaszban hajtsa végre a következő lépéseket:

    ![SAML-identitás szolgáltatók](./media/workday-tutorial/IC7829271.png "SAML-identitás szolgáltatók")

    a. Az **identitás-szolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO*).

    b. A Azure Portal a **munkanap beállítása** szakaszban másolja az **Azure ad-azonosító** értékét, majd illessze be a **kiállító** szövegmezőbe.

    ![SAML-identitás szolgáltatók](./media/workday-tutorial/IC7829272.png "SAML-identitás szolgáltatók")

    c. A Azure Portal a **munkanap beállítása** szakaszban másolja ki a **KIJELENTKEZÉSI URL-címet** , majd illessze be a **kijelentkezési válasz URL-címe** szövegmezőbe.

    d. A Azure Portal a **munkanap beállítása** szakaszban másolja be a **bejelentkezési URL-címet** , majd ILLESSZE be a **identitásszolgáltató SSO-szolgáltatás URL-címének** szövegmezőbe.

    e. A **környezetekhez használt** szövegmezőben válassza ki a környezet nevét.

    f. Kattintson az **Identity Provider nyilvánoskulcs-tanúsítványa**elemre, majd a **Létrehozás**gombra.

    ![Létrehozás](./media/workday-tutorial/IC782928.png "Létrehozás")

    g. Kattintson a **X509 nyilvános kulcs létrehozása**lehetőségre.

    ![Létrehozás](./media/workday-tutorial/IC782929.png "Létrehozás")

6. A **X509 nyilvános kulcsának megtekintése** szakaszban hajtsa végre a következő lépéseket:

    ![X509 nyilvános kulcsának megtekintése](./media/workday-tutorial/IC782930.png "X509 nyilvános kulcsának megtekintése")

    a. A **név** szövegmezőbe írja be a tanúsítvány nevét (például: *PPE\_SP*).

    b. Az **érvényes feladó** szövegmezőbe írja be a tanúsítvány érvényes értékének értékét.

    c.  Az **érvényes** a szövegmezőbe írja be a tanúsítvány érvényes attribútum értékét.

    > [!NOTE]
    > A letöltött tanúsítvány érvényességének dátumát és érvényességi idejét úgy érheti el, ha duplán rákattint.  A dátumok a **részletek** lapon jelennek meg.
    >
    >

    d.  Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, és másolja a tartalmát.

    e.  A **tanúsítvány** szövegmezőbe illessze be a vágólap tartalmát.

    f.  Kattintson az **OK** gombra.

7. Hajtsa végre a következő lépéseket:

    ![SSO-konfiguráció](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguráció")

    a.  A **szolgáltató azonosítója** szövegmezőbe írja be a következőt: **http://www.workday.com** .

    b. Válassza a ne állítsa le az **SP által kezdeményezett hitelesítési kérelmet**.

    c. A **hitelesítési kérelem aláírása módszerként**válassza a **sha256**lehetőséget.

    ![Hitelesítési kérelem aláírása módszer](./media/workday-tutorial/WorkdaySSOConfiguration.png "Hitelesítési kérelem aláírása módszer")

    d. Kattintson az **OK** gombra.

    ![oké](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Győződjön meg arról, hogy helyesen állította be az egyszeri bejelentkezést. Ha az egyszeri bejelentkezést nem megfelelő beállítással engedélyezi, előfordulhat, hogy nem tudja beírni az alkalmazást a hitelesítő adataival, és kizárja a zárolást. Ebben az esetben a munkanap olyan biztonsági mentési bejelentkezési URL-címet biztosít, ahol a felhasználók a szokásos felhasználónévvel és jelszóval jelentkezhetnek be a következő formátumban: [a munkanap URL-címe]/login.Flex? átirányítás = n

### <a name="create-workday-test-user"></a>Munkanapokat vizsgáló felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre munkanapokon. A munkanapokat támogató ügyfélszolgálati [csapattal](https://www.workday.com/partners-services/services/support.html) együtt veheti fel a felhasználókat a munkanap platformba. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a munkanap csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a munkanapon, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A munkanap kipróbálása az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A munkanap védetté tétele a speciális láthatósággal és ellenőrzésekkel](https://docs.microsoft.com/cloud-app-security/protect-workday)