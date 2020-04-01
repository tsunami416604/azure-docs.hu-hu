---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Eliummal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Elium között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72791228"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Eliummal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Eliumot az Azure Active Directoryval (Azure AD). Ha integrálja az Eliumot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Eliumhoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Elium az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Elium egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Elium támogatja az **SP-t és az IDP** által kezdeményezett sso-t
* Az Elium támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-elium-from-the-gallery"></a>Elium hozzáadása a galériából

Az Elium Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Eliumot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **Elium** kifejezést a keresőmezőbe.
1. Válassza **az Elium** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az Elium számára

Konfigurálja és tesztelje az Azure AD SSO-t az Elium segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó Eliumban.

Az Azure AD SSO Elium mal való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Elium Egyszeri bejelentkezést](#configure-elium-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Elium teszt felhasználó](#create-elium-test-user)** - a B.Simon az Elium, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Elium** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<platform-domain>.elium.com/login/saml2/acs`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Ezeket az értékeket az **SP metaadatfájlból** kapja, amely letölthető a alkalmazásban, `https://<platform-domain>.elium.com/login/saml2/metadata`amelyet az oktatóanyag későbbi részében ismertetünk.

1. Elium alkalmazás elvárja az SAML-állítások egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az Elium alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | first_name| user.givenname |
    | last_name| user.vezetéknév|
    | job_title| user.jobtitle|
    | cég| user.companyname|

    > [!NOTE]
    > Ezek az alapértelmezett jogcímek. **Csak e-mail jogcím szükséges**. JIT kiépítés is csak e-mail jogcím kötelező. Az egyéb egyéni jogcímek ügyfélplatformonként eltérőek lehetnek.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Elium beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával az Elium hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Elium**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-elium-sso"></a>Elium SSO konfigurálása

1. Az Elium konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Elium** irányítja, hogy az Elium alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat az Eliumba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az Elium programot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az Elium vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a **felhasználói profilra** a jobb felső sarokban, majd válassza **az Adminisztráció**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user1.png)

1. Válassza a **Biztonság** fület.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user2.png)

1. Görgessen le az **Egyszeri bejelentkezés (SSO)** szakaszhoz, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user3.png)

    a. Ellenőrizze, hogy az **SAML2-hitelesítés működik-e a fiókjában,** és illessze be a **bejelentkezési URL-cím** beírt szövegmezőbe az Azure Portal **alapszintű SAML-konfiguráció** jain.

    > [!NOTE]
    > Az SSO konfigurálása után mindig elérheti az alapértelmezett távoli bejelentkezési lapot a következő URL-címen:`https://<platform_domain>/login/regular/login` 

    b. Jelölje **be az SAML2 összevonás engedélyezése** jelölőnégyzetet.

    c. Jelölje be **a JIT-kiépítés jelölőnégyzetet.**

    d. Nyissa meg az **SP metaadatait** a **Letöltés** gombra kattintva.

    e. Keresse meg az **entitásazonosítót** az **SP metaadatfájlban,** másolja az **entitásazonosító** értékét, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció** **szakaszának Azonosító** szövegmezőjébe. 

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user4.png)

    f. Keresse meg a **AssertionConsumerService** az **SP metaadatfájlban,** másolja a **hely** értékét, és illessze be a **Válasz URL-cím** szövegmezőbe az Azure Portal **alapszintű SAML-konfigurációja** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/elium-tutorial/user5.png)

    g. Nyissa meg a letöltött metaadatfájlt az Azure Portalról a jegyzettömbbe, másolja a tartalmat, és illessze be az **IdP metaadatok** szövegdobozába.

    h. Kattintson a **Mentés** gombra.

### <a name="create-elium-test-user"></a>Elium tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre elium. Az Elium támogatja **a just-in-time kiépítést,** amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik az Eliumban, akkor egy új jön létre, amikor megpróbál hozzáférni az Eliumhoz.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon [az Elium támogatási csapatához.](mailto:support@elium.com)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Elium csempére kattint, automatikusan be kell jelentkeznie arra az Eliumba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Elium ot az Azure AD-vel](https://aad.portal.azure.com/)