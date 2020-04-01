---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a NetSuite programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a NetSuite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760052"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Oktatóanyag: Az Azure AD egyszeri bejelentkezésének (SSO) integrálása a NetSuite programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a NetSuite szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a NetSuite szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a NetSuite szolgáltatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkeznek a NetSuite-be az Azure AD-fiókjukkal.
* Egyetlen központi helyen, az Azure Portalon kezelheti fiókjait.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* NetSuite egyszeri bejelentkezést (SSO) engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 

A NetSuite támogatja:

* IDP által kezdeményezett egyszeri szolgáltató.
* JIT (just-in-time) felhasználói kiépítés.
* [Automatikus felhasználói kiépítés.](NetSuite-provisioning-tutorial.md)
* A NetSuite konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Mivel az alkalmazás azonosítója rögzített karakterlánc-érték, csak egy példány konfigurálható egy bérlőben.

## <a name="add-netsuite-from-the-gallery"></a>NetSuite hozzáadása a galériából

A NetSuite azure AD-be való integrációjának konfigurálásához adja hozzá a NetSuite-et a galériából a felügyelt SaaS-alkalmazások listájához az alábbi módon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblában válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **NetSuite** kifejezést a keresőmezőbe.
1. Az eredmények ablaktáblán válassza a **NetSuite**lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a NetSuite szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a NetSuite szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a NetSuite kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a NetSuite szolgáltatással hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso) hogy a felhasználók használhassák ezt a funkciót.
    * [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon felhasználóval.  
    * [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy b.Simon felhasználó használhassa az Azure AD egyszeri bejelentkezést.
1. [Konfigurálja a NetSuite Egyszeri bejelentkezési](#configure-netsuite-sso) beállításokat az alkalmazás oldalon.
    * [Hozza létre a NetSuite tesztfelhasználó,](#create-the-netsuite-test-user) hogy egy megfelelője a felhasználó B.Simon a NetSuite, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
1. [Tesztelje az SSO-t,](#test-sso) hogy ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD-sSO engedélyezéséhez az Azure Portalon tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com/)a **NetSuite** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza az **Egyszerű SAML-konfiguráció**melletti **Szerkesztés** ("ceruza") ikont.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** csoport **Válasz URL-cím** mezőjébe írjon be egy URL-címet az alábbi formátumok egyikében:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * Az értéket ** < `Account ID` ** a Netsuite konfigurációs részében kapja meg, amelyet később a 8. Meg fogja találni a pontos domain (mint például a system.na0.netsuite.com ebben az esetben).

        ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Az előző URL-címek értékei nem valósak. Frissítse őket a tényleges Válasz URL-címmel. Az érték lefelvételéhez lépjen kapcsolatba a [NetSuite ügyféltámogatási csapatával.](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható formátumokat is megjelenítheti.

1. A NetSuite alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. Amellett, hogy a fenti, NetSuite alkalmazás elvárja, hogy néhány további attribútumokat kell átadni vissza SAML választ, amelyek az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútum |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > A számlaattribútum értéke nem valós. Frissíteni fogja ezt az értéket, amint azt az oktatóanyag későbbi részében ismertetjük.

1. Az Egyszeri bejelentkezés beállítása SAML-lel lapon az SAML aláíró tanúsítvány szakaszban keresse meg az összevonási metaadatok XML-jét, és válassza a Letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **NetSuite beállítása** szakaszban másolja a megfelelő URL-t vagy URL-eket, a követelménytől függően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

1. A **Felhasználó** tulajdonságai ablaktáblában hajtsa végre az alábbi lépéseket:

   a. A **Név** mezőbe írja be a **B.Simon**értéket.  
   b. A **Felhasználónév** mezőbe írja username@companydomain.extension be a B.Simon@contoso.com(például) értéket.  
   c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.  
   d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi a B.Simon felhasználó számára az Azure egyszeri bejelentkezést a NetSuite-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **NetSuite**lehetőséget.
1. Az áttekintő ablaktáblán keresse meg a **Kezelés szakaszt,** majd válassza a **Felhasználók és csoportok** hivatkozást.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása lehetőséget,** majd a Hozzárendelés **hozzáadása** ablaktáblán válassza a Felhasználók **és csoportok**lehetőséget.

    ![A "Felhasználó hozzáadása" gomb](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán a **Felhasználók** legördülő listában válassza a **B.Simon**elemet, majd a képernyő alján található **Kijelölés** gombot.
1. Ha az SAML-feltételben szerepkörértéket vár, tegye a következőket:

   a. A **Szerepkör kiválasztása** ablaktáblában a legördülő listában válassza ki a felhasználó számára megfelelő szerepkört.  
   b. A képernyő alján válassza a **Kijelölés** gombot.
1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés** gombot.

## <a name="configure-netsuite-sso"></a>NetSuite sso konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a NetSuite vállalati webhelyére rendszergazdaként.

2. A felső navigációs sávon válassza a **Telepítő**lehetőséget, majd a **Vállalati** > **szolgáltatások engedélyezése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setupsaml.png)

3. A lap közepén lévő eszköztáron válassza a **SuiteCloud**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-suitecloud.png)

4. A **Hitelesítés kezelése csoportban**jelölje be az **SAML Single Sign-on** jelölőnégyzetet, ha engedélyezni szeretné az SAML egyszeri bejelentkezési beállítást a NetSuite alkalmazásban.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-ticksaml.png)

5. A felső navigációs sávon válassza a **Telepítő**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

6. A **Telepítési feladatok** listában válassza az **Integráció**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-integration.png)

7. A **Hitelesítés kezelése csoportban**válassza **az SAML Single Sign-on**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml.png)

8. Az **SAML telepítő** ablaktáblájának **NetSuite-konfiguráció területén**tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Jelölje be az **Elsődleges hitelesítési módszer** jelölőnégyzetet.

    b. Az **SAMLV2 identitásszolgáltató metaadatai**csoportban válassza **az IDP-metaadat-fájl feltöltése**lehetőséget, majd a **Tallózás** gombra a metaadat-fájl Azure Portalról letöltött feltöltéséhez.

    c. Válassza a **Küldés** lehetőséget.

9. A NetSuite felső navigációs sávján válassza a **Telepítő**lehetőséget, majd a **Vállalati** > **vállalati adatok**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-com.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-account-id.png)

    b. A **Vállalati információ** ablaktábla jobb oldali oszlopában másolja a **számlaazonosító** értékét.

    c. Illessze be a NetSuite-fiókból másolt **fiókazonosítót** az Azure AD **Attribútumérték** mezőjébe.

10. Mielőtt a felhasználók egyszeri bejelentkezést végezhetnének a NetSuite szolgáltatásba, először hozzá kell rendelniük a megfelelő engedélyekhez a NetSuite alkalmazásban. Az engedélyek hozzárendeléséhez tegye a következőket:

    a. A felső navigációs sávon válassza a **Telepítő**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    b. A bal oldali ablaktáblában válassza a **Felhasználók/szerepkörök**lehetőséget, majd a **Szerepkörök kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Válassza az **Új szerepkör lehetőséget.**

    d. Adja meg az új szerepkör **nevét.**

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Mentés** gombra.

    f. A felső navigációs sávon válassza az **Engedélyek**lehetőséget. Ezután válassza **a Telepítő**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-sso.png)

    g. Válassza **az SAML Single Sign-on**lehetőséget, majd a **Hozzáadás**lehetőséget.

    h. Kattintson a **Mentés** gombra.

    i. A felső navigációs sávon válassza a **Telepítés**lehetőséget, majd a **Telepítéskezelő**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    j. A bal oldali ablaktáblában válassza a **Felhasználók/szerepkörök**lehetőséget, majd a **Felhasználók kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Jelöljön ki egy tesztfelhasználót, válassza **a Szerkesztés**lehetőséget, majd az **Access** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A **Szerepkörök** ablaktáblán rendelje hozzá a létrehozott megfelelő szerepkört.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Mentés** gombra.

### <a name="create-the-netsuite-test-user"></a>A NetSuite tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a NetSuite alkalmazásban. A NetSuite támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a NetSuite, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor a Hozzáférési panelen kiválasztja a NetSuite csempét, automatikusan be kell jelentkeznie arra a NetSuite-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Próbálja ki a NetSuite szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)
- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védhetnet a NetSuite fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)