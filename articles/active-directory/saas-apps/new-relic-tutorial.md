---
title: 'Oktatóanyag: Azure Active Directory integráció az új ereklye fiókkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és az új ereklye között fiókkal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: jeedes
ms.openlocfilehash: 19a471b9339828250baa190b1de57c86eb22b30c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554464"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye fiókkal

Ez az oktatóanyag azt ismerteti, hogyan integrálható az új ereklye a Azure Active Directory (Azure AD) fiókkal. Ha az Azure AD-vel fiókkal integrálja az új ereklye-t, a következőket teheti:

* Vezérlés az Azure AD-ben, amely fiókkal fér hozzá az új ereklye szolgáltatáshoz.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az új Ereklyebe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Új ereklye a fiók egyszeri bejelentkezés (SSO) engedélyezett előfizetése alapján.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az új ereklye fiók támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* Az új ereklye fiókkal való konfigurálása után kényszerítheti a munkamenet-vezérlők betartatását, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-new-relic-by-account-from-the-gallery"></a>Új ereklye hozzáadása fiók alapján a katalógusból

Az új ereklye az Azure AD-be való integrálásának konfigurálásához új Ereklyeet kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az új ereklye a következőt **: fiók** .
1. Válassza ki az **új ereklye a fiókból** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-by-account"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése fiókkal az új ereklye számára

Konfigurálja és tesztelje az Azure AD SSO-t új ereklye fiókkal egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között az új ereklye fiókkal.

Az Azure AD SSO az új ereklye fiókkal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Új ereklye konfigurálása fiók egyszeri bejelentkezéssel](#configure-new-relic-by-account-sso)** – az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
    * **[Hozzon létre egy új ereklyeet a fiók tesztelése felhasználóval](#create-new-relic-by-account-test-user)** , hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó fiókkal rendelkezzen az új ereklye alapján.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **új ereklye a fiók** alkalmazás-integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)
1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    `https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` -Ne felejtse el helyettesíteni az `acc_id` új ereklye fiók azonosítóját.

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet: `rpm.newrelic.com`

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **új ereklye fiók beállítása alapján** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az új ereklye fiókhoz való hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **új ereklye a fiók alapján**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-new-relic-by-account-sso"></a>Új ereklye konfigurálása fiók egyszeri bejelentkezéssel

1. Egy másik böngészőablakban jelentkezzen be az **új ereklye** helyre a fiók vállalati webhelyén rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások**lehetőségre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797036.png "Fiókbeállítások")

3. Kattintson a **Biztonság és hitelesítés** lapra, majd kattintson az **egyszeri bejelentkezés** fülre.
   
    ![Egyszeri bejelentkezés](./media/new-relic-tutorial/ic797037.png "Egyszeri bejelentkezés")

4. Az SAML párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
    a. A letöltött Azure Active Directory-tanúsítvány feltöltéséhez kattintson a **fájl kiválasztása** lehetőségre.

    b. A **távoli bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet**, amelyet a Azure Portalból másolt.
   
    c. A **kijelentkezési URL-cím** szövegmezőbe illessze be a **KIJELENTKEZÉSI URL-címet**, amelyet a Azure Portalból másolt.

    d. Kattintson **a módosítások mentése**gombra.

### <a name="create-new-relic-by-account-test-user"></a>Új ereklye létrehozása fiók tesztelése felhasználó alapján

1. Jelentkezzen be az **új ereklyebe a fiók** vállalati webhelyén rendszergazdaként.

2. A felső menüben kattintson a **Fiókbeállítások**lehetőségre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797040.png "Fiókbeállítások")

3. A bal oldali **fiók** ablaktáblán kattintson az **Összefoglalás**, majd a **felhasználó hozzáadása**elemre.
   
    ![Fiókbeállítások](./media/new-relic-tutorial/ic797041.png "Fiókbeállítások")

4. Az **aktív felhasználók** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Aktív felhasználók](./media/new-relic-tutorial/ic797042.png "Aktív felhasználók")
   
    a. Az **e-mail** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory-felhasználó e-mail-címét.

    b. A **szerepkört** válassza a **felhasználó**lehetőségre.

    c. Kattintson **a felhasználó hozzáadása**elemre.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez a fiókhoz tartozó felhasználói fiók létrehozása vagy az új ereklye által biztosított API-k segítségével bármilyen más új ereklye is használható.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az új ereklye a fiók csempére kattint, automatikusan be kell jelentkeznie az új ereklye fiókba, amelyhez be van állítva az egyszeri bejelentkezés. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Új ereklye kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)