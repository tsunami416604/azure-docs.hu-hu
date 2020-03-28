---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Sage Intacct-tal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sage Intacct között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b28390dba009226d493f5bfc6a5270b067f5bba0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73570475"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Oktatóanyag: A Sage Intacct integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sage Intacct-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Sage Intacct-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Sage Intacct.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve sage Intacct az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Sage Intacct egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Sage Intacct támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-sage-intacct-from-the-gallery"></a>Sage Intacct hozzáadása a galériából

A Sage Intacct azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Sage Intacct-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be **a Sage Intacct kifejezést** a keresőmezőbe.
1. Válassza a **Sage Intacct** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sage-intacct"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Sage Intacct számára

Konfigurálja és tesztelje az Azure AD SSO-t a Sage Intacct segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó sage intacct.

Az Azure AD SSO sage intacct-tel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
2. **[Konfigurálja a Sage Intacct SSO-t](#configure-sage-intacct-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Sage Intacct teszt felhasználó](#create-sage-intacct-test-user)** – egy megfelelője B.Simon a Sage Intacct, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Sage Intacct** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt, és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:`https://www.intacct.com/ia/acct/sso_response.phtml`

1. A Sage Intacct alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához..

    ![image](common/edit-attribute.png)

1. A fentieken kívül a Sage Intacct alkalmazás azt várja, hogy néhány további attribútum ot visszaadjon az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Név  |  Forrás attribútuma|
    | ---------------| --------------- |
    | Vállalat neve | **Sage Intacct vállalat azonosítója** |
    | név | Az értéknek meg kell egyeznie a Sage Intacct **felhasználói azonosítójának**, amelyet a **Sage Intacct tesztfelhasználó létrehozása szakaszban**adhat meg, amelyet az oktatóanyag későbbi részében ismertet. |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier | Az értéknek meg kell egyeznie a Sage Intacct **Összevont SSO felhasználói azonosítóval,** amelyet a **Sage Intacct tesztfelhasználói részében**ír be, amelyet az oktatóanyag későbbi részében ismertetünk. |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sage Intacct beállítása szakaszban** másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Sage Intacct.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Sage Intacct lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sage-intacct-sso"></a>Sage Intacct SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sage Intacct vállalati webhelyére rendszergazdaként.

1. Kattintson a **Vállalat** fülre, majd a **Vállalati adatok gombra.**

    ![Vállalat](./media/intacct-tutorial/ic790037.png "Vállalat")

1. Kattintson a **Biztonság** fülre, majd a **Szerkesztés gombra.**

    ![Biztonság](./media/intacct-tutorial/ic790038.png "Biztonság")

1. Az **Egyszeri bejelentkezés (SSO)** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/intacct-tutorial/ic790039.png "egyszeri bejelentkezés")

    a. Válassza **az Egyszeri bejelentkezés engedélyezése lehetőséget.**

    b. **Identitásszolgáltató típusaként**válassza az **SAML 2.0**lehetőséget.

    c. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    d. A **Bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    e. Nyissa meg az **alap-64** kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a **Tanúsítvány** mezőbe.

    f. Kattintson a **Mentés** gombra.

### <a name="create-sage-intacct-test-user"></a>Sage Intacct tesztfelhasználó létrehozása

Az Azure AD-felhasználók beállítása, hogy azok bejelentkezhetnek a Sage Intacct, ki kell építeni a Sage Intacct. A Sage Intacct esetében a kiépítés manuális feladat.

**A felhasználói fiókok kiépítéséhez hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **Sage Intacct** bérlőbe.

1. Kattintson a **Vállalat** fülre, majd a **Felhasználók gombra.**

    ![Felhasználók](./media/intacct-tutorial/ic790041.png "Felhasználók")

1. Kattintson a **Hozzáadás** fülre.

    ![Hozzáadás](./media/intacct-tutorial/ic790042.png "Hozzáadás")

1. A **Felhasználói adatok** csoportban hajtsa végre az alábbi lépéseket:

    ![Felhasználói adatok](./media/intacct-tutorial/ic790043.png "Felhasználói adatok")

    a. Adja meg a **felhasználói azonosítót,** a **vezetéknevet**, az **utónevet**, az **e-mail címet**, a **címet**és a **telefon** egy Azure AD-fiók, amely et ki szeretne építeni a **Felhasználói adatok** szakaszban.

    > [!NOTE]
    > Győződjön meg arról, hogy a **felhasználói azonosító** a fenti képernyőkép és a **Source Attribute** érték, amely le van képezve a **name** attribútum az Azure Portal **Felhasználói attribútumok** szakaszában meg kell egyeznie.

    b. Válassza ki a **rendszergazdai jogosultságokat** egy Azure AD-fiók, amely kiépíteni szeretne.

    c. Kattintson a **Mentés** gombra. 
    
    d. Az Azure AD-fiók tulajdonosa kap egy e-mailt, és egy linket követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

1. Kattintson **az Egyszeri bejelentkezés** fülre, és győződjön meg arról, hogy az **összevont egyszeri egyszeri egyszeri felhasználói azonosító** a képernyőkép alatt, és a Forrás **attribútum** értéke, amely le van képezve a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` felhasználói **attribútumok** szakaszban az Azure Portalon meg kell egyeznie.

    ![Felhasználói adatok](./media/intacct-tutorial/ic790044.png "Felhasználói adatok")

> [!NOTE]
> Azure AD felhasználói fiókok kiépítéséhez használhatja a Sage Intacct felhasználói fiók létrehozásához szükséges eszközöket vagy API-kat, amelyeket a Sage Intacct biztosít.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Sage Intacct csempére kattint, automatikusan be kell jelentkeznie arra a Sage Intacct-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

