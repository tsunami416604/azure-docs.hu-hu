---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SolarWinds Orionval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a SolarWinds Orion között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.openlocfilehash: 723546d6a997d895c1a1df39b558aabb797d0a11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545097"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SolarWinds Orionval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SolarWinds Oriont Azure Active Directory (Azure AD-val). Ha az SolarWinds Oriont az Azure AD-vel integrálja, a következőket teheti:

* A SolarWinds Orionhoz hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek a SolarWinds Orionba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SolarWinds Orion-alapú egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SolarWinds Orion támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A SolarWinds Orion konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatai kiszűrése és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből is kiterjeszthető. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-solarwinds-orion-from-the-gallery"></a>A SolarWinds Orion hozzáadása a katalógusból

A SolarWinds Orion Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SolarWinds Oriont a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SolarWinds Orion** kifejezést a keresőmezőbe.
1. Válassza ki a **SolarWinds Orion** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Azure AD SSO konfigurálása és tesztelése a SolarWinds Orionhoz

Konfigurálja és tesztelje az Azure AD SSO-t a SolarWinds Orion használatával egy **B. Simon**nevű teszt felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SolarWinds Orionban.

Az Azure AD SSO és a SolarWinds Orion konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[SolarWinds Orion SSO konfigurálása](#configure-solarwinds-orion-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy SolarWinds Orion test User](#create-solarwinds-orion-test-user)** -t, hogy a SolarWinds Orionban található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van csatolva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **SolarWinds Orion** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:  `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba a [SolarWinds Orion-ügyfél támogatási csoportjával](mailto:technicalsupport@solarwinds.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A SolarWinds Orion-alkalmazás adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a SolarWinds Orion alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
    
    | Name |  Forrás attribútum|
    | ----------- | --------- |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |
    | E-mail |User. mail |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **SolarWinds Orion beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SolarWinds Orion elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **SolarWinds Orion**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-solarwinds-orion-sso"></a>SolarWinds Orion SSO konfigurálása

1. Jelentkezzen be a SolarWinds orionba, és lépjen a **Beállítások**  ->  **minden beállítás**menüpontra.

    ![ Konfiguráció SolarWinds Orion ](./media/solarwinds-orion-tutorial/settings.png)

1. A **felhasználói fiókok** szakaszban válassza ki az **SAML-konfiguráció**elemet.

    ![ Konfiguráció SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Kattintson az **Identity Provider hozzáadása**elemre.

    ![ Konfiguráció SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Hajtsa végre a következő lépéseket az **identitás-szolgáltató hozzáadása** lapon:

    ![ Konfiguráció SolarWinds Orion ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Lépjen a **configure (Konfigurálás** ) lapra.

    b. Az **identitás-szolgáltató neve** szövegmezőben adjon meg bármilyen érvényes nevet, például: `My SSO service` .

    c. Az **SSO cél URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    d.  A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    e. Nyissa meg a letöltött **tanúsítványt (Base64)** a Azure Portal a Jegyzettömbben, és illessze be a tartalmat az **X. 509 aláíró tanúsítvány** szövegmezőbe.

    f. Kattintson a **Save (Mentés**) gombra.

### <a name="create-solarwinds-orion-test-user"></a>SolarWinds Orion-teszt felhasználó létrehozása

1. Jelentkezzen be a SolarWinds Orion webhelyére, és lépjen a **Beállítások**  ->  **minden beállítás**menüpontra.

    ![ SolarWinds Orion Create test User ](./media/solarwinds-orion-tutorial/settings.png)

1. A **felhasználói fiókok** szakaszban válassza a **fiókok kezelése**lehetőséget.

    ![ SolarWinds Orion Create test User ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Az **egyes fiókok** lapon kattintson az **új fiók hozzáadása**lehetőségre.

    ![ SolarWinds Orion Create test User ](./media/solarwinds-orion-tutorial/create-user.png)

1. Válassza ki a fiók típusát, amelyet SAML-felhasználók vagy-csoportok létrehozásához kell létrehoznia.

    ![ SolarWinds Orion Create test User ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  A **név azonosító** szövegmezőbe írja be azt a nevet, amelynek meg kell egyeznie a felhasználónévvel vagy a csoport nevével pontosan az Azure ad-ben.

1.  Kattintson a **tovább** gombra, majd küldje el a lapot.

    ![ SolarWinds Orion Create test User ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SolarWinds Orion csempére kattint, automatikusan be kell jelentkeznie a SolarWinds-Orionba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A SolarWinds Orion kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A SolarWinds Orion védetté tétele fejlett láthatósággal és ellenőrzésekkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

