---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Repliconnal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Replicon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092768"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Oktatóanyag: A Replicon integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Replicont az Azure Active Directoryval (Azure AD). Ha integrálja a Replicont az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Repliconhoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve replicon az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Replicon egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Replicon támogatja **sp** kezdeményezett SSO.

## <a name="adding-replicon-from-the-gallery"></a>Replicon hozzáadása a galériából

A Replicon azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Replicont a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Replicon** kifejezést a keresőmezőbe.
1. Válassza az **Újraplicon** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Replicon nal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a repliconi kapcsolódó felhasználó között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Replicon segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Replicon Egyszeri bejelentkezést](#configure-replicon-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Replicon teszt felhasználó](#create-replicon-test-user)** - egy megfelelője B.Simon a Replicon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Replicon-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://global.replicon.com/!/saml2/<client name>`

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a Replicon ügyféltámogatási csapatával](https://www.replicon.com/customerzone/contact-support) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A beállítások szerkesztéséhez kattintson az **SAML aláíró tanúsítvány** szerkesztési/tollikonjára.

    ![Aláírási algoritmus](common/signing-algorithm.png)

    1. Jelölje be **az SAML-hozzárendelés aláírása** **aláírási lehetőségként jelölőnégyzetet.**

    1. Válassza az **SHA-256** **lehetőséget aláíró algoritmusként.**

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Replicon vállalati webhelyére rendszergazdaként.

2. Az SAML 2.0 konfigurálásához hajtsa végre az alábbi lépéseket:

    ![SAML-hitelesítés engedélyezése](./media/replicon-tutorial/ic777805.png "SAML-hitelesítés engedélyezése")

    a. Az **EnableSAML Authentication2** párbeszédpanel megjelenítéséhez fűzze hozzá az alábbiakat az URL-címhez a vállalati kulcs után:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * A következőkben a teljes URL-cím sémája látható:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kattintson **+** a gombra a **v20Configuration** szakasz kibontásához.

   c. Kattintson **+** a **gombra a metaDataConfiguration** szakasz kibontásához.

   d. Válassza az **SHA256** elemet az xmlSignatureAlgorithm

   e. Kattintson **a Fájl kiválasztása**gombra az identitásszolgáltató metaadat-XML-fájljának kiválasztásához, majd a **Küldés**gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Replicon hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Replicon**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-replicon-test-user"></a>Replicon-tesztfelhasználó létrehozása

Ennek a szakasznak a célja egy B.Simon nevű felhasználó létrehozása a Repliconban.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

1. A webböngésző ablakában jelentkezzen be a Replicon vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Felügyeleti \> felhasználók lehetőséget.**

    ![Felhasználók](./media/replicon-tutorial/ic777806.png "Felhasználók")

3. Kattintson **a +Felhasználó hozzáadása gombra.**

    ![Felhasználó hozzáadása](./media/replicon-tutorial/ic777807.png "Felhasználó hozzáadása")

4. A **Felhasználói profil szakaszban** hajtsa végre az alábbi lépéseket:

    ![Felhasználói profil](./media/replicon-tutorial/ic777808.png "Felhasználói profil")

    a. A **Bejelentkezési név** mezőbe írja be az Azure AD-felhasználó Azure AD-s e-mail címét, például. `B.Simon@contoso.com`

    > [!NOTE]
    > A bejelentkezési névnek meg kell egyeznie a felhasználó e-mail címével az Azure AD-ben

    b. **Hitelesítési típusként**válassza **az SSO**lehetőséget.

    c. Állítsa a hitelesítési azonosítót ugyanarra az értékre, mint a bejelentkezési név (A felhasználó Azure AD e-mail címe)

    d. A **Részleg** mezőbe írja be a felhasználó részlegét.

    e. **Alkalmazotttípusként**válassza **a Rendszergazda**lehetőséget.

    f. Kattintson **a Felhasználói profil mentése gombra.**

> [!NOTE]
> A Replicon felhasználói fiókok létrehozásához használt bármely más, a Replicon által biztosított api-k at használhat az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen a Replicon csempét választja, automatikusan be kell jelentkeznie arra a Repliconba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)