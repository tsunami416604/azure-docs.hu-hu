---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Segítővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Segítő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5f42e4d7-4d92-4096-a0d5-02fa438a5dfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d70cdade93b028c90c9f62374cd6b997556dd52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101285"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Oktatóanyag: A segítő integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Segítőt az Azure Active Directoryval (Azure AD). Ha integrálja a Segítőt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Helper Helper.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a Segítőbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Helper egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Helper támogatja az **SP és az IDP** által kezdeményezett egyszeri bejelentkezést, és támogatja **a Just In Time** felhasználói kiépítést.

## <a name="adding-helper-helper-from-the-gallery"></a>Segítő hozzáadása a galériából

A Helper Helper Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a kartárból a felügyelt SaaS-alkalmazások listájához a Segítő segítőt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **Segítő beírását** a keresőmezőbe.
1. Válassza **a Segítő lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD-sSO-t a Segítővel egy **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Segítőben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Segítővel hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[A Segítő konfigurálásával](#configure-helper-helper)** konfigurálja az SSO-beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre helper teszt felhasználó,](#create-helper-helper-test-user)** hogy egy megfelelője B. Simon a segítő, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Segítő** alkalmazás integrációja lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha **a** **Szolgáltató metaadatfájlja** van, és **idp** által kezdeményezett módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    >[!NOTE]
    >Lépjen az `https://sso.helperhelper.com/saml/<customer_id>` URL-címre a Szolgáltató metaadatfájljának leéséhez. Lépjen kapcsolatba [a Helper Helper ügyféltámogatási csapatával.](mailto:info@helperhelper.com) `<customer_id>`

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    c. A metaadatfájl sikeres feltöltése után az **Azonosító** és a **Válasz URL-értékei** automatikusan feltöltődnek az Egyszerű SAML-konfiguráció szakaszban.

    > [!Note]
    > Ha az **azonosító** és a **válasz URL-értékei** nem kapnak automatikus polulated, majd töltse ki az értékeket manuálisan a követelménynek megfelelően.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték lekérti a [Helper Helper Ügyféltámogatási csapatát.](mailto:info@helperhelper.com) Az Azure Portal.l **Alapszintű SAML-konfiguráció** szakaszában látható mintákra is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és a jegyzettömbbe való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. A **Segítő beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Segítő konfigurálása

A **Segítő** oldalán való egyszeri bejelentkezés konfigurálásához el kell küldenie az **App Federation metaadat-címét** a [Segítő segítő támogatási csapatának.](mailto:info@helperhelper.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi B. Simon azure egyszeri bejelentkezés t a Helper Helper hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Segítő segítő**lehetőséget .
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-helper-helper-test-user"></a>Segítő segítő tesztfelhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Segítő ben. A Helper Helper támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Helper Helper, egy új jön létre a hitelesítés után.

### <a name="test-sso"></a>SSO tesztelése

Amikor a Hozzáférési panelen kiválasztja a Segítő csempét, automatikusan be kell jelentkeznie a segítőhöz, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)