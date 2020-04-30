---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure AD SAML Toolkit használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Azure AD SAML Toolkit között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d2681c09030ff0f36938d7a09e1d1b2e9aa645c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82166310"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az Azure AD SAML Toolkit használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Azure AD SAML-eszközkészletet Azure Active Directory (Azure AD) használatával. Ha az Azure AD SAML-eszközkészletet az Azure AD-vel integrálja, a következőket teheti:

* Olyan Azure AD-beli vezérlés, amely hozzáfér az Azure AD SAML Toolkit-hez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Azure AD SAML-eszközkészletbe az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Azure AD SAML-eszközkészlet – egyszeri bejelentkezés (SSO) – engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az Azure AD SAML Toolkit támogatja az **SP** által kezdeményezett egyszeri bejelentkezést
* Az Azure AD SAML Toolkit konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Az Azure AD SAML Toolkit hozzáadása a katalógusból

Az Azure ad SAML Toolkit Azure AD-integrációjának konfigurálásához hozzá kell adnia az Azure AD SAML-eszközkészletet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **Azure ad SAML Toolkit** kifejezést.
1. Válassza az **Azure ad SAML Toolkit** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az Azure AD SAML Toolkit-hez

Konfigurálja és tesztelje az Azure AD SSO-t az Azure AD SAML Toolkit segítségével egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure ad-felhasználó és a kapcsolódó felhasználó között az Azure AD SAML Toolkit-ben.

Az Azure AD SSO Azure AD SAML-eszközkészlettel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[Azure ad SAML TOOLKIT SSO konfigurálása](#configure-azure-ad-saml-toolkit-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Azure ad SAML Toolkit-tesztet](#create-azure-ad-saml-toolkit-test-user)** , hogy az Azure ad SAML-eszközkészletben található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-partnert.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. Az [Azure Portal](https://portal.azure.com/)az **Azure ad SAML Toolkit** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet:`https://samltoolkit.azurewebsites.net/`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://samltoolkit.azurewebsites.net`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet:`https://samltoolkit.azurewebsites.net/SAML/Consume`

    > [!NOTE]
    > Ezek az értékek nem valós értékek. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-cím, azonosító és válasz URL-értékekkel, amelyeket az oktatóanyag későbbi részében ismertet.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **Azure ad SAML-eszközkészlet beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban az Azure AD SAML Toolkit elérésének biztosításával engedélyezi a B. Simon számára az Azure egyszeri bejelentkezés használatát.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **Azure ad SAML Toolkit**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Az Azure AD SAML Toolkit egyszeri bejelentkezésének konfigurálása

1. Nyisson meg egy új böngészőablakot, ha még nem regisztrált az Azure AD SAML-eszközkészlet webhelyén, először regisztráljon a **regisztrálás**gombra kattintva. Ha már regisztrált, jelentkezzen be az Azure AD SAML Toolkit vállalati webhelyre a regisztrált bejelentkezési hitelesítő adatok használatával.

    ![Azure AD SAML Toolkit-regisztráció](./media/saml-toolkit-tutorial/register.png)

1. Kattintson az **SAML-konfigurációra**.

    ![Azure AD SAML Toolkit SAML-konfiguráció](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kattintson a **Létrehozás**gombra.

    ![Azure AD SAML-eszközkészlet – SSO létrehozása](./media/saml-toolkit-tutorial/createsso.png)

1. Az **SAML SSO-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Azure AD SAML-eszközkészlet – SSO létrehozása](./media/saml-toolkit-tutorial/fill-details.png)

    1. A **bejelentkezési URL** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    1. Az **Azure ad-azonosító** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    1. A **kijelentkezési URL** szövegmezőbe illessze be a **kijelentkezési URL-** értéket, amelyet a Azure Portal másolt.

    1. Kattintson a **fájl kiválasztása** elemre, és töltse fel a Azure Portalból letöltött **tanúsítvány-(RAW-)** fájlt.

    1. Kattintson a **Létrehozás**gombra.

    1. Másolja be a bejelentkezési URL-címet, az azonosítót és az ACS URL-értékeket az SAML Toolkit SSO konfigurációs lapján, és illessze be a beillesztett szövegmezőket a Azure Portal **alapszintű SAML-konfiguráció szakaszában** .

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML Toolkit-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre az Azure AD SAML Toolkit-ben. Az Azure AD SAML Toolkit az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Azure AD SAML Toolkit-ben, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az Azure AD SAML Toolkit csempére kattint, automatikusan be kell jelentkeznie az Azure AD SAML-eszközkészletbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az Azure AD SAML Toolkit kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az Azure AD SAML eszközkészletének és a speciális láthatóságának és ellenőrzésének biztosítása](https://docs.microsoft.com/cloud-app-security/protect-azure)