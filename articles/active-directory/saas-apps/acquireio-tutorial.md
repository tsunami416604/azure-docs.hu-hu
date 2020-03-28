---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az AcquireIO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AcquireIO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76714616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az AcquireIO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AcquireIO-t az Azure Active Directoryval (Azure AD). Ha integrálja az AcquireIO-t az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér acquireio.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve a AcquireIO az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* AcquireIO egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az AcquireIO támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-acquireio-from-the-gallery"></a>AcquireIO hozzáadása a gyűjteményből

A AcquireIO azure AD-be való integrálásának konfigurálásához hozzá kell adnia a acquireio-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **AcquireIO** kifejezést a keresőmezőbe.
1. Válassza a **AcquireIO elemet** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az AcquireIO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az AcquireIO-val egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között acquireio.

Az Azure AD SSO konfigurálásához és teszteléséhez az AcquireIO segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[AcquireIO SSO konfigurálása](#configure-acquireio-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[AcquireIO-tesztfelhasználó létrehozása](#create-acquireio-test-user)** – b.Simon megfelelője az AcquireIO-ban, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg a **Manage** **(Kezelés)** szakaszt az Azure Portalon, és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Az érték nem valós. A tényleges válasz URL-címet kapja, amelyet később az oktatóanyag **AcquireIO konfigurálása** szakaszában ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **AcquireIO beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést a AcquireIO.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **AcquireIO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-acquireio-sso"></a>AcquireIO SSO konfigurálása

1. A AcquireIO konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzáadja a bővítményt a böngészőhöz, kattintson a **AcquireIO beállítása gombra,** amely az AcquireIO alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat az AcquireIO-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani az AcquireIO-t, egy másik böngészőablakban jelentkezzen be az AcquireIO-ba rendszergazdaként.

1. A menü bal oldalán kattintson az **App Store elemre.**

     ![AcquireIO-konfiguráció](./media/acquireio-tutorial/config01.png)

1. Görgessen le az **Active Directoryhoz,** és kattintson a **Telepítés gombra.**

    ![AcquireIO-konfiguráció](./media/acquireio-tutorial/config02.png)

1. Az Active Directory előugró ablakában hajtsa végre az alábbi lépéseket:

    ![AcquireIO-konfiguráció](./media/acquireio-tutorial/config03.png)

    a. Kattintson a **Másolás** gombra a válasz URL-cím másolásához a példányhoz, és illessze be a **Válasz URL-szövegmezőbe** az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    b. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Nyissa meg a Base64 kódolású tanúsítványt a Jegyzettömbben, másolja annak tartalmát, és illessze be az **X.509 Tanúsítvány** mezőbe.

    d. Kattintson **a Csatlakozás most gombra.**

### <a name="create-acquireio-test-user"></a>AcquireIO tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a AcquireIO-ba, ki kell építeni őket az AcquireIO-ba. Az AcquireIO-ban a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be az AcquireIO-ba rendszergazdaként.

1. A menü bal oldalán kattintson a **Profilok** elemre, és keresse meg a **Profil hozzáadása parancsot.**

     ![AcquireIO-konfiguráció](./media/acquireio-tutorial/config04.png)

1. Az **Ügyfél hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![AcquireIO-konfiguráció](./media/acquireio-tutorial/config05.png)

    a. A **Név** mezőbe írja be a felhasználó nevét, például **B.simon**.

    b. Az **E-mail** szöveg mezőbe írja **B.simon@contoso.com**be a felhasználó e-mail címét, például .

    c. Kattintson a **Küldés gombra.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az AcquireIO csempére kattint, automatikusan be kell jelentkeznie az OnacquireIO-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az AcquireIO-t az Azure AD-vel](https://aad.portal.azure.com/)
