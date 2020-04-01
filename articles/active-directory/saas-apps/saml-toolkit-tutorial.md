---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Azure AD SAML toolkit-tel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Azure AD SAML toolkit között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047950"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Azure AD SAML eszközkészlettel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Azure AD SAML toolkit-et az Azure Active Directoryval (Azure AD). Ha integrálja az Azure AD SAML toolkit az Azure AD, akkor:

* Az Azure AD-ben, aki hozzáfér az Azure AD SAML toolkit.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek az Azure AD SAML toolkitbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Az Azure AD SAML Toolkit egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az Azure AD SAML toolkit támogatja az **SP** által kezdeményezett sso-t
* Miután konfigurálta az Azure AD SAML toolkit kényszerítheti Session Control, amely védi a kiszivárgást és a szervezet bizalmas adatainak valós idejű beszivárgása. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Azure AD SAML eszközkészlet hozzáadása a galériából

Az Azure AD SAML Toolkit Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Azure AD SAML toolkit-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be az **Azure AD SAML toolkit** a keresőmezőbe.
1. Válassza ki **az Azure AD SAML toolkit** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az Azure AD SAML eszközkészlethez

Konfigurálja és tesztelje az Azure AD SSO-t az Azure AD SAML Toolkit segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Azure AD SAML Toolkit-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez az Azure AD SAML toolkit segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az Azure AD SAML Toolkit Egyszeri](#configure-azure-ad-saml-toolkit-sso)** egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre Az Azure AD SAML Toolkit teszt felhasználó](#create-azure-ad-saml-toolkit-test-user)** – egy megfelelője B.Simon az Azure AD SAML toolkit, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Azure AD SAML Toolkit** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    a. A **Bejelentkezés az URL-cím** mezőbe írjon be egy URL-címet:`https://samltoolkit.azurewebsites.net/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:`https://samltoolkit.azurewebsites.net`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet:`https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Raw)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

1. Az **Azure AD SAML toolkit beállítása** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t az Azure AD SAML toolkit hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Azure AD SAML Toolkit**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Az Azure AD SAML eszközkészlet sso konfigurálása

1. Nyisson meg egy új böngészőablakot, ha még nem regisztrált az Azure AD SAML Toolkit webhelyén, először kattintson a **Regisztráció**gombra. Ha már regisztrált, jelentkezzen be az Azure AD SAML Toolkit vállalati webhelyén a regisztrált bejelentkezési hitelesítő adatok használatával.

    ![Azure AD SAML eszközkészlet-regiszter](./media/saml-toolkit-tutorial/register.png)

1. Kattintson az **SAML konfigurációra**.

    ![Azure AD SAML eszközkészlet SAML konfiguráció](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kattintson **a Létrehozás gombra.**

    ![Azure AD SAML eszközkészlet SSO létrehozása](./media/saml-toolkit-tutorial/createsso.png)

1. Az **SAML SSO-konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Azure AD SAML eszközkészlet SSO létrehozása](./media/saml-toolkit-tutorial/fill-details.png)

    1. A **bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    1. Az **Azure AD-azonosító** szövegdobozba illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    1. A **Kijelentkezés URL-cím** mezőjébe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    1. Kattintson **a Fájl kiválasztása elemre,** és töltse fel az Azure Portalról letöltött **tanúsítványfájl (Raw)** fájlját.

    1. Kattintson **a Létrehozás gombra.**

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Azure AD SAML toolkit tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre az Azure AD SAML toolkit. Az Azure AD SAML Toolkit támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az Azure AD SAML Toolkit, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az Azure AD SAML toolkit csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az Azure AD SAML toolkitbe, amelyhez beállítja az sso-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Azure AD SAML eszközkészletet az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az Azure AD SAML eszközkészlet védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/protect-azure)