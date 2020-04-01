---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Értékeléssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Appraisd között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67561204"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Oktatóanyag: Az Értékelések integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Appraisd-t az Azure Active Directoryval (Azure AD). Ha integrálja az Appraisd-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az appraisd.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve az Appraisd az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egyszeri bejelentkezés (SSO) engedélyezett előfizetés értékelése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Az Értékelés támogatja az **SP-t és az IDP** által kezdeményezett sso-t.

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd hozzáadása a galériából

Az Azure AD-be való integráció konfigurálásához hozzá kell adnia a katalógusból származó értékeléseket a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be **az Értékelés** kifejezést a keresőmezőbe.
1. Válassza **az Értékelés lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD-sSO-t a **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a appraisd.

Az Azure AD SSO beállítással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Az Appraisd konfigurálása](#configure-appraisd)** az sso-beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre egy értékelésteszt-felhasználót,](#create-appraisd-test-user)** hogy b. Simon megfelelője legyen az appraisdban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Appraisd** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció szakaszban** az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a Mentés gombra kattintva, és végre kell hajtania a következő lépéseket:

    a. Kattintson **a További URL-címek beállítása gombra.**

    b. A **Továbbítási állapot** mezőbe írjon be egy URL-címet:`<TENANTCODE>`

    c. Ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni, a **Bejelentkezési URL mezőbe** írjon be egy URL-címet a következő minta használatával:`https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > A tényleges bejelentkezési URL-címet és a továbbítási állapot értékét az egyszeri bejelentkezés konfigurációja lapon kapja meg, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az értékelt alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. Az appra vonatkozó alkalmazás azt várja, hogy a **névazonosító** le legyen képezve a **user.mail programmal,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva és az attribútumleképezés módosításával kell módosítania.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Értékelés beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Értékelés konfigurálása

1. Az Appraisd konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngésző, kattintson a **Setup Appraisd** irányítja, hogy az Értékelt alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy jelentkezzen be a Beértékelés. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani az értékeléseket, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az értékelt vállalati webhelyre, és hajtsa végre a következő lépéseket:

4. A lap jobb felső részén kattintson a **Beállítások** ikonra, majd lépjen a **Konfiguráció gombra.**

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. A menü bal oldalán kattintson az **SAML egyszeri bejelentkezésre.**

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Az **SAML 2.0 Single Sign-On konfigurációs** lapon hajtsa végre a következő lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Másolja **az alapértelmezett továbbítási állapot** értékét, és illessze be a **továbbítási állapot** szövegmezőbe az Azure Portal **alapszintű SAML-konfigurációjában.**

    b. Másolja a **szolgáltatás által kezdeményezett bejelentkezési URL-értéket,** és illessze be **a Bejelentkezési URL-cím** beírt szövegébe az Azure Portal **alapszintű SAML-konfigurációjában.**

7. Görgessen le ugyanazon az oldalon a Felhasználók azonosítása csoportban, **hajtsa**végre az alábbi lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Az **identitásszolgáltató egyetlen bejelentkezési URL-címmezőjébe** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt, majd kattintson a **Mentés gombra.**

    b. Az **Identitásszolgáltató kiállítójának URL-címmezőjébe** illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt, majd kattintson a **Mentés gombra.**

    c. A Jegyzettömbben nyissa meg az Azure Portalról letöltött 64-es alapú kódolt tanúsítványt, másolja annak tartalmát, majd illessze be az **X.509 tanúsítvány** mezőbe, és kattintson a **Mentés gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B. Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi B. Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít az appraisd.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Appraisd**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-appraisd-test-user"></a>Értékelésteszt-felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a appraisd, ki kell építeni a appraisd. A felértékelésben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Appraisd biztonsági rendszergazdaként.

2. A lap jobb felső részén kattintson a **Beállítások** ikonra, majd lépjen a **Felügyeleti központba**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. A lap tetején lévő eszköztáron kattintson a **Személyek**gombra, majd keresse meg az **Új felhasználó hozzáadása**lapot.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Az **Új felhasználó hozzáadása** lapon hajtsa végre az alábbi lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, **például Simon**.

    c. Az **E-mail** szöveg mezőbe írja `B. Simon@contoso.com`be a felhasználó e-mail címét, például .

    d. Kattintson **a Felhasználó hozzáadása**gombra.

### <a name="test-sso"></a>SSO tesztelése

Amikor kiválasztja az Appraisd csempét a Hozzáférési panelen, automatikusan be kell jelentkeznie arra az értékelésre, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
