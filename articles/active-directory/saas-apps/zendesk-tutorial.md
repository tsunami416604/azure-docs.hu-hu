---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zendeskkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zendesk között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f70e7067a78b439d3dcaf0b83460296cad7b4485
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585805"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zendeskkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zendesket az Azure Active Directoryval (Azure AD). Ha integrálja a Zendesket az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zendeskhez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezzenek a Zendeskbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Zendesk egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Zendesk támogatja az **SP** által kezdeményezett SSO-t
* A Zendesk támogatja az [ **automatikus** felhasználói kiépítést](zendesk-provisioning-tutorial.md)
* A Zendesk konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-zendesk-from-the-gallery"></a>A Zendesk hozzáadása a galériából

A Zendesk Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Zendesket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Zendesk** kifejezést a keresőmezőbe.
1. Válaszd ki a **Zendesk** elemet az eredménypanelről, majd add hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Zendesk számára

Konfigurálja és tesztelje az Azure AD SSO-t a Zendesk-el egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a zendesk kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Zendesk segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Zendesk Egyszeri bejelentkezést](#configure-zendesk-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre Zendesk tesztfelhasználót](#create-zendesk-test-user)** – hogy b.Simon megfelelője legyen a Zendeskben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Zendesk** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.zendesk.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.zendesk.com`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel, az azonosítóval és a válasz URL-címmel. Lépjen kapcsolatba [a Zendesk ügyféltámogatási csapatával,](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A Zendesk alkalmazás az SAML-állításokat egy adott formátumban várja. Nincsenek kötelező SAML-attribútumok, de tetszés szerint az alkalmazásintegrációs lap **Felhasználói attribútumok** szakaszából kezelhető. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** gombra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > A bővítményattribútumok használatával olyan attribútumokat adhat hozzá, amelyek alapértelmezés szerint nem szerepelnek az Azure AD-ben. Kattintson az [SAML-ben beállítható Felhasználói attribútumok](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) elemre a **Zendesk** által által fogadott SAML-attribútumok teljes listájának levételéhez.

1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja a **hüvelykujj értékét,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

1. A **Zendesk beállítása** szakaszban másold le a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Zendesk hozzáférést biztosítva a Zendesk.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Zendesk**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-zendesk-sso"></a>A Zendesk SSO konfigurálása

1. Ha manuálisan szeretnéd beállítani a Zendesket, nyiss meg egy új böngészőablakot, és jelentkezz be rendszergazdaként a Zendesk vállalati webhelyére, és hajtsd végre a következő lépéseket:

2. Kattintson **a Rendszergazda gombra.**

3. A bal oldali navigációs ablakban kattintson a **Beállítások**gombra, majd a **Biztonság**parancsra.

4. A **Biztonság** lapon hajtsa végre az alábbi lépéseket:

    ![Biztonság](./media/zendesk-tutorial/ic773089.png "Biztonság")

    ![Egyszeri bejelentkezés](./media/zendesk-tutorial/ic773090.png "Egyszeri bejelentkezés")

    a. Kattintson a **Rendszergazda & ügyintézők** fülre.

    b. Válassza **az Egyszeri bejelentkezés (SSO) és az SAML**lehetőséget, majd az **SAML**lehetőséget.

    c. Az **SAML SSO URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. A **Távoli kijelentkezés URL-cím** mezőjébe illessze be a **Kijelentkezés URL-címének** az Azure Portalról másolt értékét.

    e. A **Tanúsítványujjlenyomat** szövegmezőbe illessze be az Azure Portalról másolt tanúsítvány **ujjlenyomat-értékét.**

    f. Kattintson a **Mentés** gombra.

### <a name="create-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy felhasználó nevű Britta Simon a Zendesk. A Zendesk támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](Zendesk-provisioning-tutorial.md) kiépítés konfigurálásáról itt olvashat bővebben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

> [!NOTE]
> **A végfelhasználói** fiókok automatikusan kivannak építve bejelentkezéskor. **Az ügynöki** és **rendszergazdai** fiókokat bejelentkezés előtt manuálisan kell kiépíteni a **Zendeskben.**

1. Jelentkezzen be a **Zendesk-bérlőbe.**

2. Válassza a **Vevőlista** lapot.

3. Kattintson a **Felhasználó** fülre, majd a **Hozzáadás**gombra.

    ![Felhasználói csoportok](./media/zendesk-tutorial/ic773632.png "Felhasználó hozzáadása")
4. Írja be a kiépíteni kívánt meglévő Azure AD-fiók **nevét** és **e-mail címét,** majd kattintson a **Mentés gombra.**

    ![Új felhasználó](./media/zendesk-tutorial/ic773633.png "Új felhasználó")

> [!NOTE]
> A Zendesk által biztosított bármely más Zendesk felhasználói fiók-létrehozási eszközt vagy API-t használhatja az Azure AD felhasználói fiókok kiépítéséhez.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelzenére kattintasz a Zendesk csempére, automatikusan be kell jelentkezned arra a Zendeskbe, amelyhez az SSO-t beállítottad. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Zendesket az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védheti meg a Zendesket a fejlett láthatósággal és vezérléssel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Felhasználói kiépítés konfigurálása](zendesk-provisioning-tutorial.md)