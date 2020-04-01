---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zscalerrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcf0341e03a5d95abbe8b1a8ce69379fef8251b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989048"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zscalerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler-t az Azure Active Directoryval (Azure AD). Ha integrálja a Zscaler-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zscaler.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Zscaler az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Zscaler egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Zscaler támogatja az **SP** által kezdeményezett SSO-t
* A Zscaler támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a galériából

A Zscaler azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Zscaler** kifejezést a keresőmezőbe.
1. Válassza a **Zscaler** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Zscaler számára

Konfigurálja és tesztelje az Azure AD SSO-t a Zscalerrel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a zscaler kapcsolódó felhasználója között.

Az Azure AD SSO zscalerrel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Zscaler Egyszeri bejelentkezés is-t](#configure-zscaler-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Zscaler teszt felhasználó](#create-zscaler-test-user)** - a B.Simon a Zscaler, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.zscaler.net`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Zscaler ügyféltámogatási csapatával](https://www.zscaler.com/company/contact) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A Zscaler-alkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentiekmellett a Zscaler alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Név | Forrás attribútuma |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Kérjük, kattintson [ide,](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) hogy megtudja, hogyan konfigurálhatja a szerepkört az Azure AD-ben

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Zscaler beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Zscaler.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Zscaler**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler**lehetőséget.

    ![A Zscaler hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználót, például **Britta Simont** a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. A **Szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés** gombot.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Zscaler sso konfigurálása

1. A Zscaler konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson **a Setup Zscaler** irányítja, hogy a Zscaler alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Zscaler-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási sso](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Zscalert, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Zscaler vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Nyissa meg **a Felügyeleti > hitelesítés i > hitelesítési beállításokat,** és hajtsa végre az alábbi lépéseket:

    ![Felügyelet](./media/zscaler-tutorial/ic800206.png "Adminisztráció")

    a. A Hitelesítés típusa csoportban válassza az **SAML lehetőséget.**

    b. Kattintson **az SAML konfigurálása gombra.**

1. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket: és kattintson a Mentés gombra.  

    ![A felhasználók & a hitelesítés kezelése](./media/zscaler-tutorial/ic800208.png "A felhasználók & a hitelesítés kezelése")
    
    a. Az **SAML Portal URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    b. A **Bejelentkezési név attribútum** mezőbe írja be a **NameID értéket.**

    c. Kattintson **a Feltöltés**gombra az Azure SAML-aláíró tanúsítvány feltöltéséhez, amelyet az Azure Portalról töltött le a **nyilvános SSL-tanúsítványban.**

    d. Az **SAML automatikus kiépítés engedélyezése**között.

    e. A **Felhasználó megjelenítendő neve attribútum** szövegmezőbe írja be a **displayName értéket,** ha engedélyezni szeretné az SAML automatikus kiépítését a displayName attribútumokhoz.

    f. A **Group Name Attribute** textbox, írja be **memberOf** ha engedélyezni szeretné SAML automatikus kiépítése a memberOf attribútumok.

    g. A **Részlegnév attribútum** enter **részlegében,** ha engedélyezni szeretné az SAML automatikus kiépítést a részlegattribútumokhoz.

    h. Kattintson a **Mentés** gombra.

1. A **Felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Adminisztráció](./media/zscaler-tutorial/ic800207.png)

    a. Mutasson az **Aktiválás** menüre a bal alsó sarokban.

    b. Kattintson **az Aktiválás gombra.**

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A proxybeállítások konfigurálása az Internet Explorer programban

1. Indítsa el **az Internet Explorer**t .

1. Válassza az **Internetbeállítások** párbeszédpanel megnyitásához válassza az **Eszközök** menü **Internetbeállítások** parancsát.

    ![Internetbeállítások](./media/zscaler-tutorial/ic769492.png "Internetbeállítások")

1. Kattintson a **Kapcsolatok** fülre.
  
    ![Kapcsolatok](./media/zscaler-tutorial/ic769493.png "Kapcsolatok")

1. A **LAN-beállítások gombra** kattintva nyissa meg a **LAN-beállítások párbeszédpanelt.**

1. A Proxykiszolgáló szakaszban hajtsa végre az alábbi lépéseket:   

    ![Proxykiszolgáló](./media/zscaler-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a Proxykiszolgáló használata a helyi hálózathoz**lehetőséget.

    b. A Cím mezőbe írja be **a gateway.zscaler.net.**

    c. A Port szövegmezőbe írja be a **80-at.**

    d. Válassza **a Proxykiszolgáló mellőzése lehetőséget a helyi címekhez.**

    e. A **Helyi hálózat (LAN) beállításai** párbeszédpanel bezárásához kattintson az **OK** gombra.

1. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="create-zscaler-test-user"></a>Zscaler tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscalerben. Zscaler támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Zscaler, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Zscaler támogatási csapatához.](https://www.zscaler.com/company/contact)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Zscaler csempére kattint, automatikusan be kell jelentkeznie a Zscalerbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Zscaler-t az Azure AD-vel](https://aad.portal.azure.com/)
