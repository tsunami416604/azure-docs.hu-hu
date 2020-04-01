---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Harness-szal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Harness között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026957"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Harness-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Harness-et az Azure Active Directoryval (Azure AD). Ha integrálja a Harness-et az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a Harnesshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a Harness az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Használja ki az egyszeri bejelentkezéssel (SSO) engedélyezett előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A kábelköteg támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-harness-from-the-gallery"></a>A Hám hozzáadása a galériából

A Harness Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Harness-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Hasznostár** kifejezést a keresőmezőbe.
1. Válassza a **Harness** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Harness számára

Konfigurálja és tesztelje az Azure AD SSO-t a Harness segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Harness-ban.

Az Azure AD SSO konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a harness egyszeri egyszeri](#configure-harness-sso)** bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre egy harness teszt felhasználó](#create-harness-test-user)** - egy megfelelője B.Simon a Hám, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Harness** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://app.harness.io/`

    > [!NOTE]
    > A Válasz URL-értéke nem valós. A tényleges válasz URL-címét a **Harness SSO konfigurálása sso** szakaszból kapja, amelyet az oktatóanyag későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Kábelköteg beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t a Harness hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Harness**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-harness-sso"></a>Kábelköteg-szo-stso konfigurálása

1. A Biztonsági asszimilálón belüli konfiguráció automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadt a bővítményt a böngészőhöz, kattintson a **Setup Harness** gombra, amely a Harness alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a További műveletekhez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Harness programot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Kábelköteg vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. A lap jobb felső részén kattintson a Folyamatos biztonsági > **hozzáférés-kezelés** > **hitelesítési beállításai**elemre. **Continuous Security**

    ![Hám konfigurációja](./media/harness-tutorial/configure01.png)

5. Az **SSO-szolgáltatók** szakaszban kattintson a **+ Add SSO Providers** > **SAML gombra.**

    ![Hám konfigurációja](./media/harness-tutorial/configure03.png)

6. Az **SAML-szolgáltató** előugró ablakán hajtsa végre az alábbi lépéseket:

    ![Hám konfigurációja](./media/harness-tutorial/configure02.png)

    a. Másolja a **sso-szolgáltatóba, engedélyezze az SAML-alapú bejelentkezést, majd írja be a következő URL-példányt,** és illessze be a Válasz URL-szövegmezőbe az Azure Portal **alapszintű SAML-konfigurációszakaszában.**

    b. A **Megjelenítendő név** mezőbe írja be a megjelenítendő nevet.

    c. Kattintson **a Fájl kiválasztása** az Összevonási metaadat-XML-fájl feltöltéséhez, amelyet az Azure AD-ből töltött le.

    d. Kattintson **a KÜLDÉS gombra.**

### <a name="create-harness-test-user"></a>Kábelköteg tesztfelhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Harness szolgáltatásba, ki kell építeni őket a Harness-be. A Kábelkötegben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Harness rendszergazdaként.

1. A lap jobb felső részén kattintson a **Folyamatos biztonsági** > **hozzáférés-kezelési** > felhasználók**elemre.**

    ![Hám konfigurációja](./media/harness-tutorial/configure04.png)

1. Az oldal jobb oldalán kattintson a **+ Felhasználó hozzáadása gombra.**

    ![Hám konfigurációja](./media/harness-tutorial/configure05.png)

1. A **Felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Hám konfigurációja](./media/harness-tutorial/configure06.png)

    a. Az **E-mail cím(ek)** mezőbe írja `B.simon@contoso.com`be a felhasználó e-mail címét, például.

    b. Válassza ki a **felhasználói csoportokat.**

    c. Kattintson a **Küldés gombra.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Kábelköteg csempére kattint, automatikusan be kell jelentkeznie arra a biztonsági eszközre, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Harness szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

