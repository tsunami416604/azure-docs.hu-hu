---
title: 'Oktatóanyag: Azure Active Directory integráció a iLMS-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944023"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Oktatóanyag: A iLMS integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a iLMS a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az iLMS-t az Azure AD-vel, a következőket teheti:

* A iLMS-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a iLMS az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* iLMS egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. a iLMS támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO

## <a name="adding-ilms-from-the-gallery"></a>ILMS hozzáadása a gyűjteményből

A iLMS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a iLMS a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **iLMS** kifejezést a keresőmezőbe.
1. Válassza ki a **iLMS** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a iLMS a **Britta Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a iLMS-ben.

Az Azure AD SSO és a iLMS konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[ILMS SSO konfigurálása](#configure-ilms-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre iLMS-teszt felhasználót](#create-ilms-test-user)** – hogy a iLMS Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **iLMS** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe illessze be az SAML-beállítások a iLMS felügyeleti portálon a **szolgáltatótól** másolt **azonosító** értékét.

    b. A **Válasz URL-címe** szövegmezőbe illessze be a iLMS felügyeleti portál SAML-beállítások a **szolgáltatótól** másolt **végpont (URL)** értékét a következő mintával`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőben illessze be a iLMS felügyeleti portál SAML-beállítások a **szolgáltató** részéből másolt **végpont (URL)** értékét a következőre:`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Az JIT-kiépítés engedélyezéséhez a iLMS-alkalmazás megadott formátumban várja az SAML-jogcímeket, amelyhez egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    > [!NOTE]
    > Az attribútumok leképezéséhez engedélyeznie kell a nem **felismert felhasználói fiók létrehozását** a iLMS-ben. Az attribútumok konfigurációjának megismeréséhez kövesse az [itt](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) található utasításokat.

1. A fentieken kívül a iLMS alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név) | Forrás attribútum|
    | --------|------------- |
    | osztály | felhasználó. részleg |
    | régió | felhasználó. állapot |
    | Szervezeti egység | user.jobtitle |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **ILMS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-ilms-sso"></a>ILMS SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **iLMS felügyeleti portálra** rendszergazdaként.

2. Kattintson az **SSO: SAML** lehetőségre a **Beállítások** lapon az SAML-beállítások megnyitásához, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png)

3. Bontsa ki a szolgáltató szakaszt, és másolja az **azonosító** és a **végpont (URL)** értéket.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

4. Az **identitás-szolgáltató** szakaszban kattintson a **Metaadatok importálása**elemre.

5. Válassza ki a Azure Portalból letöltött **összevonási metaadat** -fájlt az **SAML aláíró tanúsítvány** szakaszból.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Ha engedélyezni szeretné a JIT-kiépítés használatát iLMS-fiókok létrehozásához a nem felismerhető felhasználók számára, kövesse az alábbi lépéseket:

    a. Keresse meg a nem **ismert felhasználói fiók létrehozását**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Képezze le az attribútumokat az Azure AD-ben a iLMS attribútumaival. Az attribútum oszlopban adja meg az attribútumok nevét vagy az alapértelmezett értéket.

    c. Lépjen az **üzleti szabályok** lapra, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/5.png)

    d. Az egyszeri bejelentkezés időpontjában még nem létező régiók, osztályok és részlegek létrehozásához keresse fel a nem **ismert régiók, osztályok és részlegek** létrehozását.

    e. A **bejelentkezés során jelölje be a felhasználói profil frissítése** annak megadásához, hogy a felhasználó profilja frissítve van-e az egyes egyszeri bejelentkezésekkel.

    f. Ha a **felhasználó profilja nem kötelező mezőinek frissítése jelölőnégyzet be** van jelölve, akkor a bejelentkezéskor üres, opcionális profilok mező is a felhasználó iLMS-profiljában üres értékeket fog tartalmazni ezekhez a mezőkhöz.

    g. Tekintse meg a **hibajelentési értesítő e-mailt** , és adja meg annak a felhasználónak az e-mail-címét, ahová az értesítő e-mailt kapni szeretné.

7. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a iLMS hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **iLMS**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-ilms-test-user"></a>ILMS-tesztelési felhasználó létrehozása

Az alkalmazás a felhasználók üzembe helyezését támogatja, és a hitelesítéssel rendelkező felhasználók automatikusan jönnek létre az alkalmazásban. A JIT működni fog, ha a nem **felismert felhasználói fiók létrehozása** jelölőnégyzetre kattintott az SAML konfigurációs beállításakor a iLMS felügyeleti portálján.

Ha manuálisan kell létrehoznia egy felhasználót, kövesse az alábbi lépéseket:

1. Jelentkezzen be a iLMS vállalati webhelyre rendszergazdaként.

2. Kattintson a **felhasználó regisztrálása** elemre a **felhasználók** lapon a **felhasználó regisztrálása** lap megnyitásához.

   ![Alkalmazott hozzáadása](./media/ilms-tutorial/3.png)

3. A **felhasználó regisztrálása** lapon hajtsa végre a következő lépéseket.

    ![Alkalmazott hozzáadása](./media/ilms-tutorial/create_testuser_add.png)

    a. Az **Utónév** szövegmezőbe írja be az utónév nevét (például Britta).

    b. A **vezetéknév** szövegmezőbe írja be a vezetéknevet, például Simon nevet.

    c. Az **e-mail-azonosító** szövegmezőbe írja be a felhasználó e-mail címét BrittaSimon@contoso.com, például:.

    d. A **régió** legördülő menüben válassza ki a régió értékét.

    e. A **divízió** legördülő menüben válassza ki a osztás értékét.

    f. A **részleg** legördülő menüben válassza ki a részleg értékét.

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A regisztrációs e-mail **küldése** jelölőnégyzet bejelölésével elküldheti a felhasználónak a regisztrációs e-maileket.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a iLMS csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a iLMS, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
