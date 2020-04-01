---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Sectigo Tanúsítványkezelővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sectigo Certificate Manager között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588237"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Oktatóanyag: Az Azure Active Directory integrációja a Sectigo Tanúsítványkezelővel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sectigo Tanúsítványkezelőt az Azure Active Directoryval (Azure AD).

A Sectigo Tanúsítványkezelő integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Sectigo Tanúsítványkezelőhöz.
* A felhasználók automatikusan bejelentkezhetnek a Sectigo Tanúsítványkezelőbe az Azure AD-fiókjukkal (egyszeri bejelentkezés).
* A fiókokat egy központi helyen, az Azure Portalon kezelheti.

A szoftverszolgáltatásként (SaaS) alkalmazásintegrációaz Azure AD-vel való integrációjáról az [Azure Active Directory ban lévő alkalmazásokegyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció sectigo tanúsítványkezelővel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Sectigo Certificate Manager-előfizetés egyszeri bejelentkezésengedélyezve.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését egy tesztkörnyezetben, és integrálhatja a Sectigo Tanúsítványkezelőt az Azure AD-vel.

A Sectigo Certificate Manager a következő szolgáltatásokat támogatja:

* **Sp által kezdeményezett egyszeri bejelentkezés**
* **IDP által kezdeményezett egyszeri bejelentkezés**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Sectigo tanúsítványkezelő hozzáadása az Azure Portalon

A Sectigo Tanúsítványkezelő azure AD-vel való integrálásához hozzá kell adnia a Sectigo Tanúsítványkezelőt a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali menüben válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be a **Sectigo Tanúsítványkezelő**t. A keresési eredmények között válassza a **Sectigo Tanúsítványkezelő**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![Sectigo tanúsítványkezelő az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Sectigo Certificate Managerrel egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó sectigo tanúsítványkezelőben.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Sectigo Certificate Manager rel a következő építőelemeket kell végrehajtania:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezéskonfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a szolgáltatás használatát. |
| **[Sectigo tanúsítványkezelő egyszeri bejelentkezésének konfigurálása](#configure-sectigo-certificate-manager-single-sign-on)** | Az alkalmazás egyszeri bejelentkezési beállításainak konfigurálása. |
| **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Lehetővé teszi Britta Simon számára az Azure AD egyszeri bejelentkezését. |
| **[Sectigo tanúsítványkezelő tesztfelhasználójának létrehozása](#create-a-sectigo-certificate-manager-test-user)** | Britta Simon megfelelője a Sectigo Tanúsítványkezelőben, amely a felhasználó Azure AD-ábrázolásához kapcsolódik. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban konfigurálhatja az Azure AD egyszeri bejelentkezéssectigo tanúsítványkezelő az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Sectigo Certificate Manager alkalmazásintegrációs** ablaktáblán válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza **az SAML** vagy az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** ablaktáblában az *IDP által kezdeményezett mód*konfigurálásához hajtsa végre az alábbi lépéseket:

    1. Az **Azonosító** mezőbe írja be az alábbi URL-címek egyikét:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. A **Válasz URL-címe** mezőbe írja be az alábbi URL-címek egyikét:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Válassza **a További URL-címek beállítása**lehetőséget.

    1. A **Továbbítási állapot** mezőbe írja be az alábbi URL-címek egyikét:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Tanúsítványkezelő tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-relay.png)

1.  Az alkalmazás *SP által kezdeményezett módban történő*konfigurálásához hajtsa végre az alábbi lépéseket:

    * A **Bejelentkezési URL-cím** mezőbe írja be az alábbi URL-címek egyikét:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Tanúsítványkezelő tartomány és URL-címek egyszeri bejelentkezési adatai](common/both-signonurl.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés** lehetőséget a **Tanúsítvány (Base64)** mellett. Válasszon egy letöltési lehetőséget az igényei nek megfelelően. Mentse a tanúsítványt a számítógépre.

    ![A Tanúsítvány (Base64) letöltési lehetőség](common/certificatebase64.png)

1. A **Sectigo tanúsítványkezelő beállítása** szakaszban másolja a következő URL-címeket az Ön igényei nek megfelelően:

    * Bejelentkezési URL
    * Azure Hirdetés-azonosító
    * Kijelentkezés URL-címe

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sectigo tanúsítványkezelő egyszeri bejelentkezésének konfigurálása

Az egyszeri bejelentkezés konfigurálásához a Sectigo Tanúsítványkezelő oldalán küldje el a letöltött tanúsítványfájlt (Base64) és az Azure Portalról másolt megfelelő URL-címeket a [Sectigo Tanúsítványkezelő támogatási csapatának.](https://sectigo.com/support) A Sectigo Tanúsítványkezelő támogatási csapata az elküldött adatok at használja annak biztosítására, hogy az SAML egyszeri bejelentkezési kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A Felhasználók és az Összes felhasználó beállítás](common/users.png)

1. Válassza az **Új felhasználó**lehetőséget.

    ![Az Új felhasználó beállítás](common/new-user.png)

1. A **Felhasználó** ablaktáblán hajtsa végre az alábbi lépéseket:

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **a\@\<brittasimon\< your-company-domain>. kiterjesztés.\>** Például **a\@brittasimon contoso.com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

    ![A Felhasználó ablaktábla](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít Britta Simonnak a Sectigo Tanúsítványkezelőhöz, hogy használhassa az Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **Sectigo Tanúsítványkezelő**lehetőséget.

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **Sectigo Tanúsítványkezelő lehetőséget.**

    ![Sectigo tanúsítványkezelő az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A Felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** ablaktáblán válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza a Felhasználók listájában a **Britta Simon** elemet. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** ablaktáblában válassza ki a megfelelő szerepkört a felhasználó számára a listából. Válassza a **Kiválasztás** lehetőséget

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Sectigo tanúsítványkezelő tesztfelhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Sectigo Tanúsítványkezelőben. Együttműködve a [Sectigo Tanúsítványkezelő támogatási csapatával](https://sectigo.com/support) a felhasználó sectigo tanúsítványkezelő platformon való hozzáadásához. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Az egyszeri bejelentkezés beállítása után, amikor a **Sectigo Tanúsítványkezelő** t választja a My Apps portálon, automatikusan bejelentkezik a Sectigo Tanúsítványkezelőbe. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md)talál.

## <a name="next-steps"></a>További lépések

További információért tekintse át az alábbi cikkeket:

- [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


