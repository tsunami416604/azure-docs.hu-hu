---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Freshworks-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Freshworks között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 697c3509-0e3a-4b05-9d5b-9ded4269eb60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b73b8769b5f7360ba4ad778600b1000b8b3f7bc7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72376412"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Freshworks-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Freshworks szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Freshworks-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Freshworks.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Freshworks az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Freshworks egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Freshworks támogatja **SP** kezdeményezett SSO

## <a name="adding-freshworks-from-the-gallery"></a>Freshworks hozzáadása a galériából

A Freshworks azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshworks-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Freshworks** kifejezést a keresőmezőbe.
1. Válassza **a Freshworks** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshworks"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Freshworks számára

Konfigurálja és tesztelje az Azure AD SSO-t a Freshworks segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a freshworks kapcsolódó felhasználója között.

Az Azure AD SSO freshworks-sel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a Freshworks Egyszeri bejelentkezést](#configure-freshworks-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Freshworks tesztfelhasználó létrehozása](#create-freshworks-test-user)** – b.Simon megfelelője a Freshworksben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Freshworks** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.freshworks.com/login`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Freshworks ügyféltámogatási csapatával,](mailto:support@freshworks.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Ha a követelménynek megfelelően szeretné módosítani az **aláírási** beállításokat, kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

     ![image](common/edit-certificate.png)

     ![Freshworks konfiguráció](./media/freshworks-tutorial/response.png)

    a. Válassza **az SAML aláírása válasz** **aláírási lehetőségként**lehetőséget.

    b. Kattintson a **Mentés** gombra.

1. A **Freshworks beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Freshworks.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Freshworks**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-freshworks-sso"></a>Freshworks SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a Freshworks vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

2. A menü bal oldalán kattintson a **Biztonság** ikonra, majd ellenőrizze az **Egyszeri bejelentkezés** lehetőséget, és válassza az **SAML Egyszeri bejelentkezés** lehetőséget a **Hitelesítési módszerek**csoportban.

    ![Freshworks konfiguráció](./media/freshworks-tutorial/configure01.png)

3. Az **Egyszeri bejelentkezés szakaszban** hajtsa végre a következő lépéseket:

    ![Freshworks konfiguráció](./media/freshworks-tutorial/configure02.png)

    a. Kattintson a **Másolás** gombra a **szolgáltató(SP) entitásazonosító** jának másolásához a példányhoz, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció szakaszában az** **Azonosító (entitásazonosító)** mezőbe.

    b. Az **IdP-szövegmező által biztosított entitásazonosítóban** illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **SAML SSO URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    d. Nyissa meg a Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát, és illessze be a **Biztonsági tanúsítvány** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-freshworks-test-user"></a>Freshworks tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót a Freshworks alkalmazásban. Együttműködve [Freshworks ügyfél támogatási csapat](mailto:support@freshworks.com) a felhasználók hozzáadása a Freshworks platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel Freshworks csempéjére kattint, automatikusan be kell jelentkeznie a Freshworks programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Freshworks szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

