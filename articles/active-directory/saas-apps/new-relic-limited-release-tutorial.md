---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az új ereklye között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az új ereklye szolgáltatással

Ez az oktatóanyag azt ismerteti, hogyan integrálható az új ereklye Azure Active Directory (Azure AD) használatával. Az új ereklye Azure AD-val való integrálásával a következőket teheti:

* Az Azure AD-ben való hozzáférés az új ereklye eléréséhez.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az új Ereklyebe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Új ereklye egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az új ereklye **az SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést támogatja.
* Az új ereklye konfigurálását követően kényszerítheti a munkamenet-vezérlést, amely védelmet biztosít a szervezete bizalmas adatai kiszűrése és a valós időben történő beszivárgásával szemben. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Új ereklye alkalmazás hozzáadása a katalógusból

Az új ereklye Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az **új ereklye (szervezet szerint)** elemet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. Válassza ki a **Azure Active Directory** szolgáltatást.
1. Válassza a **vállalati alkalmazások** lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás** lehetőséget.
1. Az **Azure ad** -katalógus tallózása lapon írja be a keresőmezőbe az **új ereklye (szervezet szerint)** kifejezést.
1. Válassza az **új ereklye (szervezet szerint)** lehetőséget az eredmények panelen, majd válassza a **Létrehozás** lehetőséget. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Az Azure AD SSO konfigurálása és tesztelése az új ereklye számára

Konfigurálja és tesztelje az Azure AD SSO-t az új ereklye használatával egy **B. Simon** nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az új Ereklyeben.

Az Azure AD SSO új ereklye használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
   1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
   1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Új EREKLYE SSO konfigurálása](#configure-new-relic-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az új ereklye oldalon.
   1. **[Hozzon létre egy új ereklye tesztet használó felhasználót](#create-a-new-relic-test-user)** – hogy az Azure ad-felhasználóhoz kapcsolódó új ereklyeben a B. Simon-nek megfelelő jogosultsággal rendelkezzen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **új ereklye a szervezeti** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés** lehetőséget.

1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget.

1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg az **azonosító** és a **Válasz URL-cím** értékeit.

   * Ezek az értékek az új ereklye **saját szervezet** alkalmazás használatával kérhetők le. Az alkalmazás használatához hajtsa végre a következő lépéseket:
      1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.
      1. A felső menüben válassza az **alkalmazások** lehetőséget.
      1. A **saját alkalmazások** szakaszban válassza a **saját szervezet** lehetőséget.
      1. Kattintson a **hitelesítési tartományok** elemre.
      1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t szeretné kapcsolni (ha több hitelesítési tartománnyal rendelkezik). A legtöbb vállalatnak csak egy **alapértelmezett** nevű hitelesítési tartománya van. Csak egy hitelesítési tartománnyal nem kell kiválasztania.
      1. A **hitelesítés** szakaszban az **állítás fogyasztói URL-cím** tartalmazza a **Válasz URL-címéhez** használandó értéket.
      1. A **hitelesítés** szakaszban a **saját entitás azonosítója** tartalmazza az **azonosítóhoz** használandó értéket.

1. A **felhasználói attribútumok & jogcímek** szakaszban győződjön meg arról, hogy az **egyedi felhasználói azonosító** egy olyan mezőre van leképezve, amely tartalmazza az új ereklye által használt e-mail-címet.

   * Az alapértelmezett mező **User. userPrincipalName** fog működni, ha az értékek ugyanazok, mint az új ereklye e-mail-címei.
   * Ha a **User. userPrincipalName** nem az új ereklye e-mail-címe, akkor a mező **felhasználója** jobban működhet.

1. Az **SAML aláíró tanúsítvány** szakaszban másolja az **alkalmazás-összevonási metaadatok URL-címét** , és mentse az értékét későbbi használatra.

1. Az **új ereklye beállítása szervezet szerint** szakaszban másolja a **bejelentkezési URL-címet** , és mentse az értékét későbbi használatra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal válassza a **Azure Active Directory** szolgáltatás elemet.
1. Válassza a **Felhasználók** lehetőséget.
1. Új felhasználó hozzáadásához válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. Az **új felhasználó** lapon kövesse az alábbi lépéseket:
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `b.simon@contoso.com`. Ennek egyeznie kell azzal az e-mail címmel, amelyet az új ereklye oldalon fog használni.
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd mentse a **kezdeti jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure AD egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít az **új ereklye** számára a szervezeti alkalmazásban.

1. A Azure Portal válassza a **Azure Active Directory** szolgáltatás elemet.
1. Válassza a **vállalati alkalmazások** lehetőséget.
1. Az alkalmazások listában válassza az **új ereklye szervezet szerint** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok** lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és a csoportok** (vagy a **felhasználók** a csomag szintjétől függően) lehetőséget.

   ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** (vagy **felhasználók**) párbeszédpanelen válassza ki a felhasználók listából a **B. Simon** elemet, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-new-relic-sso"></a>Új ereklye SSO konfigurálása

A következő lépésekkel konfigurálhatja az egyszeri bejelentkezést az új ereklye szolgáltatásban.

1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.

1. A felső menüben válassza az **alkalmazások** lehetőséget.

1. A **saját alkalmazások** szakaszban válassza a **saját szervezet** lehetőséget.

1. Kattintson a **hitelesítési tartományok** elemre.

1. Válassza ki azt a hitelesítési tartományt, amelyhez az Azure AD SSO-t szeretné kapcsolni (ha több hitelesítési tartománnyal rendelkezik). A legtöbb vállalatnak csak egy **alapértelmezett** nevű hitelesítési tartománya van. Csak egy hitelesítési tartománnyal nem kell kiválasztania.

1. A **hitelesítés** szakaszban kattintson a **Konfigurálás** elemre.

   1. Az **SAML-metaadatok forrása** mezőben adja meg azt az értéket, amelyet korábban az Azure ad oldal **alkalmazás-összevonási metaadatok URL-címe** mezőből mentett.

   1. Az **SSO-cél URL-címe** mezőben adja meg az Azure ad-oldal **bejelentkezési URL-címe** mezőben korábban mentett értéket.

   1. Kattintson a **Mentés** gombra a Beállítások ellenőrzése után az Azure ad-ben és az új ereklye oldalon is. Ha a két oldal nem megfelelően van konfigurálva, a felhasználók nem fognak tudni új Ereklyebe bejelentkezni.

### <a name="create-a-new-relic-test-user"></a>Új ereklye teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az új Ereklyeben. A felhasználó létrehozásához kövesse az alábbi lépéseket.

1. [Jelentkezzen](https://login.newrelic.com/) be az új ereklyebe.

1. A felső menüben válassza az **alkalmazások** lehetőséget.

1. Az **alkalmazások** szakaszban válassza a **felhasználói kezelés** lehetőséget.

1. Kattintson a **felhasználó hozzáadása** gombra.

   1. A név mezőbe írja be a **B. Simon** **nevet** .
   
   1. Az **e-mail** mezőben adja meg az Azure ad SSO által küldendő értéket.
   
   1. Válasszon egy felhasználói **típust** és egy felhasználói **csoportot** a felhasználó számára. Tesztelési felhasználó esetén az **alapszintű felhasználó** a csoport típusa és **felhasználója** számára ésszerű választás.
   
   1. A felhasználó mentéséhez kattintson a **felhasználó hozzáadása** elemre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az **új ereklye a szervezet csempe alapján** lehetőségre kattint, automatikusan be kell jelentkeznie az új ereklye szolgáltatásba. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](./tutorial-list.md)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [Új ereklye kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)
