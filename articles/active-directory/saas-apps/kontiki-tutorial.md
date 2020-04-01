---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Kontikival | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Kontiki között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb154bce34b2ceda99b82c7ca3534b8a8ee0a1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098487"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Oktatóanyag: Az Azure Active Directory integrációja a Kontikival

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Kontiki az Azure Active Directory (Azure AD) integrálását.

A Kontiki integrálása az Azure AD-vel a következő előnyökkel jár:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Kontikihoz.
* A felhasználók automatikusan bejelentkezhetnek a Kontiki-ba az Azure AD-fiókjukkal (egyszeri bejelentkezés).
* A fiókokat egy központi helyen, az Azure Portalon kezelheti.

A szoftverszolgáltatásként (SaaS) alkalmazásintegrációaz Azure AD-vel való integrációjáról az [Azure Active Directory ban lévő alkalmazásokegyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Kontikival a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Kontiki-előfizetés egyszeri bejelentkezésengedélyezve.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését egy tesztkörnyezetben, és integrálhatja a Kontiki-t az Azure AD-vel.

A Kontiki a következő szolgáltatásokat támogatja:

* **Sp által kezdeményezett egyszeri bejelentkezés**
* **Just-in-time felhasználói kiépítés**

## <a name="add-kontiki-in-the-azure-portal"></a>Kontiki hozzáadása az Azure-portálon

A Kontiki integrálása az Azure AD-vel, hozzá kell adnia a Kontiki-t a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali menüben válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be **a Kontiki**. A keresési eredmények között válassza a **Kontiki**lehetőséget, majd a **Hozzáadás**lehetőséget.

    ![Kontiki az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Kontikival egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó Kontikiban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Kontiki val, a következő építőelemeket kell végrehajtania:

| Tevékenység | Leírás |
| --- | --- |
| **[Az Azure AD egyszeri bejelentkezéskonfigurálása](#configure-azure-ad-single-sign-on)** | Lehetővé teszi a felhasználók számára a szolgáltatás használatát. |
| **[Kontiki egyszeri bejelentkezés konfigurálása](#configure-kontiki-single-sign-on)** | Az alkalmazás egyszeri bejelentkezési beállításainak konfigurálása. |
| **[Azure AD-tesztfelhasználó létrehozása](#create-an-azure-ad-test-user)** | Teszteli az Azure AD egyszeri bejelentkezést egy Britta Simon nevű felhasználó számára. |
| **[Az Azure AD-tesztfelhasználó hozzárendelése](#assign-the-azure-ad-test-user)** | Lehetővé teszi Britta Simon számára az Azure AD egyszeri bejelentkezését. |
| **[Kontiki tesztfelhasználó létrehozása](#create-a-kontiki-test-user)** | Britta Simon megfelelőjét hozza létre Kontikiban, amely a felhasználó Azure AD-ábrázolásához kapcsolódik. |
| **[Az egyszeri bejelentkezés tesztelése](#test-single-sign-on)** | Ellenőrzi, hogy a konfiguráció működik-e. |

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban konfigurálja az Azure AD egyszeri bejelentkezésk kontiki az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Kontiki alkalmazásintegrációs** ablaktáblán válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza **az SAML** vagy az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** ablaktábla Bejelentkezés az **URL-címén** mezőbe írjon be egy URL-címet, amely a következő mintát követi:`https://<companyname>.mc.eval.kontiki.com`

    ![Kontiki tartomány és URL egyszeri bejelentkezési információk](common/sp-signonurl.png)

    > [!NOTE]
    > Lépjen kapcsolatba a [Kontiki ügyfél támogatási csapatával,](https://customersupport.kontiki.com/enterprise/contactsupport.html) hogy megkapja a megfelelő értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány szakaszában** válassza a **Letöltés** lehetőséget az **összevonási metaadat-XML**mellett. Válasszon egy letöltési lehetőséget az igényei nek megfelelően. Mentse a tanúsítványt a számítógépre.

    ![Az összevonási metaadat-XML-tanúsítvány letöltési lehetősége](common/metadataxml.png)

1. A **Kontiki beállítása** szakaszban másolja a következő URL-címeket az Ön igényei nek megfelelően:

    * Bejelentkezési URL
    * Azure Hirdetés-azonosító
    * Kijelentkezés URL-címe

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a Kontiki oldalon küldje el a letöltött összevonási metaadat-XML-fájlt és az Azure Portalról másolt megfelelő URL-címeket a [Kontiki támogatási csapatának.](https://customersupport.kontiki.com/enterprise/contactsupport.html) A Kontiki támogatási csapata az ön által küldött adatokat használja annak biztosítására, hogy az SAML egyszeri bejelentkezési kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A Felhasználók és az Összes felhasználó beállítás](common/users.png)

1. Válassza az **Új felhasználó**lehetőséget.

    ![Az Új felhasználó beállítás](common/new-user.png)

1. A **Felhasználó** ablaktáblán hajtsa végre az alábbi lépéseket:

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **a\@\<brittasimon\< your-company-domain>. kiterjesztés>. ** Például **a\@brittasimon contoso.com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

    ![A Felhasználó ablaktábla](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít Britta Simon nak Kontikihoz, hogy használhassa az Azure egyszeri bejelentkezést.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **Kontiki**lehetőséget.

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **Kontiki**lehetőséget.

    ![Kontiki az alkalmazások listájában](common/all-applications.png)

1. A menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A Felhasználók és csoportok beállítás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** ablaktáblán válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza a Felhasználók listájában a **Britta Simon** elemet. Válassza a **Kiválasztás** lehetőséget

1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** ablaktáblában válassza ki a megfelelő szerepkört a felhasználó számára a listából. Válassza a **Kiválasztás** lehetőséget

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-kontiki-test-user"></a>Kontiki tesztfelhasználó létrehozása

Nincs műveletelem, amely konfigurálhatja a felhasználók kiépítését a Kontikiban. Amikor egy hozzárendelt felhasználó megpróbál bejelentkezni a Kontiki-ba a Saját alkalmazások portálon keresztül, a Kontiki ellenőrzi, hogy a felhasználó létezik-e. Ha nem talál felhasználói fiókot, a Kontiki automatikusan létrehozza a felhasználói fiókot.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Miután beállította az egyszeri bejelentkezést, amikor a **Kontiki** lehetőséget választja a Saját alkalmazások portálon, automatikusan bejelentkezik a Kontikiba. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon](../user-help/my-apps-portal-end-user-access.md)talál.

## <a name="next-steps"></a>További lépések

További információért tekintse át az alábbi cikkeket:

- [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
