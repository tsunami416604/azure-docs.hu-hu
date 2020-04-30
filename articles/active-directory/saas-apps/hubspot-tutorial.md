---
title: 'Oktatóanyag: Azure Active Directory integráció a HubSpot-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és HubSpot között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68944449"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Oktatóanyag: Azure Active Directory integráció a HubSpot

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a HubSpot a Azure Active Directory (Azure AD) szolgáltatással.

A HubSpot és az Azure AD integrálásával az alábbi előnyökkel jár:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a HubSpot.
* A felhasználók automatikusan bejelentkezhetnek a HubSpot az Azure AD-fiókjával (egyszeri bejelentkezéssel).
* A fiókokat egy központi helyen, a Azure Portal is kezelheti.

További információ az Azure AD-vel való szolgáltatott szoftveres (SaaS) alkalmazások integrálásáról: [egyszeri bejelentkezés a Azure Active Directory lévő alkalmazásokba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció HubSpot való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.
* Egyszeri bejelentkezést használó HubSpot-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az HubSpot-t az Azure AD-vel.

A HubSpot a következő funkciókat támogatja:

* **Az SP által kezdeményezett egyszeri bejelentkezés**
* **IDENTITÁSSZOLGÁLTATÓ – kezdeményezett egyszeri bejelentkezés**

## <a name="add-hubspot-in-the-azure-portal"></a>HubSpot hozzáadása a Azure Portal

A HubSpot és az Azure AD integrálásához hozzá kell adnia a HubSpot a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **HubSpot**kifejezést. A keresési eredmények között válassza a **HubSpot**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![HubSpot az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az HubSpot-mel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a HubSpot-ben.

Az Azure AD egyszeri bejelentkezés HubSpot való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a funkció használatát. |
| **[HubSpot egyszeri bejelentkezés konfigurálása](#configure-hubspot-single-sign-on)** | Az egyszeri bejelentkezési beállításokat konfigurálja az alkalmazásban. |
| **[Azure AD-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** | Lehetővé teszi a Britta Simon számára az Azure AD egyszeri bejelentkezés használatát. |
| **[HubSpot-teszt felhasználó létrehozása](#create-a-hubspot-test-user)** | Létrehoz egy, a felhasználó Azure AD-képviseletéhez kapcsolódó Britta Simon-t a HubSpot-ben. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja a Azure Portal HubSpot.

1. Az [Azure Portal](https://portal.azure.com/) **HubSpot** alkalmazás-integráció paneljén válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. **Az egyszeri bejelentkezés** engedélyezéséhez válassza az **SAML** vagy az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezéshez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri bejelentkezés az SAML-vel** panelen való beállítása lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** panelen, a *identitásszolgáltató-kezdeményezésű mód*konfigurálásához hajtsa végre a következő lépéseket:

    1. Az **azonosító** mezőbe írjon be egy URL-címet, amely a következő mintával rendelkezik\/: https:\</API.HubSpot.com/login-API/v1/SAML/login?portalId\>= Customer id.

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet, amely a következő mintával rendelkezik:\<https:\>\//API.HubSpot.com/login-API/v1/SAML/ACS?portalId = Customer id.

    ![HubSpot tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    > [!NOTE]
    > Az URL-címek formázásához tekintse meg a Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintákat is.

1. Az alkalmazás konfigurálása *SP-kezdeményezésű* módban:

    1. Válassza a **további URL-címek beállítása**lehetőséget.

    1. A **bejelentkezési URL-cím** mezőbe írja be a **https\/:/app.HubSpot.com/login**értéket.

    ![A további URL-címek beállítása lehetőség](common/metadata-upload-additional-signon.png)

1. Az **egyszeri bejelentkezés SAML-panelen való beállításához** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** a tanúsítvány mellett **(Base64)** elemet. A követelmények alapján válasszon egy letöltési lehetőséget. Mentse a tanúsítványt a számítógépére.

    ![A tanúsítvány (Base64) letöltési lehetősége](common/certificatebase64.png)

1. A **HubSpot beállítása** szakaszban másolja a következő URL-címeket a követelmények alapján:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL-cím

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a HubSpot rendszergazdai fiókjába.

1. Válassza a **Beállítások** ikont az oldal jobb felső sarkában.

    ![A beállítások ikon a HubSpot](./media/hubspot-tutorial/config1.png)

1. Válassza a **fiók Alapértelmezések**lehetőséget.

    ![A fiók alapértékei beállítás a HubSpot](./media/hubspot-tutorial/config2.png)

1. Görgessen le a **Biztonság** szakaszhoz, majd válassza a **beállítás**lehetőséget.

    ![A beállítás beállítása a HubSpot](./media/hubspot-tutorial/config3.png)

1. Az **egyszeri bejelentkezés beállítása** szakaszban hajtsa végre a következő lépéseket:

    1. A **célközönség URl-címe (szolgáltatói entitás azonosítója)** mezőben válassza a **Másolás** lehetőséget az érték másolásához. A Azure Portal az **alapszintű SAML-konfiguráció** panelen illessze be az értéket az **azonosító** mezőbe.

    1. A **bejelentkezési URL-cím, ACS, címzett vagy átirányítás** mezőben válassza a **Másolás** lehetőséget az érték másolásához. A Azure Portal az **alapszintű SAML-konfiguráció** panelen illessze be az értéket a **Válasz URL-címe** mezőbe.

    1. A HubSpot-ben az azonosító **szolgáltató azonosítója vagy a kiállító URL-címe** mezőben illessze be a Azure Portalba másolt **Azure ad-azonosító** értékét.

    1. A HubSpot-ben az **identitás-szolgáltató egyszeri bejelentkezési URL-címe** mezőben illessze be a Azure Portalba másolt **bejelentkezési URL-cím** értékét.

    1. A Windows Jegyzettömb alkalmazásban nyissa meg a letöltött tanúsítvány (Base64) fájlt. Válassza ki és másolja ki a fájl tartalmát. Ezután a HubSpot illessze be az **X. 509-tanúsítvány** mezőbe.

    1. Válassza az **Ellenőrzés** lehetőséget.

        ![Az egyszeri bejelentkezés beállítása szakasz a HubSpot-ben](./media/hubspot-tutorial/config4.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.

    ![A felhasználók és az összes felhasználó lehetőség](common/users.png)

1. Válassza az **új felhasználó**lehetőséget.

    ![Az új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be **a brittasimon\@\<-vállalat-tartomány>.\< bővítmény\>**. Például **brittasimon\@contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a **jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

    ![A felhasználó panel](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban Simon Britta-hozzáférést biztosít a HubSpot, így az Azure egyszeri bejelentkezést is használhatja.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás** > **HubSpot**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **HubSpot**lehetőséget.

    ![HubSpot az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**elemet. Ezután a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-állításban a szerepkör értékét várja, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Válassza a **Kiválasztás** lehetőséget

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-hubspot-test-user"></a>HubSpot-teszt felhasználó létrehozása

Ha engedélyezni szeretné, hogy az Azure AD-felhasználó bejelentkezzen a HubSpot, a felhasználónak a HubSpot-ben kell kiépíteni. A HubSpot-ben a kiépítés manuális feladat.

Felhasználói fiók kiépítése a HubSpot-ben:

1. Jelentkezzen be a HubSpot vállalati webhelyre rendszergazdaként.

1. Válassza a **Beállítások** ikont az oldal jobb felső sarkában.

    ![A beállítások ikon a HubSpot](./media/hubspot-tutorial/config1.png)

1. Válassza a **felhasználók & csapatok**lehetőséget.

    ![A felhasználók & csapatok lehetőség a HubSpot](./media/hubspot-tutorial/user1.png)

1. Válassza a **felhasználó létrehozása**lehetőséget.

    ![A felhasználó létrehozása lehetőség a HubSpot](./media/hubspot-tutorial/user2.png)

1. Az **e-mail addess (ek) hozzáadása** mezőben adja meg a felhasználó e-mail-címét a következő formátumban:\@brittasimon contoso.com, majd kattintson a **tovább**gombra.

    ![Az e-mail cím (ek) hozzáadása a HubSpot felhasználók létrehozása szakaszában](./media/hubspot-tutorial/user3.png)

1. A **felhasználók létrehozása** szakaszban válassza ki az egyes lapokat. Az egyes lapokon állítsa be a megfelelő beállításokat és engedélyeket a felhasználó számára. Ezután válassza a **tovább**lehetőséget.

    ![A HubSpot felhasználók létrehozása szakaszának lapjai](./media/hubspot-tutorial/user4.png)

1. A meghívás a felhasználónak való elküldéséhez válassza a **Küldés**lehetőséget.

    ![A Küldés lehetőség a HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > A felhasználó akkor aktiválódik, ha a felhasználó elfogadja a meghívást.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Miután beállította az egyszeri bejelentkezést, a **HubSpot** kiválasztása után a saját alkalmazások portálon automatikusan bejelentkezik a HubSpot. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket:

- [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
