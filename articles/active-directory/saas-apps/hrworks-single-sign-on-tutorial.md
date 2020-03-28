---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a HRworks single sign-on szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a HRworks single sign-on között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c4c5d434-3f8a-411e-83a5-c3d5276ddc0a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b20ce5f754333aec78513e32901b0608f8bee3b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75638742"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a HRworks single sign-on szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a HRworks single sign-on szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a HRworks single sign-on-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a HRworks single sign-on.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve HRworks Single Sign-On az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* HRworks single sign-on egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A HRworks Single Sign-On támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>HRworks single sign-on hozzáadása a galériából

A HRworks Single Sign-On azure-ba való integrációjának konfigurálásához hozzá kell adnia a HRworks Single Sign-On-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **HRworks Single Sign-On** kifejezést a keresőmezőbe.
1. Válassza a **HRworks Single Sign-On** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a HRworks single sign-on szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD Egyszeri bejelentkezést a HRworks single sign-on szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a HRworks single sign-on.

Az Azure AD Egyszeri bejelentkezés konfigurálásához és teszteléséhez a HRworks single sign-on szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Configure HRworks Single Sign-On SSO](#configure-hrworks-single-sign-on-sso)** - to configure the single sign-on settings on application side.
    * **[Hozzon létre HRworks Single Sign-On teszt felhasználó](#create-hrworks-single-sign-on-test-user)** - egy megfelelője B.Simon a HRworks Single Sign-On, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **HRworks egyszeri bejelentkezésalkalmazás-integrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba a [HRworks Single Sign-On ügyféltámogatási csapatával](mailto:nadja.sommerfeld@hrworks.de) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A HRworks egyszeri bejelentkezés beállítása szakaszban másolja a megfelelő **URL-cím(eke)t** a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a HRworks single sign-on használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **HRworks Single Sign-On**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-hrworks-single-sign-on-sso"></a>A HRworks egyszeri bejelentkezési egyszeri egyszeri egyszeri bejelentkezési egyszeri bejelentkezési egyszeri bejelentkezésének konfigurálása

1. A HRworks Single Sign-On konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Miután hozzátette a bővítményt a böngészőhöz, kattintson a **HRworks egyszeri bejelentkezés beállítása** gombra, amely a HRworks single sign-on alkalmazáshoz irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a HRworks single sign-on-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-4 lépéseket.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a HRworks Single Sign-On alkalmazást, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a HRworks Single Sign-On vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a **Rendszergazda** > alapjai**biztonsági** > **egyszeri bejelentkezés** elemre a menüsor bal oldalán, és**hajtsa** > végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Jelölje be az **Egyszeri bejelentkezés használata jelölőnégyzetet.**

    b. Jelölje ki **az XML-metaadatokat** **metaadat-beviteli módszerként.**

    c. Válassza az **Egyéni névazonosító azonosítót** **a NameID értékként.**

    d. A Jegyzettömbben nyissa meg az Azure Portalról letöltött metaadat-XML-t, másolja a tartalmát, majd illessze be a **Metaadatok** szövegmezőbe.

    e. Kattintson a **Mentés** gombra.

### <a name="create-hrworks-single-sign-on-test-user"></a>HRworks single sign-on tesztfelhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a HRworks single sign-on, ki kell építeni a HRworks single sign-on. A HRworks egyszeri bejelentkezés, kiépítése egy manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a HRworks egyszeri bejelentkezésbe rendszergazdaként.

1. Kattintson **a Rendszergazdák** > **személyek** > **Persons** > **Új személy elemre** a menüsor bal oldalán.

     ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Az előugró ablakban kattintson a **Tovább**gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. A **Jogi kifejezésekhez országgal rendelkező új személy létrehozása** előugró ablakban töltse ki a megfelelő adatokat, **például utónév**, **vezetéknév,** és kattintson a **Létrehozás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a HRworks egyszeri bejelentkezés csempére kattint, automatikusan be kell jelentkeznie a HRworks egyszeri bejelentkezésbe, amelyhez beállítja az egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a HRworks single sign-on-t az Azure AD-vel](https://aad.portal.azure.com/)