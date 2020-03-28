---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az OfficeSpace szoftverrel | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OfficeSpace Software között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80712c7f59845287006c1699524573c6094498b3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561711"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az OfficeSpace szoftverrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OfficeSpace szoftvert az Azure Active Directoryval (Azure AD). Ha integrálja az OfficeSpace szoftvert az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá az OfficeSpace szoftverhez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezhessenek az OfficeSpace Szoftverbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* OfficeSpace Software egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az OfficeSpace szoftver támogatja az **SP** által kezdeményezett sso-t


* Az OfficeSpace szoftver támogatja **a Just In Time** felhasználói kiépítést


## <a name="adding-officespace-software-from-the-gallery"></a>OfficeSpace szoftver hozzáadása a galériából

Az OfficeSpace Szoftver Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az OfficeSpace szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **OfficeSpace Szoftver** kifejezést a keresőmezőbe.
1. Válassza az **OfficeSpace Szoftver** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az OfficeSpace Software alkalmazáshoz

Konfigurálja és tesztelje az Azure AD SSO-t az OfficeSpace szoftverrel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az OfficeSpace Szoftverben.

Az Azure AD SSO OfficeSpace szoftverrel való konfigurálásához és teszteléséhez hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az OfficeSpace Software SSO-t](#configure-officespace-software-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre OfficeSpace Szoftver teszt felhasználó](#create-officespace-software-test-user)** - hogy egy megfelelője B.Simon az OfficeSpace szoftver, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **OfficeSpace Software** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`<company name>.officespacesoftware.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az OfficeSpace szoftverügyfél támogatási csapatával,](mailto:support@officespacesoftware.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az OfficeSpace Software alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. Az OfficeSpace Software alkalmazás elvárja, hogy a **névazonosító** t appleképezve legyen **user.mail,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. A fentieken kívül az OfficeSpace Software alkalmazás arra számít, hogy néhány további attribútum ot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmény nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | név | user.displayname |
    | first_name | user.givenname |
    | last_name | user.vezetéknév |

1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja a **hüvelykujj értékét,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

1. Az **OfficeSpace szoftver beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az OfficeSpace szoftverhez.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **OfficeSpace Software lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="configure-officespace-software-sso"></a>Officespace szoftverstso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az OfficeSpace Software bérlőjéhez.

2. Nyissa meg a **Beállítások lapot,** és kattintson **az Összekötők gombra.**

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Kattintson **az SAML-hitelesítés gombra.**

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Az **SAML-hitelesítés** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. A **Kijelentkezésszolgáltató URL-címmezőjébe** illessze be a **Kijelentkezés URL-címének** értékét, amelyet az Azure Portalról másolt.

    b. Az **ügyfél idp cél URL-szövegmezőbe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Illessze be az Azure Portalról másolt **ujjlenyomat-értéket** az **Ügyfél IDP-tanúsítvány ujjlenyomatának** szövegmezőjébe. 

    d. Kattintson **a Beállítások mentése gombra.**

### <a name="create-officespace-software-test-user"></a>OfficeSpace szoftverteszt-felhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre az OfficeSpace Szoftverben. Az OfficeSpace szoftver támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik az OfficeSpace Szoftverben, a hitelesítés után új felhasználó jön létre.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie [az OfficeSpace szoftvertámogatási csapatával.](mailto:support@officespacesoftware.com)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az OfficeSpace Szoftver csempére kattint, automatikusan be kell jelentkeznie az OfficeSpace szoftverbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az OfficeSpace szoftvert az Azure AD-vel](https://aad.portal.azure.com/)

