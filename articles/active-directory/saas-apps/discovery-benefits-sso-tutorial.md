---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a discovery előnyeiegyszeri bejelentkezéssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a felderítési előnyök egyszeri bejelentkezése között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72266142"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a felderítéselőnyei egyszeri bejelentkezéssel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a felderítési előnyök sso az Azure Active Directory (Azure AD). Ha integrálja a Discovery Benefits SSO-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a felderítési előnyök sso.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve discovery előnyök egyszeri bejelentkezés az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Discovery előnyök egyszeri egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Discovery előnyei SSO támogatja **IDP** által kezdeményezett SSO

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Discovery előnyeinek hozzáadása SSO a galériából

A felderítési előnyök sso-ba való integrálásának konfigurálásához hozzá kell adnia a katalógusból származó felderítési előnyök sso-t a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be a **felderítési előnyök SSO** a keresőmezőbe.
1. Válassza a **Felderítéselőnyei SSO** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a felderítési előnyök egyszeri szolgáltatása érdekében

Konfigurálja és tesztelje az Azure AD SSO-t felderítési előnyökkel rendelkező SSO használatával egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a felderítéselőnyei sso.

Az Azure AD SSO konfigurálásához és teszteléséhez a felderítési előnyök sso-val, hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a felderítéselőnyei egyszeri egyszeri](#configure-discovery-benefits-sso-sso)** egyszeri bejelentkezési beállításokat az alkalmazás oldalon.
    1. **[Discovery előnyei egyszeri bejelentkezés teszt felhasználó](#create-discovery-benefits-sso-test-user)** – a B.Simon megfelelője a felderítéselőnyei egyszeri bejelentkezés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a Felderítési előnyök egyszeri bejelentkezés **alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszerű SAML konfigurációs** szakaszban az alkalmazás előre konfigurált **IDP** kezdeményezett módban, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

1. Felderítési előnyök Egyszeri bejelentkezés alkalmazás elvárja az SAML-állítások egy adott formátumban, amely megköveteli, hogy egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

    a. Kattintson a **Szerkesztés** ikonra az **Egyedi felhasználói azonosító (Névazonosító)** párbeszédpanel megnyitásához.

    ![Felderítéselőnyei SSO-konfiguráció](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Felderítéselőnyei SSO-konfiguráció](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Kattintson a **Szerkesztés** ikonra az **Átalakítás kezelése** párbeszédpanel megnyitásához.

    c. Az **Átalakítás** mezőbe írja be az adott sorhoz megjelenített **ToUppercase()** szöveget.

    d. Az **1.** `<Name Identifier value>`

    e. Kattintson a **Hozzáadás** gombra.

    > [!NOTE]
    > A felderítési előnyök sso szükséges rögzített karakterlánc-értéket kell átadni **az egyedi felhasználói azonosító (név azonosító)** mezőben, hogy ez az integráció működik. Az Azure AD jelenleg nem támogatja ezt a funkciót, így a munka körül, **használhatja ToUpper** vagy **ToLower** átalakítása nameid beállítani egy rögzített karakterlánc-érték, amint az a képen fent látható.

    f. Automatikusan feltöltöttük az egyszeri szolgáltatás konfigurációjához (`SSOInstance` `SSOID`és a ) szükséges további jogcímeket. A **Szerkesztés** ikonnal a szervezet szerint képezheti le az értékeket.

    ![Felderítéselőnyei SSO-konfiguráció](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Felderítési előnyök beállítása sSO** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a felderítési előnyök egyszeri bejelentkezés.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Felderítési előnyök SSO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-discovery-benefits-sso-sso"></a>Felderítési előnyök sso-sso konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **felderítési előnyök egyszeri egyszeri bejelentkezési** oldalán el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról a [Discovery Benefits egyszeri bejelentkezés támogatási csapatának.](mailto:Jsimpson@DiscoveryBenefits.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-discovery-benefits-sso-test-user"></a>Felderítési előnyök létrehozása SSO-tesztfelhasználó

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon a discovery előnyei SSO. A [Discovery Benefits SSO támogatási csapatával](mailto:Jsimpson@DiscoveryBenefits.com) együttműködve adja hozzá a felhasználókat a Felderítéselőnyei SSO platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen a felderítési előnyök sso csempéjére kattint, automatikusan be kell jelentkeznie a felderítési előnyök sso-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Discovery előnyeis SSO-t az Azure AD-vel](https://aad.portal.azure.com/)

