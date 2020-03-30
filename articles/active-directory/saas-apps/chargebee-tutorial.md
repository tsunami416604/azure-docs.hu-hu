---
title: 'Bemutató: Az Azure Active Directory integrációja a Chargebee-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Chargebee között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 033d413d-1656-4d9c-a606-dd33c23948f9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bbaf3d527ad1e58914c6b3f9c8b5b4ea57ae08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68931853"
---
# <a name="tutorial-integrate-chargebee-with-azure-active-directory"></a>Oktatóanyag: A Chargebee integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Chargebee-t az Azure Active Directoryval (Azure AD). Ha integrálja a Chargebee-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér a Chargebee-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezhessenek a Chargebee-be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Chargebee egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Chargebee támogatja az **SP-t és az IDP** által kezdeményezett SSO-t

## <a name="adding-chargebee-from-the-gallery"></a>Chargebee hozzáadása a galériából

A Chargebee Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Chargebee-t a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be a **Chargebee** kifejezést a keresőmezőbe.
1. Válassza **a Chargebee** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-chargebee"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Chargebee-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Chargebee-vel egy **B.Simon**nevű tesztfelhasználó segítségével. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a Chargebee kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Chargebee segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja a Chargebee SSO-t](#configure-chargebee-sso)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre Chargebee teszt felhasználó](#create-chargebee-test-user)** - hogy egy megfelelője B.Simon a Chargebee, amely kapcsolódik az Azure AD képviselete a felhasználó.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Chargebee** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domainname>.chargebee.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://app.chargebee.com/saml/<domainname>/acs`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domainname>.chargebee.com`

    > [!NOTE]
    > `<domainname>`Annak a tartománynak a neve, amelyet a felhasználó a fiók igénylése után hoz létre. Bármilyen egyéb információ esetén lépjen kapcsolatba a [Chargebee ügyféltámogatási csapatával.](mailto:support@chargebee.com) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Chargebee beállítása** szakaszon másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezheti b.Simon számára az Azure egyszeri bejelentkezést a Chargebee-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Chargebee**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-chargebee-sso"></a>Chargebee SSO konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be a Chargebee vállalati webhelyére rendszergazdaként.

4. A menü bal oldalán kattintson a **Beállítások** > **biztonsági** > **kezelése gombra.**

    ![Chargebee konfiguráció](./media/chargebee-tutorial/config01.png)

5. Az **egyszeri bejelentkezés előugró** ablakban hajtsa végre a következő lépéseket:

    ![Chargebee konfiguráció](./media/chargebee-tutorial/config02.png)

    a. Válassza **az SAML lehetőséget.**

    b. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    c. Nyissa meg a Base64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát, és illessze be az **SAML tanúsítvány** szövegmezőjébe.

    d. Kattintson a **Megerősítés** gombra.

### <a name="create-chargebee-test-user"></a>Chargebee tesztfelhasználó létrehozása

Az Azure AD-felhasználók engedélyezéséhez jelentkezzen be a Chargebee-be, és ki kell építeniőket a Chargebee-be. A Chargebee-ben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Egy másik böngészőablakban jelentkezzen be a Chargebee-be biztonsági rendszergazdaként.

2. A menü bal oldalán kattintson az **Ügyfelek** elemre, majd keresse meg **az Új ügyfél létrehozása**lapot.

    ![Freedcamp konfiguráció](./media/chargebee-tutorial/config03.png)

3. Az **Új ügyfél** lapon töltse ki az alábbi mezőket, és kattintson az **Ügyfél létrehozása** a felhasználó létrehozásához gombra.

    ![Freedcamp konfiguráció](./media/chargebee-tutorial/config04.png)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelchargebee csempéjére kattint, automatikusan be kell jelentkeznie a Chargebee-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

