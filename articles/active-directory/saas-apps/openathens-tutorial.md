---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az OpenAthens programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OpenAthens között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73053213"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az OpenAthens programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OpenAthens-t az Azure Active Directoryval (Azure AD). Ha integrálja az OpenAthens-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az OpenAthens.Control in Azure AD who has access to OpenAthens.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve openathens az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* OpenAthens egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az OpenAthens támogatja az **IDP** által kezdeményezett SSO-t
* Az OpenAthens támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-openathens-from-the-gallery"></a>OpenAthens hozzáadása a galériából

Az OpenAthens azure AD-be való integrációjának konfigurálásához hozzá kell adnia az OpenAthens-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **OpenAthens** kifejezést a keresőmezőbe.
1. Válassza **az OpenAthens** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az OpenAthens számára

Konfigurálja és tesztelje az Azure AD SSO-t az OpenAthens segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó openathens.

Az Azure AD SSO openathens-rel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja openathens SSO](#configure-openathens-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[OpenAthens tesztfelhasználó](#create-openathens-test-user)** létrehozása – b.Simon megfelelője az OpenAthens-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **OpenAthens** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban töltse fel a **Szolgáltató metaadatfájlját,** amelynek lépéseit az oktatóanyag későbbi részében említi.

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![openathens feltöltésmetaadatok](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![Openathens böngészés feltöltési metaadatok](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **azonosító** értéke automatikusan kilesz töltve az **Egyszerű SAML konfiguráció szakasz** szövegmezőjében:

    ![OpenAthens Domain és URL-címek egyszeri bejelentkezési információk](common/idp-identifier.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **OpenAthens beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t openathens hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **OpenAthens**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-openathens-sso"></a>OpenAthens sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az OpenAthens vállalati webhelyére rendszergazdaként.

1. Válassza a **Kapcsolatok** elemet a **Kezelés** lap listájában.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Válassza **az SAML 1.1/2.0**lehetőséget, majd kattintson a **Beállítás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. A konfiguráció hozzáadásához kattintson a **Tallózás** gombra az Azure Portalról letöltött metaadat .xml fájl feltöltéséhez, majd kattintson a **Hozzáadás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Hajtsa végre az alábbi lépéseket a **Részletek** lapon.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. A **Névleképezés megjelenítése**csoportban válassza az **Attribútum használata**lehetőséget.

    b. A **Megjelenítendő név attribútum** mezőbe `http://schema.microsoft.com/identity/claims/displayname`írja be az értéket.

    c. Az **Egyedi felhasználómegfeleltetés**csoportban válassza **az Attribútum használata**lehetőséget.

    d. Az **Egyedi felhasználói attribútum** mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`be az értéket.

    e. Az **Állapot párbeszédpanelen**jelölje be mind a három jelölőnégyzetet.

    f. A **Helyi fiókok létrehozása** **párbeszédpanelen**válassza az Automatikus lehetőséget.

    g. Válassza a **Módosítások mentése** lehetőséget.

    h. A **</> Függő entitás** lapon másolja a **metaadat URL-címét,** és nyissa meg ezt a böngészőben az **SP metaadat-XML-fájl** letöltéséhez. Töltse fel ezt az SP-metaadat-fájlt az Azure AD **egyszerű SAML-konfigurációs** szakaszába.

    ![Egyszeri bejelentkezés konfigurálása](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>OpenAthens tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre openathens. OpenAthens támogatja **a just-in-time felhasználói kiépítés**, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az OpenAthens, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az OpenAthens csempére kattint, automatikusan be kell jelentkeznie az OpenAthens programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az OpenAthens szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)