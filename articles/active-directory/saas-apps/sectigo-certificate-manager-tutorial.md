---
title: 'Oktatóanyag: Azure Active Directory integráció a Sectigo tanúsítványkezelővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Sectigo tanúsítványkezelő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67588237"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Oktatóanyag: Azure Active Directory integráció a Sectigo tanúsítványkezelővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Sectigo Tanúsítványkezelőt Azure Active Directory (Azure AD) használatával.

A Sectigo tanúsítványkezelő és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Sectigo Tanúsítványkezelőhöz.
* A felhasználók automatikusan bejelentkezhetnek a Sectigo Tanúsítványkezelőbe az Azure AD-fiókjával (egyszeri bejelentkezéssel).
* A fiókokat egy központi helyen, a Azure Portal is kezelheti.

További információ az Azure AD-vel való szolgáltatott szoftveres (SaaS) alkalmazások integrálásáról: [egyszeri bejelentkezés a Azure Active Directory lévő alkalmazásokba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a Sectigo tanúsítványkezelővel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) a Kezdés előtt.
* Sectigo tanúsítványkezelő előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az Sectigo-Tanúsítványkezelőt az Azure AD-vel.

A Sectigo tanúsítványkezelő a következő funkciókat támogatja:

* **Az SP által kezdeményezett egyszeri bejelentkezés**
* **IDENTITÁSSZOLGÁLTATÓ – kezdeményezett egyszeri bejelentkezés**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>A Sectigo-tanúsítványkezelő hozzáadása a Azure Portal

A Sectigo tanúsítványkezelő Azure AD-val való integrálásához hozzá kell adnia a Sectigo Tanúsítványkezelőt a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza a **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **Sectigo tanúsítványkezelő**kifejezést. A keresési eredmények között válassza a **Sectigo tanúsítványkezelő**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    ![Sectigo-tanúsítványkezelő az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Sectigo Certificate Managerrel egy **Britta Simon**nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a Sectigo Tanúsítványkezelőben.

Az Azure AD egyszeri bejelentkezés a Sectigo tanúsítványkezelővel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a funkció használatát. |
| **[A Sectigo-tanúsítványkezelő egyszeri bejelentkezésének konfigurálása](#configure-sectigo-certificate-manager-single-sign-on)** | Az egyszeri bejelentkezési beállításokat konfigurálja az alkalmazásban. |
| **[Azure AD-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** | Lehetővé teszi a Britta Simon számára az Azure AD egyszeri bejelentkezés használatát. |
| **[Sectigo-tanúsítványkezelő tesztelési felhasználó létrehozása](#create-a-sectigo-certificate-manager-test-user)** | A létrehoz egy, a felhasználó Azure AD-képviseletéhez kapcsolódó Britta Simon-t a Sectigo Tanúsítványkezelőben. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja a Sectigo Tanúsítványkezelőben a Azure Portal.

1. A [Azure Portal](https://portal.azure.com/) **Sectigo tanúsítványkezelő** alkalmazás-integráció paneljén válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. **Az egyszeri bejelentkezés** engedélyezéséhez válassza az **SAML** vagy az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezéshez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri bejelentkezés az SAML-vel** panelen való beállítása lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** panelen, a *identitásszolgáltató-kezdeményezésű mód*konfigurálásához hajtsa végre a következő lépéseket:

    1. Az **azonosító** mezőben adja meg az alábbi URL-címek egyikét:
       * https:\//CERT-Manager.com/shibboleth
       * https:\//Hard.CERT-Manager.com/shibboleth

    1. A **Válasz URL-címe** mezőbe írja be az alábbi URL-címek egyikét:
        * https:\//CERT-Manager.com/Shibboleth.SSO/SAML2/post
        * https:\//Hard.CERT-Manager.com/Shibboleth.SSO/SAML2/post

    1. Válassza a **további URL-címek beállítása**lehetőséget.

    1. A **továbbítási állapot** mezőben adja meg az alábbi URL-címek egyikét:
       * https:\//CERT-Manager.com/Customer/SSLSupport/IDP
       * https:\//Hard.CERT-Manager.com/Customer/SSLSupport/IDP

    ![Sectigo tanúsítványkezelő tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-relay.png)

1.  Az alkalmazás *SP-kezdeményezésű módban*való konfigurálásához hajtsa végre a következő lépéseket:

    * A **bejelentkezési URL-cím** mezőbe írja be az alábbi URL-címek egyikét:
      * https:\//CERT-Manager.com/Shibboleth.SSO/login
      * https:\//Hard.CERT-Manager.com/Shibboleth.SSO/login

      ![Sectigo tanúsítványkezelő tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-signonurl.png)

1. Az **egyszeri bejelentkezés SAML-panelen való beállításához** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** a tanúsítvány mellett **(Base64)** elemet. A követelmények alapján válasszon egy letöltési lehetőséget. Mentse a tanúsítványt a számítógépére.

    ![A tanúsítvány (Base64) letöltési lehetősége](common/certificatebase64.png)

1. A **Sectigo tanúsítványkezelő beállítása** szakaszban a követelmények alapján másolja a következő URL-címeket:

    * Bejelentkezési URL
    * Azure AD-azonosító
    * Kijelentkezési URL-cím

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>A Sectigo-tanúsítványkezelő egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a Sectigo tanúsítványkezelő oldalon szeretné konfigurálni, küldje el a letöltött tanúsítvány (Base64) fájlját és a Azure Portalból a [Sectigo Certificate Manager támogatási csapatához](https://sectigo.com/support)másolt releváns URL-címeket. A Sectigo tanúsítványkezelő támogatási csapata az Ön által küldött adatokat használja annak biztosítására, hogy az SAML egyszeri bejelentkezési kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

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

Ebben a szakaszban Simon Britta-hozzáférést biztosít a Sectigo tanúsítványkezelőnek, így az Azure egyszeri bejelentkezést is használhatja.

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás** > **Sectigo a Tanúsítványkezelő**elemet.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **Sectigo tanúsítványkezelő**elemet.

    ![Sectigo-tanúsítványkezelő az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**elemet. Ezután a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a felhasználók listájában. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-állításban a szerepkör értékét várja, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. Válassza a **Kiválasztás** lehetőséget

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Sectigo-tanúsítványkezelő tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Sectigo Tanúsítványkezelőben. Működjön együtt a [Sectigo tanúsítványkezelő támogatási csapatával](https://sectigo.com/support) , és vegye fel a felhasználót a Sectigo tanúsítványkezelő platformba. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Miután beállította az egyszeri bejelentkezést, a saját alkalmazások portálján a **Sectigo tanúsítványkezelő** lehetőség kiválasztásával automatikusan bejelentkezik a Sectigo tanúsítványkezelőbe. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket:

- [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


