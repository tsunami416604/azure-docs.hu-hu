---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Way We Do - Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Way We Do között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67310407"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Oktatóanyag: Az Azure Active Directoryval való integrációs módszer

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Way We Do szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Way We Do-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Way We Do.Control in Azure AD who has access to Way We Do.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve a Way We Do az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Way We Do egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A We Do támogatja **az SP** által kezdeményezett SSO-t
* A Just **In Time** felhasználói kiépítést támogató módszer

## <a name="adding-way-we-do-from-the-gallery"></a>Way We Do hozzáadása a galériából

A Way We Do azure AD-be való integrálásának konfigurálásához hozzá kell adnia a The We Do-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **"Hogyan csináljuk" kifejezést** a keresőmezőbe.
1. Válassza a **Hogyan csináljuk** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Way We Do segítségével egy **B.Simon**nevű tesztfelhasználóhasználatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Way We Do.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Way We Do.

Az Azure AD SSO konfigurálásához és teszteléséhez a Way We Do segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Way We Do SSO-t](#configure-way-we-do-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Create Way We Do teszt felhasználó](#create-way-we-do-test-user)** -, hogy egy megfelelője Britta Simon a Way We Do, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Hogyan csináljuk** az alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Way We Do ügyféltámogatási csapatával,](mailto:support@waywedo.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Raw)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. A **Beállítási módja we do** szakaszban másolja a megfelelő URL-cím(ek)et a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurálja a mi sso-módszerünket

1. A Way We Do konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

1. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup Way We Do** irányítja, hogy a Way We Do alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy jelentkezzen be way we do. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

1. Ha manuálisan szeretné beállítani a Way We Do-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Way We Do vállalati webhelyére, és hajtsa végre a következő lépéseket:

1. Kattintson a **személy ikonjára** a Way We Do bármely oldalának jobb felső sarkában, majd kattintson a Legördülő menü **Fiók parancsára.**

    ![Hogyan vesszük el a számlát](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kattintson a **menü ikonra** a leküldéses navigációs menü megnyitásához, majd kattintson **az Egyszeri bejelentkezés gombra.**

    ![Way We Do egyetlen](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Az **Egyszeri bejelentkezés beállítási** lapján hajtsa végre a következő lépéseket:

    ![Így nem menteni](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Az **egyszeri bejelentkezés bekapcsolása** kapcsolóra az **Igen** beállításhoz kattintson az egyszeri bejelentkezés engedélyezéséhez.

    b. Az **Egyszeri bejelentkezési név** mezőbe írja be a nevét.

    c. Az **Entitásazonosító** szövegmezőbe illessze be az **Azure AD-azonosító**értékét, amelyet az Azure Portalról másolt.

    d. Az **SAML SSO URL-cím** szövegdobozába illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    e. Töltse fel a tanúsítványt a **Tanúsítvány**gomb melletti **select** gombra kattintva.

    f. **Választható beállítások** -
    
    * Jelszavak engedélyezése – Ha ez a beállítás le van tiltva, a normál jelszó függvények a Way We Do, hogy a felhasználók csak akkor használhatják az egyszeri bejelentkezés.

    * Automatikus kiépítés engedélyezése – Ha ez engedélyezve van, a bejelentkezéshez használt e-mail-cím automatikusan összehasonlítja a felhasználók listáját a Way We Do-ban. Ha az e-mail cím nem egyezik meg egy aktív felhasználóval a Way We Do-ban, akkor automatikusan hozzáad egy új felhasználói fiókot a bejelentkező személyhez, és minden hiányzó információt kér.

      > [!NOTE]
      > Az egyszeri bejelentkezéssel hozzáadott felhasználók általános felhasználóként kerülnek hozzáadásra, és nincsenek szerepkörhöz rendelve a rendszerben. A rendszergazda beléphet, és módosíthatja a szerkesztői vagy rendszergazdai biztonsági szerepkörüket, és egy vagy több szervezetidiagram-szerepkört is hozzárendelhet.

    g. A beállítások megőrzéséhez kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a Way We Do.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Way We Do**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-way-we-do-test-user"></a>Create Way We Do teszt felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Way We Do alkalmazásban. A Way We Do támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Way We Do, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Way We Do Ügyfél támogatási csapatához.](mailto:support@waywedo.com)

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Hogyan csempe csempét, automatikusan be kell jelentkeznie az SSO beállításához vezető Módszerbe. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)