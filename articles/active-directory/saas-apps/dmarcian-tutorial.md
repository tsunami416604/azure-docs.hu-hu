---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a dmarcianrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a dmarcian között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602c885deca429b56417181971ced495831ba5d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68823699"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Oktatóanyag: A dmarcian integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a dmarciant az Azure Active Directoryval (Azure AD). Ha integrálja a dmarcian-t az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér a dmarcian.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve dmarcian az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* dmarcian egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* dmarcian támogatja **az SP és az IDP** által kezdeményezett SSO-t

## <a name="adding-dmarcian-from-the-gallery"></a>Dmarcian hozzáadása a galériából

A dmarcian azure AD-be való integrálásának konfigurálásához hozzá kell adnia a dmarcian-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **dmarcian** kifejezést a keresőmezőbe.
1. Válassza ki **a dmarcian** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t dmarcian-nel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó dmarcian.

Az Azure AD SSO dmarcian használatával való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a dmarcian Egyszeri bejelentkezést](#configure-dmarcian-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre dmarcian teszt felhasználó](#create-dmarcian-test-user)** - egy megfelelője B.Simon dmarcian, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **dmarcian** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Ezek az értékek nem valósak. Ezeket az értékeket a tényleges azonosítóval, a válasz URL-jével és a bejelentkezési URL-címmel fogja frissíteni, amelyet az oktatóanyag későbbi részében ismertetünk.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Dmarcian SSO konfigurálása

1. A dmarcian-en belüli konfiguráció automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup dmarcian** irányítja, hogy a dmarcian alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy jelentkezzen be dmarcian. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a dmarcian-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a dmarcian vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a Jobb felső sarokban a **Profil** elemre, és keresse meg a **Beállítások (Beállítások) menüt.**

    ![A beállítások](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Görgessen le, és kattintson az **Egyszeri bejelentkezés** szakaszra, majd kattintson a **Konfigurálás gombra.**

    ![Az egységes](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. Az **SAML Single Sign-On** lapon állítsa be az **Állapot engedélyezve ként** című **lapot,** és hajtsa végre a következő lépéseket:

    ![A hitelesítés](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * A **Dmarcian hozzáadása az identitásszolgáltatóhoz** csoportban kattintson a **MÁSOLÁS gombra** a **helyességifeltétel-fogyasztói szolgáltatás URL-címének** másolásához, majd illessze be a **Válasz URL-szövegmezőbe** az Azure Portal **Alapszintű SAML-konfiguráció szakaszában.**

    * A **Dmarcian hozzáadása az identitásszolgáltatóhoz** csoportban kattintson a **MÁSOLÁS gombra** a példány **entitásazonosítójának** másolásához, majd illessze be az Azure Portal **Alapszintű SAML-konfiguráció szakaszában** az **Azonosító** szövegmezőbe.

    * A **Hitelesítés beállítása** csoportban az **Identitásszolgáltató metaadatok** szövegmezőjében illessze be az **Alkalmazásösszevonás metaadat-címét,** amelyet az Azure Portalról másolt.

    * A **Hitelesítés beállítása** csoport **attribútumutasítások** szövegmezőjében illessze be az URL-címet`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * A **Bejelentkezési URL beállítása** csoportban másolja a felhasználó **bejelentkezési URL-címét,** és illessze be **a Bejelentkezési URL-cím** beírt szövegmezőbe az Azure Portal **Alapszintű SAML-konfiguráció szakaszában.**

        > [!Note]
        > A **bejelentkezési URL-címet** a szervezetnek megfelelően módosíthatja.

    * Kattintson a **Mentés** gombra.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a dmarcian.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **a dmarcian**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-dmarcian-test-user"></a>Dmarcian tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek dmarcian, ki kell építeni a dmarcian. A dmarcian-ben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be, hogy biztonsági rendszergazdaként jelentkezzen be.

2. Kattintson a jobb felső sarokban található **Profil** elemre, és keresse meg a **Felhasználók kezelése lehetőséget.**

    ![A felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Az **SSO-felhasználók** szakasz jobb oldalán kattintson az **Új felhasználó hozzáadása gombra.**

    ![A felhasználó hozzáadása](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Az **Új felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Az új felhasználó](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Az **Új felhasználói e-mail** mezőbe írja be a felhasználó e-mail címét, például **\@brittasimon contoso.com**.

    b. Ha rendszergazdai jogokat szeretne adni a felhasználónak, válassza a **Felhasználó vájkálása rendszergazdaként**lehetőséget.

    c. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a dmarcian csempére kattint, automatikusan be kell jelentkeznie arra a dmarcianbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

