---
title: 'Oktatóanyag: Azure Active Directory integráció a Atlassian-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Atlassian-felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80384024"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Oktatóanyag: a Atlassian-felhő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Atlassian-felhőt Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Atlassian-felhőt, a következőket teheti:

* A Atlassian-felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Atlassian felhőbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Atlassian-alapú Felhőbeli egyszeri bejelentkezés (SSO) engedélyezett előfizetés.
* A Atlassian Cloud Products szolgáltatáshoz való Security Assertion Markup Language (SAML) egyszeri bejelentkezés engedélyezéséhez be kell állítania a Atlassian-hozzáférést. További információ a [Atlassian-hozzáférésről]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

* A Atlassian Cloud támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Atlassian Cloud támogatja [a felhasználók automatikus üzembe](atlassian-cloud-provisioning-tutorial.md) helyezését és megszüntetését
* A Atlassian-felhő konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben biztosítja a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian-felhő hozzáadása a gyűjteményből

A Atlassian Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Atlassian-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Atlassian Cloud** kifejezést a keresőmezőbe.
1. Válassza a **Atlassian Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Atlassian-felhővel egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Atlassian-felhőben.

Az Azure AD SSO Atlassian-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Atlassian Cloud SSO konfigurálása](#configure-atlassian-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy Atlassian Cloud test User](#create-atlassian-cloud-test-user)** -t, hogy a Atlassian-felhőben található B. Simon párja legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Atlassian Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://auth.atlassian.com/saml/<unique ID>`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Kattintson a **további URL-címek beállítása**elemre.

    d. A **továbbítási állapot** szövegmezőbe írja be a következő mintát használó URL-címet:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > Az előző értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezeket a valós értékeket a **Atlassian Cloud SAML konfigurációs** képernyőjén tekintheti meg, amelyet később a **Atlassian Cloud SSO konfigurálása** az oktatóanyagban című 7. lépésében talál.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Illessze be az értéket abból a példányból, amelyet a Atlassian Cloud admin portálra történő bejelentkezési használ.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. A Atlassian Cloud Application egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A Atlassian Cloud Application a **NameIdentifier** a **User. mail**használatával rendeli hozzá, ezért az attribútum-hozzárendelést úgy kell módosítania, hogy a **Szerkesztés** ikonra kattint, és megváltoztatja az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Atlassian-felhő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Atlassian-felhőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Atlassian-felhő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-atlassian-cloud-sso"></a>Atlassian Cloud SSO konfigurálása

1. A Atlassian-felhőben lévő konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés Atlassian a felhőben** a Atlassian Cloud alkalmazásra. Itt adja meg a rendszergazdai hitelesítő adatokat a Atlassian-felhőbe való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-7-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Atlassian-felhőt, nyisson meg egy új böngészőablakot, és jelentkezzen be a Atlassian Cloud vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Az egyszeri bejelentkezés konfigurálása előtt ellenőriznie kell a tartományt. További információ: [Atlassian tartomány-ellenőrzési](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentum.

1. A bal oldali ablaktáblán válassza a **biztonsági** > **SAML egyszeri bejelentkezés**lehetőséget. Ha még nem tette meg, fizessen elő a Atlassian Identity Managerre.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Az **SAML-konfiguráció hozzáadása** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Az **identitás-szolgáltatói entitás azonosítója** mezőben illessze be a Azure Portalból másolt **Azure ad-azonosítót** .

    b. Illessze be a Azure Portalból másolt **bejelentkezési URL** -címet az **Identity Provider bejelentkezési URL-címe** mezőbe.

    c. Nyissa meg a letöltött tanúsítványt a Azure Portal egy. txt fájlban, másolja ki az értéket (a *kezdő tanúsítvány* és a *záró tanúsítványlánc* nélkül), majd illessze be a **nyilvános X509-tanúsítvány** mezőbe.

    d. Kattintson a **konfiguráció mentése**gombra.

1. A megfelelő URL-címek beállításának biztosításához frissítse az Azure AD-beállításokat a következő módon:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Az SAML ablakban másolja ki az **SP Identity ID azonosítót** , majd a Azure Portal a Atlassian Cloud **Basic SAML-konfiguráció**alatt illessze be azt az **azonosító** mezőbe.

    b. Az SAML-ablakban másolja az **SP-t a fogyasztói szolgáltatás URL-címére** , majd a Azure Portal a Atlassian Cloud **Basic SAML-konfiguráció**alatt illessze be a **Válasz URL-címe** mezőbe. A bejelentkezési URL-cím a Atlassian-felhő bérlői URL-címe.

    > [!NOTE]
    > Ha Ön egy meglévő ügyfél, az **SP Identity ID** és az **SP-azonosító fogyasztói szolgáltatás URL-címének** a Azure Portalban való frissítése után válassza az **Igen, a konfiguráció frissítése**lehetőséget. Ha Ön új ügyfél, akkor kihagyhatja ezt a lépést.

### <a name="create-atlassian-cloud-test-user"></a>Atlassian Cloud test-felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználók bejelentkezzenek a Atlassian-felhőbe, a következő módon helyezze üzembe a felhasználói fiókokat manuálisan a Atlassian-felhőben:

1. Az **Adminisztráció** ablaktáblán válassza a **felhasználók**lehetőséget.

    ![A Atlassian Cloud Users hivatkozás](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Ha felhasználót szeretne létrehozni a Atlassian-felhőben, válassza a **felhasználó meghívása**lehetőséget.

    ![Atlassian felhőalapú felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Az **e-mail cím** mezőbe írja be a felhasználó e-mail-címét, majd rendelje hozzá az alkalmazáshoz való hozzáférést.

    ![Atlassian felhőalapú felhasználó létrehozása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Ha e-mailben szeretne meghívót küldeni a felhasználónak, válassza a **felhasználók meghívása**lehetőséget. A rendszer elküld egy e-mailes meghívót a felhasználónak, és a meghívót elfogadva a felhasználó aktív a rendszeren.

> [!NOTE]
> A felhasználókat a **tömeges létrehozás** gomb kiválasztásával is létrehozhatja a **felhasználók** szakaszban.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Atlassian felhő csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Atlassian-felhőbe, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Atlassian-felhőt az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A Atlassian-felhő elleni védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)