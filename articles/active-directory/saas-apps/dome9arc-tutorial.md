---
title: 'Oktatóanyag: Azure Active Directory integráció a CloudGuard Dome9 Arcmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a CloudGuard Dome9 ív között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240d962d56e4a2dc0758f3170c51b343d22ef98d
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944575"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Oktatóanyag: A CloudGuard Dome9-ív integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a CloudGuard Dome9-ívet a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a CloudGuard Dome9-ívet, a következőket teheti:

* Vezérlés az Azure AD-ben, hogy ki férhet hozzá a CloudGuard Dome9-ív eléréséhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Point CloudGuard Dome9 arc és az Azure AD-fiókjaik használatával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A jelölőnégyzetes CloudGuard Dome9 arc egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A CloudGuard Dome9 ív **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Ellenőrzési pont CloudGuard Dome9 ív hozzáadása a katalógusból

A CloudGuard Dome9-ív Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a CloudGuard Dome9 ív elemet a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **CloudGuard Dome9 ív** kifejezést a keresőmezőbe.
1. Válassza a **jelölőnégyzet CloudGuard Dome9 ív** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a CloudGuard Dome9-ív használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a CloudGuard Dome9-ív.

Az Azure AD SSO konfigurálásához és teszteléséhez a jelölőnégyzetes CloudGuard Dome9 ív használatával végezze el a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. A **[CloudGuard Dome9-ív konfigurálása](#configure-check-point-cloudguard-dome9-arc)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy ellenőrző pont CloudGuard Dome9 ív tesztelési felhasználót](#create-check-point-cloudguard-dome9-arc-test-user)** , hogy a "B. Simon" párja legyen a CloudGuard Dome9-ív, amely a felhasználó Azure ad-ábrázolásához van csatolva.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **jelölőnégyzet CloudGuard Dome9 arc** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://secure.dome9.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > A dome9 felügyeleti portálon ki kell választania a vállalat nevének értékét, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Az értékek beszerzéséhez forduljon a [CloudGuard Dome9 arc ügyfél-támogatási csapatához](mailto:Dome9@checkpoint.com) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A CloudGuard Dome9 arc alkalmazás az SAML-jogcímeket egy adott formátumban várja, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. A fentieken kívül a pipa CloudGuard Dome9 arc alkalmazás néhány további attribútumot vár az SAML-válaszban. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon: 

    | Name (Név) |  Forrás attribútum|
    | ---------------| --------------- |
    | memberOf | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **CloudGuard Dome9-ív beállítása** szakaszban másolja be a megfelelő URL-címeket a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>A ellenőrzési pont CloudGuard Dome9 ív konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a pipa CloudGuard Dome9 arc vállalati webhelyre rendszergazdaként.

2. Kattintson a jobb felső sarokban található **Profilbeállítások** lehetőségre, majd a **Fiókbeállítások**lehetőségre. 

    ![Ellenőrzési pont CloudGuard Dome9 ív konfigurációja](./media/dome9arc-tutorial/configure1.png)

3. Navigáljon az **egyszeri bejelentkezéshez** , majd kattintson az **Engedélyezés**elemre.

    ![Ellenőrzési pont CloudGuard Dome9 ív konfigurációja](./media/dome9arc-tutorial/configure2.png)

4. Az SSO-konfiguráció szakaszban hajtsa végre a következő lépéseket:

    ![Ellenőrzési pont CloudGuard Dome9 ív konfigurációja](./media/dome9arc-tutorial/configure3.png)

    a. Adja meg a vállalat nevét a **fiókazonosító** szövegmezőben. Ezt az értéket a Azure Portal alapszintű **SAML-konfiguráció** szakaszban említett válasz-URL-címben kell használni.

    b. A **kiállító** szövegmezőbe illessze be az **Azure ad-azonosító**értékét, amelyet másolt a Azure Portal.

    c. A **identitásszolgáltató-végpont URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet másolt a Azure Portal.

    d. Nyissa meg a letöltött Base64-kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az **X. 509 tanúsítvány** szövegmezőbe.

    e. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát, ha hozzáférést biztosít a CloudGuard Dome9-ív számára.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **jelölőnégyzet CloudGuard Dome9 ív**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Ellenőrzési pont CloudGuard Dome9 ív tesztelési felhasználójának létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a CloudGuard Dome9-ívbe, az alkalmazásba kell azokat kiépíteni. A jelölőnégyzetes CloudGuard Dome9-ív támogatja az igény szerinti üzembe helyezést, de ahhoz, hogy megfelelően működjön, a felhasználónak ki kell választania egy adott szerepkört, és hozzá kell rendelnie azt a felhasználóhoz.

   >[!Note]
   >A **szerepkörök** létrehozásához és egyéb részletekért forduljon a következőhöz: [CloudGuard Dome9 arc ügyfél-támogatási csoport](mailto:Dome9@checkpoint.com).

**Felhasználói fiók manuális üzembe helyezéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a CloudGuard Dome9 arc vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználók & szerepkörök** elemre, majd kattintson a **felhasználók**elemre.

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user1.png)

3. Kattintson a **felhasználó hozzáadása**elemre.

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user2.png)

4. A **felhasználó létrehozása** szakaszban hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user3.png)

    a. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló B.Simon@contoso.come-mail címet.

    b. Az **Utónév** szövegmezőbe írja be a (z) "B" nevű felhasználó utónevét.

    c. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét, például Simon nevet.

    d. **Egyszeri bejelentkezéses felhasználó** bekapcsolása.

    e. Kattintson a **Létrehozás**gombra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen bejelöli a jelölőnégyzetes CloudGuard Dome9 ív csempét, akkor automatikusan be kell jelentkeznie a CloudGuard Dome9-ív számára, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)