---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Workday programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Workday között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046603"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Workday programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Workday szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Workday-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Workday.Control in Azure AD who has access to Workday.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve workday az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Workday egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Workday támogatja **SP** kezdeményezett SSO.

* A Workday konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Workday hozzáadása a galériából

A Workday azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Workday-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Munkanap** kifejezést a keresőmezőbe.
1. Válassza a **Workday** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Workday szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a Workday segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Workday-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Workday segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
2. **[A Workday konfigurálása](#configure-workday)** az alkalmazás oldali SSO-beállítások konfigurálásához.
    1. **[Hozzon létre munkanap-teszt felhasználó,](#create-workday-test-user)** hogy egy megfelelője B.Simon a Workday, amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Workday-alkalmazásintegrációs** lapon keresse meg a Kezelés **szakaszt,** és válassza az Egyszeri **bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`http://www.workday.com`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Ezek az értékek nem a valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. A válasz URL-címének aldomainnel kell rendelkeznie, például: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Használ valami `http://www.myworkday.com` hasonló `http://myworkday.com` szerkezet de nem. Lépjen kapcsolatba [a Workday ügyfél támogatási csapatával,](https://www.workday.com/en-us/partners-services/services/support.html) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. A Workday alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A workday alkalmazás azt várja, hogy a **névazonosító** le legyen képezve **a user.mail**, **UPN** **Edit** , stb.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Itt van leképezve a név azonosító UPN (user.userprincipalname) alapértelmezett. Az SSO sikeres működéséhez le kell képeznie a névazonosítót a Workday-fiókban (e-mail, UPN stb.) lévő tényleges felhasználói azonosítóval.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Ha a követelménynek megfelelően szeretné módosítani az **aláírási** beállításokat, kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Válassza **az Aláírási SAML-válasz és -állítás lehetőséget** az **Aláírási beállításhoz.**

    b. Kattintson a **Mentés gombra**

1. A **Munkanap beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a Workday hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Workday**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-workday"></a>Munkanap konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workday vállalati webhelyére rendszergazdaként.

2. A **Keresőmezőben** a **Bérlő beállítása szerkesztés – Biztonság** névvel a kezdőlap bal felső részén kereshet.

    ![Bérlői biztonság szerkesztése](./media/workday-tutorial/IC782925.png "Bérlői biztonság szerkesztése")

3. Az **Átirányítási URL-ek csoportban hajtsa** végre az alábbi lépéseket:

    ![Átirányítási URL-ek](./media/workday-tutorial/IC7829581.png "Átirányítási URL-ek")

    a. Kattintson **a Sor hozzáadása gombra.**

    b. A **Bejelentkezési átirányítás URL-cím,** **időtúllépésátirányítási URL-cím** és **a mobil átirányítás URL-cím** mezőbe illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portal **Munkanap beállítása** szakaszából másolt.

    c. A **Kijelentkezés átirányításurl-cím** szövegmezőbe illessze be a **kijelentkezési URL-címet,** amelyet az Azure Portal **Munkanap beállítása** szakaszából másolt.

    d. A **Használt környezetek** szövegmezőben jelölje ki a környezet nevét.  

   > [!NOTE]
   > A Környezeti attribútum értéke a bérlői URL-cím értékéhez van kötve:  
   > -Ha a Workday-bérlői URL tartományneve impl-lel kezdődik, * https://www.myworkday.com/például: "tenant"/login-saml2.htmld),* a **Environment** attribútumot Implementációra kell állítani.  
   > -Ha a domain név kezdődik valami mást, kapcsolatba kell lépnie [Workday Ügyfél támogatási csapat,](https://www.workday.com/en-us/partners-services/services/support.html) hogy a megfelelő **környezeti** értéket.

4. Az **SAML beállítása** szakaszban hajtsa végre az alábbi lépéseket:

    ![SAML beállítása](./media/workday-tutorial/IC782926.png "SAML beállítása")

    a.  Válassza **az SAML-hitelesítés engedélyezése**lehetőséget.

    b.  Kattintson **a Sor hozzáadása gombra.**

5. Az **SAML identitásszolgáltatók** szakaszban hajtsa végre az alábbi lépéseket:

    ![SAML-identitásszolgáltatók](./media/workday-tutorial/IC7829271.png "SAML-identitásszolgáltatók")

    a. Az **Identitásszolgáltató neve** mezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO).*

    b. Az Azure Portalon a **Workday beállítása** szakaszban másolja az **Azure AD-azonosító** értékét, majd illessze be a **Kiállító** szövegmezőbe.

    ![SAML-identitásszolgáltatók](./media/workday-tutorial/IC7829272.png "SAML-identitásszolgáltatók")

    c. Az Azure Portalon a **Munkanap beállítása** szakaszban másolja a **kijelentkezési URL-cím** értékét, majd illessze be a **Kijelentkezési válasz URL-címmezőjébe.**

    d. Az Azure Portalon a **Workday beállítása** szakaszban másolja a **bejelentkezési URL-címet,** majd illessze be az **IdP-szolgáltatás URL-címmezőjébe.**

    e. A **Használt környezetek** szövegmezőben jelölje ki a környezet nevét.

    f. Kattintson **az Identitásszolgáltató nyilvános kulcsának tanúsítványa**, majd **a Létrehozás gombra.**

    ![Létrehozás](./media/workday-tutorial/IC782928.png "Létrehozás")

    g. Kattintson **az x509-es nyilvános kulcs létrehozása gombra.**

    ![Létrehozás](./media/workday-tutorial/IC782929.png "Létrehozás")

6. A **View x509 Nyilvános kulcs** szakaszában hajtsa végre az alábbi lépéseket:

    ![X509 nyilvános kulcs megtekintése](./media/workday-tutorial/IC782930.png "X509 nyilvános kulcs megtekintése")

    a. A **Név** mezőbe írja be a tanúsítvány nevét (például *PPE\_SP*).

    b. Az **Érvényesség kezdete** mezőbe írja be a tanúsítvány érvényes attribútumértékét.

    c.  Az **Érvényesség a következőmezőbe** írja be a tanúsítvány érvényes attribútumértékét.

    > [!NOTE]
    > Az érvényes dátumot és az érvényeset a letöltött tanúsítványból dupla kattintással kaphatja meg.  A dátumok a **Részletek** lapon jelennek meg.
    >
    >

    d.  Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, majd másolja annak tartalmát.

    e.  A **Tanúsítvány** szövegmezőbe illessze be a vágólap tartalmát.

    f.  Kattintson az **OK** gombra.

7. Hajtsa végre a következő lépéseket:

    ![SSO-konfiguráció](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguráció")

    a.  A **Szolgáltatóazonosító** mezőbe írja be **http://www.workday.com**a következőt:

    b. Válassza **a Ne engedje le az SP által kezdeményezett hitelesítési kérelmet**lehetőséget.

    c. Hitelesítési **kérelem aláírási módszerként**válassza az **SHA256**lehetőséget.

    ![Hitelesítési kérelem aláírási módja](./media/workday-tutorial/WorkdaySSOConfiguration.png "Hitelesítési kérelem aláírási módja")

    d. Kattintson az **OK** gombra.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Ellenőrizze, hogy megfelelően állította-e be az egyszeri bejelentkezést. Abban az esetben, ha helytelen beállítással engedélyezi az egyszeri bejelentkezést, előfordulhat, hogy nem tud belépni az alkalmazásba a hitelesítő adataival, és nem zárolható. Ebben az esetben a Workday egy biztonsági másolat bejelentkezési url-címét biztosítja, ahol a felhasználók a normál felhasználónevükkel és jelszavukkal jelentkezhetnek be a következő formátumban:[A munkanap URL-címe]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Munkanap-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót a Workday alkalmazásban. Együttműködve [Workday ügyfél támogatási csapat](https://www.workday.com/partners-services/services/support.html) a felhasználók hozzáadása a Workday platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Workday csempét, automatikusan be kell jelentkeznie arra a Munkanapra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Workday szolgáltatást az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Workday védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-workday)