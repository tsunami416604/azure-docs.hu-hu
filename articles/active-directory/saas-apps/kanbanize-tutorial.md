---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Kanbanize-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Kanbanize között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373229"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Kanbanize-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Kanbanize-t az Azure Active Directoryval (Azure AD). Ha integrálja a Kanbanize-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Kanbanize.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve kanbanize az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Kanbanize egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Kanbanize támogatja az **SP-t és az IDP** által kezdeményezett sso-t
* A Kanbanize támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-kanbanize-from-the-gallery"></a>Kanbanize hozzáadása a galériából

A Kanbanize azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Kanbanize-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Kanbanize** kifejezést a keresőmezőbe.
1. Válassza a **Kanbanize** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Kanbanize-hoz

Konfigurálja és tesztelje az Azure AD-sSO-t a Kanbanize segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó kanbanize.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Kanbanize.

Az Azure AD SSO kanbanizálással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Kanbanize Egyszeri bejelentkezést](#configure-kanbanize-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Kanbanize tesztfelhasználó létrehozása](#create-kanbanize-test-user)** – b.Simon megfelelőjének a Kanbanize-ban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Kanbanize alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

     a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com/saml/acs`

    c. Kattintson **a További URL-címek beállítása gombra.**

    d. A **Továbbítási állapot** mezőbe írjon be egy URL-címet:`/ctrl_login/saml_login`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [a Kanbanize ügyféltámogatási csapatával,](mailto:support@ms.kanbanize.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A Kanbanize alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a nameidentifier a **user.userprincipalname**. A Kanbanize alkalmazás elvárja, hogy a névazonosító le legyen képezve **a user.mail programmal,** ezért az attribútumleképezést a Szerkesztés ikonra kattintva és az attribútumleképezés módosításával kell módosítania.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Kanbanize beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Kanbanize.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Kanbanize**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-kanbanize-sso"></a>Kanbanize sso konfigurálása

1. A Kanbanizin belüli konfiguráció automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Kanbanize** irányítja, hogy a Kanbanize alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a Kanbanize-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Kanbanize programot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Kanbanize vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Ugrás az oldal jobb felső részébe, kattintson a **Beállítások** emblémára.

    ![Kanbanize beállítások](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. A menü bal oldalán található Felügyeleti panel lapon kattintson az **Integrációk** elemre, majd engedélyezze **az egyszeri bejelentkezést.**

    ![Kanbanize integrációk](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. Az Integrációk csoportban kattintson a **KONFIGURÁLÁS gombra** **az Egyszeri bejelentkezésintegráció** lap megnyitásához.

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Az **Egyszeri bejelentkezés integrációja** lap Konfigurációk területén **hajtsa**végre az alábbi lépéseket:

    ![Kanbanize integrációk](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Az **Idp-entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    b. Az **Idp-bejelentkezési végpont** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Az **Idp-kijelentkezési végpont** szövegmezőbe illessze be a **kijelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. Az E-mail szövegdoboz **attribútumnevében** adja meg ezt az értéket`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Az Utónév szövegmező **Attribútumnevébe** írja be ezt az értéket.`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. A Vezetéknév szövegmező **Attribútumnevébe** írja be ezt az értéket.`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Ezeket az értékeket a megfelelő attribútum névtér- és névértékeinek kombinálásával szerezheti be az Azure Portal Felhasználói attribútumok szakaszában.

    g. A Jegyzettömbben nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmát (a kezdő- és zárójelölők nélkül), majd illessze be az **Idp X.509 tanúsítvány** mezőbe.

    h. Jelölje be **a bejelentkezés engedélyezése sso és kanbanize**.

    i. Kattintson **a Beállítások mentése gombra.**

### <a name="create-kanbanize-test-user"></a>Kanbanize tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre a Kanbanize-ban. A Kanbanize támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Kanbanize, egy új jön létre a hitelesítés után. Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a Kanbanize ügyféltámogatási csapatához.](mailto:support@ms.kanbanize.com)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Kanbanize csempére kattint, automatikusan be kell jelentkeznie arra a Kanbanize-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Kanbanize-t az Azure AD-vel](https://aad.portal.azure.com/)

