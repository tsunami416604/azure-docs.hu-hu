---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Sales Navigator-ral | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a LinkedIn Sales Navigator között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d8293b23559860e70191576db13c3cd14f520e6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a LinkedIn Sales Navigator-ral

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a LinkedIn Sales Navigatort az Azure Active Directoryval (Azure AD). Ha integrálja a LinkedIn Sales Navigatort az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a LinkedIn Sales Navigator.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a LinkedIn Sales Navigator az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* LinkedIn Sales Navigator egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A LinkedIn Sales Navigator támogatja az **SP és az IDP** által kezdeményezett sso-t
* A LinkedIn Sales Navigator támogatja **a Just In Time** felhasználói kiépítést
* A LinkedIn Sales Navigator támogatja az [ **automatikus** felhasználói kiépítést](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn Sales Navigator hozzáadása a gyűjteményből

A LinkedIn Sales Navigator azure-ad-ba való integrációjának konfigurálásához hozzá kell adnia a LinkedIn Sales Navigator-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény hozzáadás szakaszába** írja be a **LinkedIn Sales Navigator** kifejezést a keresőmezőbe.
1. Válassza a **LinkedIn Sales Navigator** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a LinkedIn Sales Navigator számára

Konfigurálja és tesztelje az Azure AD SSO-t a LinkedIn Sales Navigator segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a LinkedIn Sales Navigator-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a LinkedIn Sales Navigator segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a LinkedIn Sales Navigator SSO-t](#configure-linkedin-sales-navigator-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[LinkedIn Sales Navigator tesztfelhasználó](#create-linkedin-sales-navigator-test-user)** létrehozása – a LinkedIn Sales Navigator B.Simon megfelelője, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **LinkedIn Sales Navigator alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be az **entitásazonosító** értékét, és az oktatóanyag későbbi részében ismertetett Linkedin-portál entitásazonosító-értékét másolja.

    b. A **Válasz URL-cím** mezőjébe írja be a **helyességi feltétel fogyasztói hozzáférési (ACS) url-címét,** és az oktatóanyag későbbi részében ismertetett Linkedin-portálurl-címét másolja a Helyességi feltétel fogyasztói hozzáférési (ACS) URL-címének értékére.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. A LinkedIn Sales Navigator alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a LinkedIn Sales Navigator alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | --- | --- |
    | e-mail| user.mail |
    | Részleg| user.department (felhasználó.részleg) |
    | Utónév| user.givenname |
    | Vezetéknév| user.vezetéknév |
    | Egyedi felhasználói azonosító | user.mail |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **LinkedIn Sales Navigator beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a LinkedIn Sales Navigator hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **LinkedIn Sales Navigator**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-linkedin-sales-navigator-sso"></a>LinkedIn Sales Navigator SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **LinkedIn Sales Navigator** webhelyére rendszergazdaként.

1. A **Fiókközpontban**kattintson a **Globális beállítások** elemre a Beállítások **csoportban.** A legördülő listából válassza a **Sales Navigator** lehetőséget is.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kattintson **a VAGY ide gombra az egyes mezők betöltéséhez és másolásához az űrlapról,** és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Entitásazonosító **másolása** és **beillesztése** az Azure Portal **alapszintű SAML-konfigurációjának** azonosító szövegmezőjébe.

    b. Másolja **az egycímfelhasználói hozzáférési (ACS) URL-címét,** és illessze be a **Válasz URL-cím** mezőjébe az Azure Portal **alapszintű SAML-konfigurációjában.**

1. Nyissa meg a **LinkedIn felügyeleti beállítások szakaszát.** Töltse fel az Azure Portalról letöltött XML-fájlt az **XML-fájl feltöltése** beállításra kattintva.

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kattintson **a Be gombra** az SSO engedélyezéséhez. Az sso állapota a **nincs csatlakoztatva** állapotról **a csatlakoztatottra változik**

    ![Egyszeri bejelentkezés konfigurálása](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator tesztfelhasználó létrehozása

A Csatolt értékesítési navigátor alkalmazás támogatja a Just in Time (JIT) felhasználói kiépítést, és a hitelesítés után a felhasználók automatikusan létrejönnek az alkalmazásban. Aktiválás **Automatikus licenchozzárendelésa** a felhasználóhoz való licenc hozzárendeléséhez.

   ![Azure AD-tesztfelhasználó létrehozása](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a LinkedIn Sales Navigator csempére kattint, automatikusan be kell jelentkeznie a LinkedIn Sales Navigator-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a LinkedIn Sales Navigator-t az Azure AD-vel](https://aad.portal.azure.com/)