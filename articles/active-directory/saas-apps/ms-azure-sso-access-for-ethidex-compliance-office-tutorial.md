---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az MS Azure SSO Access for Ethidex Compliance Office-szal™ | Microsoft dokumentumok'
description: Megtudhatja, hogy miként konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az MS Azure SSO Access között az Ethidex Compliance Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "70844366"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az MS Azure Egyszeri bejelentkezés az Ethidex Compliance Office számára™

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az MS Azure SSO Access for Ethidex Compliance Office™ az Azure Active Directoryval (Azure AD). Ha integrálja az MS Azure SSO Access for Ethidex Compliance Office™-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá az MS Azure SSO Access for Ethidex Compliance Office™.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezzenek az MS Azure SSO Access for Ethidex Compliance Office-ba™ az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* MS Azure SSO Access for Ethidex Compliance Office™ egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* MS Azure SSO Access for Ethidex Compliance Office™ támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>MS Azure SSO Access hozzáadása az Ethidex Compliance Office-hoz™ a galériából

Az MS Azure SSO Access for Ethidex Compliance Office™ azure AD-be való integrálásának konfigurálásához hozzá kell adnia az MS Azure SSO Access for Ethidex Compliance Office-t™ a gyűjteményből a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be az **MS Azure SSO Access for Ethidex Compliance Office™** a keresőmezőbe.
1. Válassza az **MS Azure SSO Access for Ethidex Compliance Office™** az eredménypanelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az EThidex Compliance Office MS Azure SSO Access szolgáltatásához™

Konfigurálja és tesztelje az Azure AD SSO-t az MS Azure SSO Access for Ethidex Compliance Office szolgáltatással™ egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az MS Azure SSO Access for Ethidex Compliance Office™.For SSO to Work, you need to establish a link relationship between a Azure AD user and the related user in MS Azure SSO Access for Ethidex Compliance Office™.

Az Azure AD SSO konfigurálásához és teszteléséhez az MS Azure SSO Access for Ethidex Compliance Office™ szolgáltatással hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az MS Azure SSO Access for Ethidex Compliance Office SSO-t](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Ms Azure SSO Access az Ethidex Compliance Office tesztfelhasználójához](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** – hogy b.Simon megfelelője legyen az MS Azure SSO Access for Ethidex Compliance Office-ban™ amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **MS Azure SSO Access for Ethidex Compliance Office™** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`com.ethidex.prod.<CLIENTID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az MS Azure SSO Access for Ethidex Compliance Office ™ támogatási csapatával,](mailto:support@ethidex.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. MS Azure SSO Access for Ethidex Compliance Office™ alkalmazásalkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. MS Azure SSO Access for Ethidex Compliance Office™ alkalmazás elvárja, hogy **a névazonosító** le legyen képezve **a user.mail-lel,** ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Raw)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **MS Azure SSO Access beállítása az Ethidex Compliance Office ™** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az MS Azure Egyszeri bejelentkezés hez az Ethidex Compliance Office™.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **MS Azure SSO Access for Ethidex Compliance Office™**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO Access konfigurálása az Ethidex Compliance Office SSO-hoz

Az **EThidex Compliance Office-hoz való** egyszeri bejelentkezés konfigurálásához az Ethidex Compliance Office ™ oldalán el kell küldenie a letöltött **tanúsítványt (Raw)** és a megfelelő másolt URL-címeket az Azure Portalról az [MS Azure SSO Access for Ethidex Compliance Office™ támogatási csapat számára.](mailto:support@ethidex.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>MS Azure SSO Access létrehozása az Ethidex Compliance Office tesztfelhasználójához

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre az MS Azure SSO Access for Ethidex Compliance Office™.-ban. Együttműködve [MS Azure SSO Access for Ethidex Compliance Office™ támogatási csapat segítségével](mailto:support@ethidex.com) adja hozzá a felhasználókat az MS Azure SSO Access for Ethidex Compliance Office™ platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az Ethidex Compliance Office-hoz való MS Azure SSO Access csempére kattint™ a hozzáférési panelen, automatikusan be kell jelentkeznie az Ethidex Compliance Office MS Azure SSO Access szolgáltatásába™ amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az MS Azure SSO Access for Ethidex Compliance Office-t™ az Azure AD-vel](https://aad.portal.azure.com/)

