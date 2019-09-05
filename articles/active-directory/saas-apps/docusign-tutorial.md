---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a DocuSign | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és DocuSign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306293"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a DocuSign

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a DocuSign a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az DocuSign-t az Azure AD-vel, a következőket teheti:

* A DocuSign-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a DocuSign az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* DocuSign egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A DocuSign támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A DocuSign támogatja **az igény** szerinti felhasználói üzembe helyezést

* A DocuSign támogatja az [automatikus felhasználó-kiépítés](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial) használatát

## <a name="adding-docusign-from-the-gallery"></a>DocuSign hozzáadása a gyűjteményből

A DocuSign Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a DocuSign a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **DocuSign** kifejezést a keresőmezőbe.
1. Válassza ki a **DocuSign** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a DocuSign

Konfigurálja és tesztelje az Azure AD SSO-t a DocuSign a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a DocuSign-ben.

Az Azure AD SSO és a DocuSign konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[DOCUSIGN SSO konfigurálása](#configure-docusign-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre DocuSign-teszt felhasználót](#create-docusign-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-DocuSign rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **DocuSign** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval, amely a jelen oktatóanyagban az **SAML 2,0-végpontok** című szakaszban található meg.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. A **DocuSign beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a DocuSign.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **DocuSign**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-docusign-sso"></a>DocuSign SSO konfigurálása

1. A DocuSign belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés DocuSign** gombra a DocuSign alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a DocuSign való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-5-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a DocuSign, nyisson meg egy új böngészőablakot, és jelentkezzen be a DocuSign vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Az oldal jobb felső részén kattintson a profil **emblémára** , majd a **Ugrás a rendszergazdára**elemre.
  
    ![Egyszeri bejelentkezés konfigurálása][51]

5. A tartományi megoldások lapon kattintson a **tartományok** elemre.

    ![Egyszeri bejelentkezés konfigurálása][50]

6. A **tartományok** szakaszban kattintson a **jogcím tartomány**elemre.

    ![Egyszeri bejelentkezés konfigurálása][52]

7. A **jogcím a tartományhoz** párbeszédpanelen a **tartománynév** szövegmezőbe írja be a vállalati tartományt, majd kattintson a **jogcím**elemre. Győződjön meg arról, hogy a tartomány ellenőrzése megtörténik, és az állapota aktív.

    ![Egyszeri bejelentkezés konfigurálása][53]

8. A tartományi megoldások lapon kattintson az **identitás-szolgáltatók**elemre.
  
    ![Egyszeri bejelentkezés konfigurálása][54]

9. Az **identitás-szolgáltatók** szakaszban kattintson az **Identity Provider hozzáadása**elemre. 

    ![Egyszeri bejelentkezés konfigurálása][55]

10. Az **Identity Provider beállításai** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása][56]

    a. A **név** szövegmezőbe írja be a konfiguráció egyedi nevét. Ne használjon szóközt.

    b. Az **Identity Provider kiállító szövegmezőben**illessze be az **Azure ad-azonosító**értékét, amelyet a Azure Portalból másolt.

    c. Az **Identity Provider bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet a Azure Portalból másolt.

    d. Az **Identitáskezelő kijelentkezési URL-címe** szövegmezőbe illessze be a **KIjelentkezési URL-címet**, amelyet a Azure Portalból másolt.

    e. Válassza a **AuthN-kérelem aláírása**lehetőséget.

    f. A **AuthN-kérelem küldéséhez**válassza a **post**lehetőséget.

    g. Mint a **kijelentkezési kérelem küldése a alapján**, válassza a **beolvasás**lehetőséget.

    h. Az **Egyéni attribútumok leképezése** szakaszban kattintson az **Új leképezés hozzáadása**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása][62]

    i. Válassza ki azt a mezőt, amelyet le szeretne képezni az Azure AD-jogcím alapján. Ebben a példában az **EmailAddress** jogcím a következő értékkel **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** van leképezve:. Ez az alapértelmezett jogcím neve az Azure AD-től az e-mail-jogcímhez, majd kattintson a **Save (Mentés**) gombra.

    ![Egyszeri bejelentkezés konfigurálása][57]

    > [!NOTE]
    > A megfelelő **felhasználói azonosító** használatával rendelje hozzá a felhasználót az Azure ad-ből DocuSign felhasználói leképezéshez. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket a szervezeti beállítások alapján.

    j. Az **Identity Provider-tanúsítványok** szakaszban kattintson a **tanúsítvány hozzáadása**elemre, majd töltse fel az Azure ad-portálról letöltött tanúsítványt, és kattintson a **Mentés**gombra.

    ![Egyszeri bejelentkezés konfigurálása][58]

    k. Az **Identity Providers** szakaszban kattintson a **műveletek**, majd a **végpontok**elemre.

    ![Egyszeri bejelentkezés konfigurálása][59]

    l. A **DocuSign felügyeleti portál** **SAML 2,0-végpontok megtekintése** szakaszában hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása][60]

    * Másolja a **szolgáltatói kiállító URL-címét**, majd illessze be a Azure Portal **alapszintű SAML-konfiguráció** szakaszának **azonosító** szövegmezőbe.

    * Másolja a **szolgáltatói bejelentkezési URL-címet**, majd illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    * Kattintson a **Bezárás** gombra

### <a name="create-docusign-test-user"></a>DocuSign-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a DocuSign-ben. A DocuSign támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a DocuSign-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [DocuSign támogatási csapatával](https://support.docusign.com/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a DocuSign csempére kattint, automatikusan be kell jelentkeznie arra a DocuSign, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A DocuSign kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png