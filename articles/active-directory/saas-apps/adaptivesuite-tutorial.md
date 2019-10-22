---
title: 'Oktatóanyag: Azure Active Directory integráció az adaptív ismeretekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az adaptív adatfelismerések között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "70213616"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Oktatóanyag: adaptív adatáttekintések integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az adaptív információkat Azure Active Directory (Azure AD) használatával. Az adaptív bepillantást az Azure AD-vel integrálva a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér az adaptív adatfelismerésekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való adaptív bepillantást.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Adaptív adatellenőrzési egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az adaptív adatfelismerések támogatják a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptív adatáttekintések hozzáadása a katalógusból

Az adaptív betekintést az Azure AD-be való integrálásának konfigurálásához adaptív betekintést kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **adaptív bepillantást** .
1. Válassza ki az **adaptív bepillantást** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az adaptív ismeretekkel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az adaptív megállapításokban.

Az Azure AD SSO és az adaptív adatfelismerések konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[Adaptív adatáttekintések konfigurálása](#configure-adaptive-insights-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre adaptív adatellenőrzési tesztet](#create-adaptive-insights-test-user)** , hogy a B. Simon párja legyen a felhasználó Azure ad-képviseletéhez kapcsolódó adaptív adatáttekintésekben.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **adaptív** betekintő alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Az adaptív adatok **SAML SSO-beállításai** lapról lekérheti az azonosító (Entity ID) és a válasz URL-értékeit.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **adaptív adatáttekintések beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Adaptív bepillantást igénylő egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az adaptív bepillantást a vállalati webhelyre rendszergazdaként.

2. Nyissa meg az **adminisztrációt**.

    ![Felügyeleti](./media/adaptivesuite-tutorial/ic805644.png "Felügyeleti")

3. A **felhasználók és szerepkörök** szakaszban kattintson az **SAML SSO-beállítások**elemre.

    ![SAML SSO-beállítások kezelése](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO-beállítások kezelése")

4. Az **SAML SSO-beállítások** oldalon hajtsa végre a következő lépéseket:

    ![SAML SSO-beállítások](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-beállítások")

    a. Az **identitás-szolgáltató neve** szövegmezőbe írja be a konfiguráció nevét.

    b. Illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét az **Identity Provider entitás-azonosító** szövegmezőbe.

    c. Illessze be a Azure Portalba másolt **bejelentkezési URL** -címet az **Identity Provider SSO URL** szövegmezőbe.

    d. Illessze be az Azure Portalból másolt **kijelentkezési URL-címet** az **Egyéni kijelentkezési URL-** szövegmezőbe.

    e. A letöltött tanúsítvány feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre.

    f. Válassza ki a következőt:

     * **SAML felhasználói azonosító**, válassza a **felhasználó adaptív elemzése felhasználónevet**.

     * Az **SAML felhasználói azonosító helye**, a **tulajdonos NameID válassza a felhasználói azonosító**elemet.

     * **SAML-NameID formátuma**, válassza az **e-mail cím**elemet.

     * **Engedélyezze az SAML**-t, válassza az **SAML egyszeri bejelentkezés engedélyezése és a közvetlen adaptív**adatellenőrzési bejelentkezés lehetőséget.

    g. Másolja át az **adaptív betekintési egyszeri bejelentkezés URL-címét** , és illessze be az **azonosítót (Entity ID)** és a **Válasz URL** -szövegmezőket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az adaptív adatfelismerésekhez való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **adaptív**adatellenőrzések lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-adaptive-insights-test-user"></a>Adaptív adatáttekintési teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek az adaptív adatfelismerésbe, az adaptív adatfelismeréseknek kell kiépíteni őket. Adaptív adatmennyiség esetén a kiépítés manuális feladat.

**A felhasználók üzembe helyezésének konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **adaptív bepillantást** a vállalati webhelyre rendszergazdaként.

2. Nyissa meg az **adminisztrációt**.

   ![Felügyeleti](./media/adaptivesuite-tutorial/IC805644.png "Felügyeleti")

3. A **felhasználók és szerepkörök** szakaszban kattintson a **felhasználók**elemre.

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/IC805648.png "Felhasználó hozzáadása")

4. Az **új felhasználó** szakaszban hajtsa végre a következő lépéseket:

   ![Küldés](./media/adaptivesuite-tutorial/IC805649.png "Küldés")

   a. Adja meg egy érvényes Azure Active Directory felhasználó **nevét**, **felhasználónevét**, **e-mail-címét**és **jelszavát** , amelyet szeretne a kapcsolódó szövegmezőbe beépíteni.

   b. Válasszon egy **szerepkört**.

   c. Kattintson a **Submit (Küldés**) gombra.

> [!NOTE]
> A HRE felhasználói fiókjainak kiépítéséhez bármilyen más, adaptív információkkal rendelkező felhasználói fiók létrehozására szolgáló eszközt vagy API-t is használhat.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az adaptív bepillantások csempére kattint, automatikusan be kell jelentkeznie az olyan adaptív adatfelismerésekhez, amelyekhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

