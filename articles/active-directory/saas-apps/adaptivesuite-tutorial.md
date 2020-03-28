---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Adaptive Insightsszolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Adaptive Insights között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232118"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Oktatóanyag: Az Adaptive Insights integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Adaptive Insightsot az Azure Active Directoryval (Azure AD). Ha integrálja az Adaptive Insightsot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Adaptive Insightshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve adaptív insights az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Adaptive Insights egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Adaptive Insights támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptív elemzési adatok hozzáadása a galériából

Az Adaptive Insights Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Adaptive Insights-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **Adaptive Insights** kifejezést a keresőmezőbe.
1. Válassza az **Adaptív elemzési adatok** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Adaptive Insights segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Adaptive Insightsban.

Az Azure AD SSO konfigurálásához és teszteléséhez az Adaptive Insights segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Az Adaptive Insights egyszeri bejelentkezés konfigurálása](#configure-adaptive-insights-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Adaptive Insights tesztfelhasználó](#create-adaptive-insights-test-user)** – egy megfelelője B.Simon az Adaptive Insights, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Adaptive Insights-alkalmazások** integrációja lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Az Identifier(Entity ID) és a Reply URL-értékekaz Adaptive Insights **SAML SSO settings** lapján szerezhető be.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Adaptív elemzések beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Adaptív insights-sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Adaptive Insights vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Közigazgatás**.

    ![Felügyelet](./media/adaptivesuite-tutorial/ic805644.png "Rendszergazda")

3. A **Felhasználók és szerepkörök** csoportban kattintson az **SAML SSO-beállítások gombra.**

    ![SAML SSO-beállítások kezelése](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO-beállítások kezelése")

4. Az **SAML SSO-beállítások** lapon hajtsa végre az alábbi lépéseket:

    ![SAML SSO-beállítások](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-beállítások")

    a. Az **Identitásszolgáltató nevének** mezőbe írja be a konfiguráció nevét.

    b. Illessze be az **Azure AD-azonosító** értékét az Azure Portalról az **Identitásszolgáltató entitásazonosító** szövegmezőjébe.

    c. Illessze be az Azure Portalról másolt **bejelentkezési URL-értéket** az **identitásszolgáltató sso URL-címének** szövegmezőjébe.

    d. Illessze be az Azure Portalról másolt **kijelentkezési URL-értéket** az **egyéni kijelentkezési URL-cím** beszövegbe.

    e. A letöltött tanúsítvány feltöltéséhez kattintson a **Fájl kiválasztása gombra.**

    f. Válassza ki a következőt:

     * **SAML felhasználói azonosító**, válassza **a Felhasználó Adaptive Insights felhasználónevét**.

     * **SAML felhasználói azonosító helye**, válassza **a Felhasználó azonosítója a Névazonosító a tárgy**.

     * **SAML NameID formátumban**válassza **az E-mail cím**lehetőséget.

     * **Engedélyezze az SAML funkciót,** válassza **az SAML-sso engedélyezése és a közvetlen Adaptive Insights bejelentkezés**lehetőséget.

    g. Másolja **az Adaptive Insights SSO URL-címét,** és illessze be az Azonosító **(entitásazonosító)** és a **Válasz URL-szövegdobozokat** az Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    h. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t engedhozzá az Adaptive Insights használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Adaptive Insights**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-adaptive-insights-test-user"></a>Adaptív elemzési tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az Adaptive Insightsba, ki kell építeni őket az Adaptive Insightsba. Az Adaptive Insights kiépítése manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be **az Adaptive Insights** vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Közigazgatás**.

   ![Felügyelet](./media/adaptivesuite-tutorial/IC805644.png "Rendszergazda")

3. A **Felhasználók és szerepkörök** csoportban kattintson a **Felhasználók gombra.**

   ![Felhasználó hozzáadása](./media/adaptivesuite-tutorial/IC805648.png "Felhasználó hozzáadása")

4. Az **Új felhasználó** csoportban hajtsa végre az alábbi lépéseket:

   ![Küldés](./media/adaptivesuite-tutorial/IC805649.png "Küldés")

   a. Írja be a kapcsolódó szövegdobozokba kiépíteni kívánt érvényes Azure Active Directory-felhasználó **nevét**, **felhasználónév**, **e-mail** **, jelszó.**

   b. Jelöljön ki egy **szerepkört.**

   c. Kattintson a **Küldés gombra.**

> [!NOTE]
> Az Adaptive Insights felhasználói fiókok létrehozásához használt bármely más Adaptive Insights felhasználói fiók-létrehozási eszközt vagy API-t az Adaptive AD felhasználói fiókok kiépítéséhez használhatja.

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Adaptive Insights csempére kattint, automatikusan be kell jelentkeznie az Adaptive Insightsba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

