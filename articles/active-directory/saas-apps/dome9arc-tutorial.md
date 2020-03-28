---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Check Point CloudGuard Dome9 Arc- Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Check Point CloudGuard Dome9 Arc között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73885352"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Check Point CloudGuard Dome9 Arc-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Check Point CloudGuard Dome9 Arc szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Check Point CloudGuard Dome9 Arc-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Check Point CloudGuard Dome9 Arc.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve check point CloudGuard Dome9 Arc az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Check Point CloudGuard Dome9 Arc egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Check Point CloudGuard Dome9 Arc támogatja **az SP és az IDP** által kezdeményezett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Check Point CloudGuard Dome9 Arc hozzáadása a galériából

A Check Point CloudGuard Dome9 Arc azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Check Point CloudGuard Dome9 Arc-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **Check Point CloudGuard Dome9 Arc** kifejezést a keresőmezőbe.
1. Válassza **a Check Point CloudGuard Dome9 Arc** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Check Point CloudGuard Dome9 Arc hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Check Point CloudGuard Dome9 Arc segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Check Point CloudGuard Dome9 Arc.

Az Azure AD SSO konfigurálásához és teszteléséhez a Check Point CloudGuard Dome9 Arc segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be a Check Point CloudGuard Dome9 Arc SSO-t](#configure-check-point-cloudguard-dome9-arc-sso)** – az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre Check Point CloudGuard Dome9 Arc teszt felhasználó](#create-check-point-cloudguard-dome9-arc-test-user)** - egy megfelelője B.Simon a Check Point CloudGuard Dome9 Arc, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Check Point CloudGuard Dome9 Arc** alkalmazásintegrációs lapon keresse meg a Kezelés **szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:`https://secure.dome9.com/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Az értéket `<company name>` a **Check Point CloudGuard Dome9 Arc SSO** szakaszból kapja, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Check Point CloudGuard Dome9 Arc alkalmazás elvárja az SAML állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútum-leképezések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Check Point CloudGuard Dome9 Arc alkalmazás azt várja, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.
    
    | Név |  Forrás attribútuma|
    | ---------------| --------------- |
    | tagja | user.assignedroles |

    >[!NOTE]
    >Kattintson [ide,](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) hogy megtudja, hogyan hozhat létre szerepköröket az Azure AD-ben.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Check Point CloudGuard Dome9 Arc beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a Check Point CloudGuard Dome9 Arc.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Check Point CloudGuard Dome9 Arc**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Check Point CloudGuard Dome9 ívsso konfigurálása

1. A Check Point CloudGuard Dome9 Arc konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngésző, kattintson a **Setup Check Point CloudGuard Dome9 Arc** irányítja, hogy a Check Point CloudGuard Dome9 Arc alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Check Point CloudGuard Dome9 Arc-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Check Point CloudGuard Dome9 Arc-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Check Point CloudGuard Dome9 Arc vállalati webhelyére, és hajtsa végre a következő lépéseket:

2. Kattintson a jobb felső sarokban található **Profilbeállítások** elemre, majd a **Fiókbeállítások parancsra.** 

    ![Check Point CloudGuard Dome9 arc konfiguráció](./media/dome9arc-tutorial/configure1.png)

3. Nyissa meg az **SSO-t,** majd kattintson **az ENABLE gombra.**

    ![Check Point CloudGuard Dome9 arc konfiguráció](./media/dome9arc-tutorial/configure2.png)

4. Az SSO-konfiguráció szakaszban hajtsa végre a következő lépéseket:

    ![Check Point CloudGuard Dome9 arc konfiguráció](./media/dome9arc-tutorial/configure3.png)

    a. Írja be a vállalat nevét a **Fiókazonosító** mezőbe. Ezt az értéket az Azure Portal **Basic SAML Configuration** szakaszában szereplő **Válasz** és **bejelentkezés** url-címben kell használni.

    b. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portal űrlapjának másolásával másolt.

    c. Az **Idp-végpont URL-szövegdobozába** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portal űrlapjának másolásával másolt.

    d. Nyissa meg a letöltött Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **X.509 tanúsítvány** szövegdobozába.

    e. Kattintson a **Mentés** gombra.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Check Point CloudGuard Dome9 Arc tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Check Point CloudGuard Dome9 Arc-ba, ki kell őket építeni az alkalmazásba. Check Point CloudGuard Dome9 Arc támogatja a just-in-time kiépítése, de ahhoz, hogy megfelelően működjön, a felhasználónak ki kell választania az adott **szerepkör,** és hozzá kell rendelnie ugyanazt a felhasználóhoz.

   >[!Note]
   >A **szerepkör létrehozásához** és egyéb részletekért forduljon [a Check Point CloudGuard Dome9 Arc ügyféltámogatási csapatához.](mailto:Dome9@checkpoint.com)

**Felhasználói fiók manuális kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Check Point CloudGuard Dome9 Arc vállalati webhelyére rendszergazdaként.

2. Kattintson a **Felhasználók & szerepkörök,** majd a **Felhasználók**elemre.

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user1.png)

3. Kattintson **a Felhasználó hozzáadása gombra.**

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user2.png)

4. A **Felhasználó létrehozása** csoportban hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/dome9arc-tutorial/user3.png)

    a. Az **E-mail** mezőbe írja be B.Simon@contoso.coma felhasználó e-mail címét, például .

    b. Az **Utónév** mezőbe írja be a felhasználó keresztnevét , például B.

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például Simon.

    d. **SSO-felhasználó** legyen **bekapcsolva.**

    e. Kattintson **a CREATE gombra.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Check Point CloudGuard Dome9 Arc csempére kattint, automatikusan be kell jelentkeznie a Check Point CloudGuard Dome9 Arc ba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Check Point CloudGuard Dome9 Arc szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)