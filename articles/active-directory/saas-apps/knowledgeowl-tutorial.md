---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a KnowledgeOwl | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és KnowledgeOwl között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e3931baa8bab6f21d0edfefd17b95f7ecf3f7be
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85799819"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a KnowledgeOwl

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a KnowledgeOwl a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az KnowledgeOwl-t az Azure AD-vel, a következőket teheti:

* A KnowledgeOwl-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a KnowledgeOwl az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* KnowledgeOwl egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A KnowledgeOwl támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A KnowledgeOwl **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-knowledgeowl-from-the-gallery"></a>KnowledgeOwl hozzáadása a gyűjteményből

A KnowledgeOwl Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a KnowledgeOwl a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **KnowledgeOwl** kifejezést a keresőmezőbe.
1. Válassza ki a **KnowledgeOwl** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a KnowledgeOwl

Konfigurálja és tesztelje az Azure AD SSO-t a KnowledgeOwl a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a KnowledgeOwl-ben.

Az Azure AD SSO és a KnowledgeOwl konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[KNOWLEDGEOWL SSO konfigurálása](#configure-knowledgeowl-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre KnowledgeOwl-teszt felhasználót](#create-knowledgeowl-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-KnowledgeOwl rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **KnowledgeOwl** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Ezek az értékek nem valósak. A tényleges azonosító, a válasz URL-cím és a bejelentkezési URL-cím alapján frissítenie kell ezeket az értékeket, amelyeket az oktatóanyag későbbi részében ismertetünk.

1. A KnowledgeOwl alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a KnowledgeOwl alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum | Névtér |
    | ------------ | -------------------- | -----|
    | ssoid | User. mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (RAW)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **KnowledgeOwl beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a KnowledgeOwl.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **KnowledgeOwl**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a KnowledgeOwl vállalati webhelyre rendszergazdaként.

1. Kattintson a **Beállítások** , majd a **Biztonság**elemre.

    ![KnowledgeOwl-konfiguráció](./media/knowledgeowl-tutorial/configure1.png)

1. Görgessen az **SAML SSO-integrációhoz** , és hajtsa végre a következő lépéseket:

    ![KnowledgeOwl-konfiguráció](./media/knowledgeowl-tutorial/configure2.png)

    a. Válassza az **SAML SSO engedélyezése**lehetőséget.

    b. Másolja az **SP-entitás azonosítójának** értékét, és illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszának **azonosító (entitás azonosítója)** részébe.

    c. Másolja az **SP bejelentkezési URL** -címét, és illessze be a **bejelentkezési URL-cím és a válasz URL-címek** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    d. A **identitásszolgáltató entityID** szövegmezőbe illessze be az **Azure ad-azonosító** értékét, amelyet a Azure Portal másolt.

    e. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-címet** , amelyet a Azure Portal másolt.

    f. A **identitásszolgáltató kijelentkezési URL-címe** szövegmezőbe illessze be a **KIjelentkezési URL** -értéket, amelyet a Azure Portal másolt

    : Töltse fel a letöltött tanúsítványt a Azure Portal a **identitásszolgáltató feltöltése tanúsítványra**kattintva.

    h. Kattintson a **Térkép SAML-attribútumok** elemre az attribútumok leképezéséhez, és hajtsa végre a következő lépéseket:

    ![KnowledgeOwl-konfiguráció](./media/knowledgeowl-tutorial/configure3.png)

    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` az **SSO-azonosító** szövegmezőt
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` a **Felhasználónév/e-mail** szövegmezőt.
    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`Az utónév szövegmezőbe írja be a **nevet** .
    * Adja meg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` az **utolsó név** szövegmezőt.
    * Kattintson a **Mentés** gombra

    i. Kattintson a lap alján található **Mentés** gombra.

    ![KnowledgeOwl-konfiguráció](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a KnowledgeOwl-ben. A KnowledgeOwl támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a KnowledgeOwl-ben, a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [KnowledgeOwl támogatási csapatával](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a KnowledgeOwl csempére kattint, automatikusan be kell jelentkeznie arra a KnowledgeOwl, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A KnowledgeOwl kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)