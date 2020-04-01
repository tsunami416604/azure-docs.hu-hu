---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zscaler Private Access (ZPA) szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler Private Access (ZPA) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085735"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Oktatóanyag: A Zscaler Private Access (ZPA) integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler Private Access (ZPA) szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Zscaler Private Access (ZPA) szolgáltatást az Azure AD-vel, a következőket teheti:

* Az Azure AD- ben, aki hozzáfér a Zscaler Private Access (ZPA) hozzáféréssel.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve zscaler private access (ZPA) az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Zscaler Private Access (ZPA) egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Zscaler Private Access (ZPA) támogatja az **SP** által kezdeményezett sso-t.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler Private Access (ZPA) hozzáadása a galériából

A Zscaler Private Access (ZPA) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler Private Access (ZPA) szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Zscaler Private Access (ZPA)** kifejezést a keresőmezőbe.
1. Válassza a **Zscaler Private Access (ZPA)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler Private Access (ZPA) segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó zscaler private access (ZPA) között.

Az Azure AD SSO zscaler private access (ZPA) használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Konfigurálja a Zscaler Private Access (ZPA) beállítást](#configure-zscaler-private-access-zpa)** az sso-beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre Zscaler Private Access (ZPA) teszt felhasználó,](#create-zscaler-private-access-zpa-test-user)** hogy egy megfelelője Britta Simon zscaler private access (ZPA), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler Private Access (ZPA)** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > A **Bejelentkezés url-cím** értéke nem valós. Frissítse az értéket a tényleges Bejelentkezési URL-címmel. Lépjen kapcsolatba [a Zscaler Private Access (ZPA) ügyféltámogatási csapatával](https://help.zscaler.com/zpa-submit-ticket) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Zscaler Private Access (ZPA beállítása)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler private access (ZPA) konfigurálása

1. A Zscaler Private Access (ZPA) konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup Zscaler Private Access (ZPA)** irányítja, hogy a Zscaler Private Access (ZPA) alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Zscaler Private Access (ZPA) bejelentkezéséhez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler Private Access (ZPA) rendszert, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Zscaler Private Access (ZPA) vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. A menü bal oldalán kattintson a **Felügyelet** elemre, és keresse meg a **HITELESÍTÉS szakaszt** az **IdP-konfiguráció gombra.**

    ![Zscaler privát hozzáférés-rendszergazda felügyelete](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. A jobb felső sarokban kattintson az **IdP-konfiguráció hozzáadása gombra.** 

    ![Zscaler privát hozzáférési rendszergazdai idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Az **IdP-konfiguráció hozzáadása** lapon hajtsa végre a következő lépéseket:
 
    ![Zscaler private access rendszergazda kiválasztása](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kattintson **a Fájl kiválasztása** gombra a letöltött metaadat-fájl feltöltéséhez az Azure AD-ből az **IdP metaadat-fájl feltöltése** mezőben.

    b. Beolvassa az **IdP-metaadatokat** az Azure AD-ből, és feltölti az összes mezőadatait az alábbiak szerint.

    ![Zscaler Private Access Administrator konfiguráció](./media/zscalerprivateaccess-tutorial/config.png)

    c. Válassza ki a tartományt a **Domains** mezőből.
    
    d. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Zscaler Private Access (ZPA) engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Zscaler Private Access (ZPA) lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler Private Access (ZPA) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Zscaler Private Access (ZPA) alkalmazásban. Kérjük, működjön együtt [a Zscaler Private Access (ZPA) támogatási csapatával](https://help.zscaler.com/zpa-submit-ticket) a felhasználók zscaler private access (ZPA) platformon való hozzáadásához.

### <a name="test-sso"></a>SSO tesztelése

Ha a Hozzáférési panelen kiválasztja a Zscaler Private Access (ZPA) csempét, automatikusan be kell jelentkeznie a Zscaler Private Access (ZPA) rendszerbe, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)