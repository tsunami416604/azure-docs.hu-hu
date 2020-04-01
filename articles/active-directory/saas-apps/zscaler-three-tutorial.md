---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zscaler Three-nal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler Three között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72554985"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zscaler Three-nal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler Three-t az Azure Active Directoryval (Azure AD). Ha integrálja a Zscaler Three-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zscaler 3.Control in Azure AD who has access to Zscaler Three.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve zscaler három az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Zscaler Három egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Zscaler Három támogatja **SP** kezdeményezett SSO

* A Zscaler Three támogatja **a Just In Time** felhasználói kiépítést

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-zscaler-three-from-the-gallery"></a>Zscaler 3 hozzáadása a galériából

A Zscaler 3 azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler Three-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Zscaler 3** értéket a keresőmezőbe.
1. Válassza a **Zscaler Three** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Zscaler Three-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler Three segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a zscaler három kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Zscaler Three segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Zscaler Three Egyszeri bejelentkezést](#configure-zscaler-three-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Zscaler Három teszt felhasználó](#create-zscaler-three-test-user)** - a B.Simon megfelelője a Zscaler 3, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler 3** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://login.zscalerthree.net/sfc_sso`

1. A Zscaler Three alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjának. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Zscaler Three alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.
    
    | Név | Forrás attribútuma |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    > [!NOTE]
    > Kérjük, kattintson [ide,](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) hogy megtudja, hogyan konfigurálhatja a szerepkört az Azure AD-ben

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Zscaler beállítása három** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Zscaler 3 hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Zscaler Three lehetőséget.**
1. A **Felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználót, például **Britta Simont** a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. A **Szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés** gombot.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Zscaler három sso konfigurálása

1. A Zscaler Three konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson **a Setup Zscaler Három** irányítja, hogy a Zscaler Három alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Zscaler 3-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Telepítés](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler Three-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Zscaler Three vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Nyissa meg **a Felügyeleti > hitelesítés i > hitelesítési beállításokat,** és hajtsa végre az alábbi lépéseket:
   
    ![Felügyelet](./media/zscaler-three-tutorial/ic800206.png "Adminisztráció")

    a. A Hitelesítés típusa csoportban válassza az **SAML lehetőséget.**

    b. Kattintson **az SAML konfigurálása gombra.**

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket: és kattintson a Mentés gombra.  
            
    ![A felhasználók & a hitelesítés kezelése](./media/zscaler-three-tutorial/ic800208.png "A felhasználók & a hitelesítés kezelése")
    
    a. Az **SAML Portal URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    b. A **Bejelentkezési név attribútum** mezőbe írja be a **NameID értéket.**

    c. Kattintson **a Feltöltés**gombra az Azure SAML-aláíró tanúsítvány feltöltéséhez, amelyet az Azure Portalról töltött le a **nyilvános SSL-tanúsítványban.**

    d. Az **SAML automatikus kiépítés engedélyezése**között.

    e. A **Felhasználó megjelenítendő neve attribútum** szövegmezőbe írja be a **displayName értéket,** ha engedélyezni szeretné az SAML automatikus kiépítését a displayName attribútumokhoz.

    f. A **Group Name Attribute** textbox, írja be **memberOf** ha engedélyezni szeretné SAML automatikus kiépítése a memberOf attribútumok.

    g. A **Részlegnév attribútum** enter **részlegében,** ha engedélyezni szeretné az SAML automatikus kiépítést a részlegattribútumokhoz.

    h. Kattintson a **Mentés** gombra.

6. A **Felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Adminisztráció](./media/zscaler-three-tutorial/ic800207.png)

    a. Mutasson az **Aktiválás** menüre a bal alsó sarokban.

    b. Kattintson **az Aktiválás gombra.**

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A proxybeállítások konfigurálása az Internet Explorer programban

1. Indítsa el **az Internet Explorer**t .

2. Válassza az **Internetbeállítások** párbeszédpanel megnyitásához válassza az **Eszközök** menü **Internetbeállítások** parancsát.   
    
     ![Internetbeállítások](./media/zscaler-three-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **Kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-three-tutorial/ic769493.png "Kapcsolatok")

4. A **LAN-beállítások gombra** kattintva nyissa meg a **LAN-beállítások párbeszédpanelt.**

5. A Proxykiszolgáló szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-three-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a Proxykiszolgáló használata a helyi hálózathoz**lehetőséget.

    b. A Cím mezőbe írja be az **átjárót. Zscaler Three.net**.

    c. A Port szövegmezőbe írja be a **80-at.**

    d. Válassza **a Proxykiszolgáló mellőzése lehetőséget a helyi címekhez.**

    e. A **Helyi hálózat (LAN) beállításai** párbeszédpanel bezárásához kattintson az **OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson az **OK** gombra.

### <a name="create-zscaler-three-test-user"></a>Zscaler Három tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a Zscaler 3-ban. Zscaler Három támogatja a just-in-time kiépítése, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Zscaler 3, egy új jön létre, amikor megpróbálja elérni Zscaler Három.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Zscaler Three támogatási csapatához.](https://www.zscaler.com/company/contact)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Zscaler Three csempére kattint, automatikusan be kell jelentkeznie a Zscaler 3-ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Zscaler Three-t az Azure AD-vel](https://aad.portal.azure.com/)

