---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Meraki-irányítópulttal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Meraki irányítópult között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 09766236-5de0-43fd-8950-5316390ce646
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d5fd75a737a0a866b6e5c26c417458ee95845fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Meraki-irányítópulttal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Meraki-irányítópultot Azure Active Directory (Azure AD) használatával. Ha az Meraki-irányítópultot az Azure AD-vel integrálja, a következőket teheti:

* A Meraki-irányítópulthoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az irányítópultra az Azure AD-fiókjával való Meraki.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Meraki-irányítópult egyszeri bejelentkezéses (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Meraki-irányítópult támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést
* Miután konfigurálta a Meraki-irányítópultot, kikényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Meraki-irányítópult hozzáadása a gyűjteményből

A Meraki-irányítópult Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Meraki-irányítópultot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Meraki Dashboard** kifejezést a keresőmezőbe.
1. Válassza az **Meraki-irányítópult** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-meraki-dashboard"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Meraki irányítópultján

Konfigurálja és tesztelje az Azure AD SSO-t a Meraki-irányítópulttal egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Meraki-irányítópulton.

Az Azure AD SSO és a Meraki irányítópultjának konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Meraki-irányítópult egyszeri bejelentkezésének konfigurálása](#configure-meraki-dashboard-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy Meraki-irányítópultot](#create-meraki-dashboard-test-user)** , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon Meraki-irányítópulton található.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Meraki-irányítópult** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:
     
    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > A válasz URL-cím értéke nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címének értékével, amelyet az oktatóanyag későbbi részében ismertet.

1. Kattintson a **Mentés** gombra.

1. A Meraki irányítópult-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Meraki irányítópult-alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name (Név) | Forrás attribútum|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | User. userPrincipalName |
    | `https://dashboard.meraki.com/saml/attributes/role` | User. assignedroles |

    > [!NOTE]
    > A szerepkörök Azure AD-ben való konfigurálásának megismeréséhez lásd [itt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Az **SAML aláíró tanúsítvány** szakaszban kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **ujjlenyomat értékét** , és mentse a számítógépre.

    ![Ujjlenyomat értékének másolása](common/copy-thumbprint.png)

1. A **Meraki-irányítópult beállítása** szakaszban másolja ki a kijelentkezési URL-címet, és mentse a számítógépre.

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Meraki-irányítópulthoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Meraki-irányítópult**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-meraki-dashboard-sso"></a>Meraki-irányítópult egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Meraki-irányítópultra.

1. Navigáljon a **szervezeti** -> **Beállítások**elemre.

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/configure1.png)

1. A hitelesítés területen módosítsa az **SAML SSO** -t az **SAML SSO**-ra engedélyezve.

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/configure2.png)

1. Kattintson **a SAML-identitásszolgáltató hozzáadása**lehetőségre.

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/configure3.png)

1. Illessze be az **ujjlenyomat** értékét, amelyet a Azure Portal az **X. 590 CERT SHA1 ujjlenyomat** szövegmezőbe másolt. Ezután kattintson a **Mentés**gombra. A mentés után megjelenik a fogyasztói URL-cím. Másolja a fogyasztói URL-címet, és illessze be a **Válasz URL-** szövegmezőbe a Azure Portal **ALAPszintű SAML-konfiguráció szakaszában** .

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Meraki-irányítópult tesztelési felhasználójának létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Meraki-irányítópultra.

1. Navigáljon a **szervezet** -> **rendszergazdái**számára.

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/user1.png)

1. Az SAML rendszergazdai szerepkörök szakaszban kattintson az SAML- **szerepkör hozzáadása** gombra.

    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/user2.png)

1. Adja meg a szerepkört **meraki_full_admin**, a **szervezeti hozzáférés** megjelölése **teljesként** , majd kattintson a **szerepkör létrehozása**lehetőségre. Ismételje meg a folyamatot **meraki_readonly_admin**, ez az idő **csak olvasható** mezőként való megjelölése a **szervezet** számára.
 
    ![Meraki-irányítópult konfigurálása](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Meraki-irányítópult csempére kattint, automatikusan be kell jelentkeznie a Meraki-irányítópultra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Meraki-irányítópult kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Meraki-irányítópultok védetté tétele speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

