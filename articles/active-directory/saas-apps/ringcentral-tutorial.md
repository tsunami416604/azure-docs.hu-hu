---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a RingCentral szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a RingCentral között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: de7cf57d177902efdbb44524703481e8c65c75c5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72991472"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Oktatóanyag: A RingCentral integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a RingCentral-t az Azure Active Directoryval (Azure AD). Ha integrálja a RingCentral-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a RingCentral.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a RingCentral az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A RingCentral egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A RingCentral támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral hozzáadása a galériából

A RingCentral azure AD-be való integrálásának konfigurálásához hozzá kell adnia a RingCentral-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **RingCentral** kifejezést a keresőmezőbe.
1. Válassza a **RingCentral** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a RingCentral segítségével egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a RingCentral-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez a RingCentral segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a RingCentral Egyszeri bejelentkezést](#configure-ringcentral-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre RingCentral tesztfelhasználó](#create-ringcentral-test-user)** – a B.Simon megfelelője a RingCentralban, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **RingCentral** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    1. Kattintson **a Metaadatfájl feltöltése gombra.**
    1. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**
    1. A metaadatfájl sikeres feltöltése után az **Azonosító** és a **Válasz URL-értékei** automatikusan feltöltődnek az **Egyszerű SAML-konfiguráció** szakaszban.

    > [!Note]
    > A **Szolgáltató metaadatfájlja** a RingCentral SSO konfigurációs lapon jelenik meg, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Ha nem rendelkezik **szolgáltatói metaadatfájllal,** adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. A **Válasz URL-címmezőjébe** írjon be egy URL-címet:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

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

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést a RingCentral-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **RingCentral**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-ringcentral-sso"></a>RingCentral sso konfigurálása

1. A RingCentral konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása RingCentral** irányítja, hogy a RingCentral alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a RingCentral-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a RingCentral alkalmazást, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a RingCentral vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. A tetején kattintson az **Eszközök gombra.**

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Nyissa meg az **Egyszeri bejelentkezés**t.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Az **Egyszeri bejelentkezés** lap Egyszeri bejelentkezés lap Egyszeri bejelentkezés **Edit** **konfigurációja** szakaszában az **1.**

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Az **Egyszeri bejelentkezés beállítása** lapon hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. A **Tallózás** gombra kattintva feltöltheti az Azure Portalról letöltött metaadatfájlt.

    b. A metaadatok feltöltése után az értékek automatikusan kitöltődnek az **Egyszeri bejelentkezés általános információ szakaszában.**

    c. Az **Attribútumleképezés** csoportban válassza **az E-mail attribútum leképezése**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kattintson a **Mentés** gombra.

    e. **A 2.** **Download** **Service Provider metadata file** **Basic SAML Configuration** **Identifier** **Reply URL**

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Ugyanezen az oldalon keresse meg az **SSO engedélyezése** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Válassza az **SSO-szolgáltatás engedélyezése**lehetőséget.

    * Válassza **a Felhasználók bejelentkezésengedélyezése SSO vagy RingCentral hitelesítő adatokkal**lehetőséget.

    * Kattintson a **Mentés** gombra.

### <a name="create-ringcentral-test-user"></a>RingCentral tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a RingCentral alkalmazásban. Együttműködve [RingCentral ügyfél támogatási csapatával](https://success.ringcentral.com/RCContactSupp) a felhasználók hozzáadása a RingCentral platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a RingCentral csempét, automatikusan be kell jelentkeznie arra a RingCentral-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a RingCentral-t az Azure AD-vel](https://aad.portal.azure.com/)