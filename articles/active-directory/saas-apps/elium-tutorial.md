---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Elium | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Elium között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791228"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Elium

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Elium a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az Elium-t az Azure AD-vel, a következőket teheti:

* A Elium-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Elium az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Elium egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Elium támogatja **az SP és a identitásszolgáltató** által KEZDEMÉNYEZett SSO
* A Elium **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-elium-from-the-gallery"></a>Elium hozzáadása a gyűjteményből

A Elium Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Elium a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Elium** kifejezést a keresőmezőbe.
1. Válassza ki a **Elium** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Elium

Konfigurálja és tesztelje az Azure AD SSO-t a Elium a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Elium-ben.

Az Azure AD SSO és a Elium konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[ELIUM SSO konfigurálása](#configure-elium-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre Elium-teszt felhasználót](#create-elium-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-Elium rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **Elium** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezek az értékek az **SP metaadat-fájlból** letölthetők a `https://<platform-domain>.elium.com/login/saml2/metadata`címen, amelyet az oktatóanyag későbbi részében talál.

1. A Elium alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentiek mellett a Elium alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Név | Forrás attribútum|
    | ---------------| ----------------|
    | e-mail   |User. mail |
    | first_name| User. givenName |
    | vezetéknév| felhasználó. vezetéknév|
    | job_title| User. beosztás|
    | vállalati| felhasználó. cégnév|

    > [!NOTE]
    > Ezek az alapértelmezett jogcímek. **Csak e-mail-jogcím szükséges**. A JIT-kiépítés esetében is csak az e-mail-jogcím megadása kötelező. Más egyéni jogcímek az egyik ügyfél-platformtól egy másik ügyfél-platformra változhatnak.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Elium beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Elium.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Elium**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-elium-sso"></a>Elium SSO konfigurálása

1. A Elium belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Elium beállítása** elemre, majd a Elium alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Elium való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Elium, nyisson meg egy új böngészőablakot, és jelentkezzen be a Elium vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Kattintson a **felhasználói profilra** a jobb felső sarokban, majd válassza az **Adminisztráció**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user1.png)

1. Válassza a **Biztonság** fület.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user2.png)

1. Görgessen le az **egyszeri bejelentkezés (SSO)** szakaszhoz, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user3.png)

    a. Másolja ki a **egy saml2-hitelesítés működésének ellenőrzését** , és illessze be a **bejelentkezési URL** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    > [!NOTE]
    > Az egyszeri bejelentkezés konfigurálása után mindig elérheti az alapértelmezett távoli bejelentkezési lapot a következő URL-címen: `https://<platform_domain>/login/regular/login` 

    b. Jelölje be az **egy saml2-összevonás engedélyezése** jelölőnégyzetet.

    c. Válassza az **JIT-kiépítés** jelölőnégyzetet.

    d. Nyissa meg az **SP-metaadatokat** a **Letöltés** gombra kattintva.

    e. Keresse meg a **entityID** az **SP metadata** fájlban, másolja a **entityID** értéket, és illessze be az **azonosító** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában. 

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user4.png)

    f. Keresse meg a **AssertionConsumerService** az **SP metadata** fájlban, másolja a **Location (hely** ) értéket, és illessze be a **válasz URL-címe** szövegmezőbe a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user5.png)

    g. Nyissa meg a letöltött metaadatokat Azure Portal a Jegyzettömbben, másolja a tartalmat, és illessze be a **identitásszolgáltató metadata** szövegmezőbe.

    h. Kattintson a **Save** (Mentés) gombra.

### <a name="create-elium-test-user"></a>Elium-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Elium-ben. A Elium támogatja az **igény szerinti üzembe**helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Elium-ben, akkor létrejön egy új, amikor megpróbál hozzáférni a Elium.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Elium támogatási csapatával](mailto:support@elium.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Elium csempére kattint, automatikusan be kell jelentkeznie arra a Elium, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Elium kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)