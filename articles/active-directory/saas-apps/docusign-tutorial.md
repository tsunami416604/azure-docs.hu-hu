---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a DocuSign használatával | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a DocuSign között.
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f969c3bc6f546025b3bbe5826181efdfa69be0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983635"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a DocuSign-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a DocuSign szolgáltatást a Microsoft Azure Active Directoryval (Azure AD). Ha integrálja a DocuSign-t az Azure AD-vel, a következőket teheti:

* Az Azure AD használatával szabályozhatja, hogy ki férhet hozzá a DocuSignhoz.
* Engedélyezze az automatikus bejelentkezést a DocuSign szolgáltatásba a felhasználók számára az Azure AD-fiókjaikon keresztül.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa [el az Egyszeri bejelentkezés az Azure AD-ben lévő alkalmazásokra című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Olyan DocuSign-előfizetés, amely egyszeri bejelentkezéssel (SSO) engedélyezett.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben, hogy ellenőrizze, hogy:

* A DocuSign támogatja a szolgáltató (SP) által kezdeményezett egyszeri bejelentkezést.

* A DocuSign támogatja *a just-in-time* felhasználói kiépítést.

* A DocuSign támogatja az [automatikus felhasználói kiépítést.](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)
* A DocuSign konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign hozzáadása a galériából

A DocuSign azure AD-be való integrációjának konfigurálásához hozzá kell adnia a DocuSign-t a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **az Összes alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **DocuSign kifejezést** a keresőmezőbe.
1. Válassza a **DocuSign** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a DocuSign számára

Konfigurálja és tesztelje az Azure AD SSO-t a DocuSign segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó a DocuSign.

Az Azure AD SSO konfigurálásához és teszteléséhez a DocuSign segítségével hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    1. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. [Konfigurálja a DocuSign Egyszeri bejelentkezési](#configure-docusign-sso) beállításokat az alkalmazás oldalon.
    1. [Hozzon létre egy DocuSign teszt felhasználó](#create-docusign-test-user) tapra egy megfelelője B.Simon a DocuSign, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
1. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD-sSO engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **DocuSign** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** majd válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** tollikonját a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Ezek a zárójelbe tett értékek helyőrzők. Cserélje le őket a tényleges bejelentkezési URL-cím és azonosító értékeire. Ezeket az adatokat az oktatóanyag későbbi részében, az "SAML 2.0 végpontok megtekintése" című szakasz ismerteti.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64) című részt.** A **Letöltés gombra** a tanúsítvány letöltéséhez és a számítógépre való mentéséhez válassza a Letöltés lehetőséget.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **DocuSign beállítása** szakaszban másolja a megfelelő URL-címet (vagy URL-eket) az Ön igényei nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.
1. A képernyő tetején válassza az **Új felhasználó**lehetőséget.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja be a **B.Simon**értéket.  
   1. A **Felhasználónév** mezőbe `<username>@<companydomain>.<extension>`írja be a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít a B.Simon nak a DocuSign-hoz, hogy a felhasználó használhatja az Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **DocuSign**lehetőséget.
1. Az alkalmazás áttekintése lapon keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok**lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a **Felhasználók** listában, majd nyomja le a **kijelölés** gombot a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd nyomja meg a **kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-docusign-sso"></a>DocuSign SSO konfigurálása

1. A DocuSign konfigurációjának automatizálásához telepítenie kell a My Apps Secure Bejelentkezésböngésző bővítményt **a Bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadja a bővítményt a böngészőhöz, válassza a **Setup DocuSign**lehetőséget. A rekedadt a DocuSign alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a DocuSign-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3–5.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a DocuSign alkalmazást, nyisson meg egy új böngészőablakot, és jelentkezzen be a DocuSign vállalati webhelyére rendszergazdaként.

4. A lap jobb felső sarkában jelölje ki a profilemblémát, majd kattintson a Rendszergazda kiválasztása **parancsra.**
  
    ![Ugrás a Rendszergazda lapra a Profil csoportban][51]

5. A tartományi megoldások lapon válassza a **Domains (Tartományok)** lehetőséget.

    ![Tartományi megoldások/tartományok][50]

6. A **Domains (Tartományok) csoportban** válassza a **CLAIM DOMAIN (ÁLLÍTÁSTARTOMÁNY)** lehetőséget.

    ![Jogcímtartomány beállítás][52]

7. A **Tartomány igénylése** párbeszédpanel Domain Name **(Tartománynév követelése)** mezőjébe írja be a vállalati tartományt, és válassza a **JOGCÍM**lehetőséget. Ellenőrizze a tartományt, és hogy az állapota aktív-e.

    ![Tartomány-/tartománynév-párbeszédpanel igénylése][53]

8. A tartományi megoldások lapon válassza az **Identitásszolgáltatók**lehetőséget.
  
    ![Identitásszolgáltatók beállítás][54]

9. Az **Identitásszolgáltatók** csoportban válassza az **IDENTITY PROVIDER HOZZÁADÁSA lehetőséget.**

    ![Identitásszolgáltató hozzáadása beállítás][55]

10. Az **Identitásszolgáltató beállításai** lapon hajtsa végre az alábbi lépéseket:

    ![Identitásszolgáltató beállításai mezők][56]

    a. A **Név** mezőbe írja be a konfiguráció egyedi nevét. Ne használjon szóközt.

    b. Az **Identitásszolgáltató kiállítója mezőbe**illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **Identitásszolgáltató bejelentkezési URL-címét** illessze be a **bejelentkezési URL-cím,** amelyet az Azure Portalról másolt.

    d. Az **Identitásszolgáltató kijelentkezési URL-címe** mezőbe illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.

    e. Válassza **az AuthN-kérelem aláírása lehetőséget.**

    f. Az **AuthN-kérelem küldése a**ban válassza a **POST**lehetőséget.

    g. A **Kijelentkezési kérelem küldése a**területen lehetőséget választja a **GET**gombra.

    h. Az **Egyéni attribútumleképezés** csoportban válassza **az ÚJ LEKÉPEZÉS HOZZÁADÁSA**lehetőséget.

       ![Egyéni attribútumleképezési felhasználói felület][62]

    i. Válassza ki az Azure AD-jogcímhez leképezni kívánt mezőt. Ebben a példában az **e-mailcím** jogcím `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`van leképezve a értéke. Ez az alapértelmezett jogcím neve az Azure AD-ből az e-mail jogcímhez. Válassza a **MENTÉS**lehetőséget.

       ![Egyéni attribútumleképezési mezők][57]

       > [!NOTE]
       > A megfelelő **felhasználói azonosító használatával** rendelje le a felhasználót az Azure AD-től a DocuSign felhasználói leképezéshez. Válassza ki a megfelelő mezőt, és adja meg a megfelelő értéket a szervezeti beállítások alapján.

    j. Az **Identitásszolgáltató tanúsítványainak csoportban** válassza a **TANÚSÍTVÁNY HOZZÁADÁSA**lehetőséget, töltse fel az Azure AD portálról letöltött tanúsítványt, és válassza a **MENTÉS**lehetőséget.

       ![Identitásszolgáltatói tanúsítványok/tanúsítvány hozzáadása][58]

    k. Az **Identitásszolgáltatók** csoportban válassza a **MŰVELETEK**lehetőséget, majd a **Végpontok**lehetőséget.

       ![Identitásszolgáltatók/végpontok][59]

    l. A DocuSign felügyeleti portál **SAML 2.0 végpontok** megtekintése szakaszában hajtsa végre az alábbi lépéseket:
       1. Másolja a **szolgáltató kiállítójának URL-címét,** majd illessze be az Azure Portal **Alapszintű SAML-konfiguráció** **szakaszának Azonosító** mezőjébe.

       1. Másolja a **szolgáltató bejelentkezési URL-címét,** majd illessze be az Azure Portal **Alapszintű SAML-konfiguráció szakaszbejelentkezési** **URL-címmezőjébe.**

       1. Kattintson a **Bezárás** gombra.

       ![SAML 2.0 végpontok megtekintése][60]

### <a name="create-docusign-test-user"></a>DocuSign-tesztfelhasználó létrehozása

In this section, a user named B.Simon is created in DocuSign. A DocuSign támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a DocuSign, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [DocuSign támogatási csapatához.](https://support.docusign.com/)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor a Hozzáférési panelen kiválasztja a DocuSign csempét, automatikusan be kell jelentkeznie a DocuSign-példányba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok a SaaS-alkalmazások Azure AD-vel való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure AD-ben?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure AD-ben?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a DocuSign szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

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