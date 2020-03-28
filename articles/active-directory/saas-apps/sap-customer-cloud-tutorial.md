---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Cloud for Customer | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Cloud for Customer között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264061"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Cloud for Customer szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Cloud for Customer-t az Azure Active Directoryval (Azure AD). Ha integrálja az SAP Cloud for Customer alkalmazást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az SAP Cloud for Customer szolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve az SAP Cloud for Customer az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SAP Cloud for Customer egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az SAP Cloud for Customer támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP Cloud hozzáadása az ügyfélnek a galériából

Az SAP Cloud for Customer azure AD-be való integrációjának konfigurálásához hozzá kell adnia az SAP Cloud for Customer-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be az **SAP Cloud for Customer kifejezést** a keresőmezőbe.
1. Válassza ki **az SAP Cloud for Customer** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az SAP Cloud for Customer számára

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Cloud for Customer szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud for Customer-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az SAP Cloud for Customer szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az SAP Cloud for Customer SSO](#configure-sap-cloud-for-customer-sso)** -az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre SAP Cloud for Customer tesztfelhasználó](#create-sap-cloud-for-customer-test-user)** – a B.Simon megfelelője az SAP Cloud for Customer, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Cloud for Customer** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<server name>.crm.ondemand.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az SAP Cloud ügyfélügyfél-támogatási csapatával,](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az SAP Cloud for Customer alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A **Felhasználói attribútumok & jogcímek** párbeszédpanel **Felhasználói attribútumok** szakaszában hajtsa végre a következő lépéseket:

    a. Kattintson a **Szerkesztés ikonra** a **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Válassza **az Átalakítás** **forrásként**lehetőséget.

    c. Az **Átalakítás** listában válassza **a ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (ExtractMailPrefix() (Átalakítási**

    d. Az **1.**
    Ha például az EmployeeID azonosítót egyedi felhasználói azonosítóként szeretné használni, és az attribútumértékét az ExtensionAttribute2 attribútumban tárolta, válassza a user.extensionattribute2 lehetőséget.

    e. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **SAP Cloud beállítása az ügyfélhez** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t az SAP Cloud az ügyfél számára.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP Cloud for Customer**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sap-cloud-for-customer-sso"></a>Sap Cloud konfigurálása az ügyfél-sso-hoz

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az SAP Cloud for Customer vállalati webhelyére rendszergazdaként.

2. A menü bal oldalán kattintson az **Identitásszolgáltatók** > **vállalati identitásszolgáltatóinak** > **hozzáadása elemre,** és az előugró ablakban adja hozzá az Identitásszolgáltató nevét, például az **Azure AD-t,** kattintson a **Mentés** gombra, majd kattintson az **SAML 2.0 konfigurációgombra.**

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure01.png)

3. Az **SAML 2.0 konfigurációs** szakaszában hajtsa végre az alábbi lépéseket:

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Kattintson a **Tallózás** gombra az Összevonási metaadat-XML-fájl feltöltéséhez, amelyet az Azure Portalról töltött le.

    b. Az XML-fájl sikeres feltöltése után az alábbi értékek automatikusan automatikusan kitöltődnek, majd a **Mentés**gombra kattintanak.

### <a name="create-sap-cloud-for-customer-test-user"></a>Hozzon létre SAP Cloud az ügyfél számára tesztfelhasználó

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az SAP Cloud for Customer szolgáltatásba, ki kell építeni őket az SAP Cloud for Customer szolgáltatásba. Az SAP Cloud for Customer,kiépítése manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az SAP Cloud for Customer biztonsági rendszergazdaként.

2. A menü bal oldalán kattintson a **Felhasználók & engedélyezés** > **felhasználókezelés felhasználókezelés** > **hozzáadása elemre.**

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure03.png)

3. Az **Új felhasználó hozzáadása** csoportban hajtsa végre az alábbi lépéseket:

    ![SAP-konfiguráció](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Az **Utónév** mezőbe írja be a felhasználó nevét, például **B.**

    b. A **Vezetéknév** mezőbe írja be a felhasználó nevét, például **Simon**.

    c. Az **E-mail** mezőbe írja be a `B.Simon@contoso.com`felhasználó e-mail címét, például .

    d. A **Bejelentkezési név** mezőbe írja be a felhasználó nevét, például **B.Simon**.

    e. Válassza ki **a Felhasználó típusát** a követelménynek megfelelően.

    f. Válassza **a Fiókaktiválás** lehetőséget a követelménynek megfelelően.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az SAP Cloud for Customer csempére kattint, automatikusan be kell jelentkeznie az SAP Cloud for Customer szolgáltatásba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az SAP Cloud for Customer szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

