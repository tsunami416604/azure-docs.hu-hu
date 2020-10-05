---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a AskYourTeam | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AskYourTeam között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: 6ab6a355b986daeca41ffd18a51689cd9d880dcd
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713813"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a AskYourTeam

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AskYourTeam a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az AskYourTeam-t az Azure AD-vel, a következőket teheti:

* A AskYourTeam-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a AskYourTeam az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AskYourTeam egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A AskYourTeam **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.
* A AskYourTeam konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam hozzáadása a gyűjteményből

A AskYourTeam Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a AskYourTeam a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **AskYourTeam** kifejezést a keresőmezőbe.
1. Válassza ki a **AskYourTeam** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a AskYourTeam

Konfigurálja és tesztelje az Azure AD SSO-t a AskYourTeam a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a AskYourTeam-ben.

Az Azure AD SSO és a AskYourTeam konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ASKYOURTEAM SSO konfigurálása](#configure-askyourteam-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre AskYourTeam-teszt felhasználót](#create-askyourteam-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-AskYourTeam rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **AskYourTeam** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-értékekkel, amelyeket az oktatóanyag későbbi részében ismertet.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **AskYourTeam beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a AskYourTeam.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **AskYourTeam**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-askyourteam-sso"></a>AskYourTeam SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a AskYourTeam webhelyre.

1. Kattintson a **saját szervezet**elemre.

    ![A képernyőfelvételen a saját szervezet hivatkozás látható.](./media/askyourteam-tutorial/user1.png)

1. Kattintson az **integrációk**elemre.

    ![A képernyőképen az integrációk hivatkozás látható.](./media/askyourteam-tutorial/configure1.png)

1. Kattintson a **beállítások szerkesztése**elemre.

    ![Képernyőfelvétel: az egyszeri bejelentkezési üzenet, amely a beállítások szerkesztése gombot tartalmazza.](./media/askyourteam-tutorial/configure2.png)

1. Az **egyszeri bejelentkezési integráció szerkesztése** oldalon hajtsa végre a következő lépéseket: 

    ![A képernyőképen az egyszeri bejelentkezéses integráció szerkesztése látható, ahol megadhatja a lépés értékeit.](./media/askyourteam-tutorial/configure3.png)

    a. Az **SAML egyszeri bejelentkezési szolgáltatás URL-címe** szövegmezőbe illessze be azt a **bejelentkezési URL** -címet, amelyet a Azure Portal másolt.

    b. Az **SAML-entitás azonosítója** szövegmezőbe illessze be azt az **Azure ad-azonosító** értéket, amelyet a Azure Portal másolt.

    c. A kijelentkezési **URL** szövegmezőbe illessze be azt a **KIJELENTKEZÉSI URL-címet** , amelyet a Azure Portal másolt.

    d. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat az **SAML aláíró tanúsítvány – Base64** szövegmezőbe.

    > [!NOTE]
    > Azt is megteheti, hogy az **összevonási metaadatok XML-** fájlját is feltölti a **fájl kiválasztása** lehetőségre kattintva.

    e. Másolja a **Válasz URL-címét (a fogyasztói szolgáltatás URL-címe)** értéket, illessze be ezt az értéket a **Válasz URL-cím** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    f. Másolja a **bejelentkezési URL-cím** értéket, illessze be ezt az értéket a Azure Portal **alapszintű SAML-konfiguráció** szakaszában található **bejelentkezési URL** szövegmezőbe.

    : Kattintson a **Mentés** gombra.

### <a name="create-askyourteam-test-user"></a>AskYourTeam-tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a AskYourTeam webhelyre.

1. Kattintson a **saját szervezet**elemre.

    ![Képernyőfelvétel: a saját szervezet hivatkozása, amely a feladat elindítását mutatja.](./media/askyourteam-tutorial/user1.png)

1. Kattintson a **felhasználók** elemre, és válassza az **új felhasználó**lehetőséget.

    ![Képernyőfelvétel: a felhasználók hivatkozása az új felhasználóval.](./media/askyourteam-tutorial/user2.png)

1. Az **új felhasználó** szakaszban hajtsa végre a következő lépéseket:

    ![A képernyőképen egy új felhasználói szakasz jelenik meg, ahol megadhatja a felhasználói adatokat.](./media/askyourteam-tutorial/user3.png)

    1. **A Utónév szövegmezőbe** írja be a felhasználó utónevét.

    1. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét.

    1. Az **e-mail** szövegmezőbe írja be a felhasználóhoz hasonló EmailAddress címet B.Simon@contoso.com .

    1. Válassza ki a felhasználó **szerepkörét** a szervezet követelményeinek megfelelően.

    1. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a AskYourTeam csempére kattint, automatikusan be kell jelentkeznie arra a AskYourTeam, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A AskYourTeam kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
