---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a PageDNA-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a PageDNA között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227466"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Oktatóanyag: Az Azure Active Directory integrációja a PageDNA-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PageDNA-t az Azure Active Directoryval (Azure AD).

A PageDNA integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a PageDNA-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek a PageDNA-be (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

A szoftver szolgáltatásként (SaaS) alkalmazásintegráció az Azure AD-vel kapcsolatos részletekért olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a PageDNA-nal a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* PageDNA-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését egy tesztkörnyezetben, és integrálja a PageDNA-t az Azure AD-vel.

A PageDNA a következő funkciókat támogatja:

* Sp által kezdeményezett egyszeri bejelentkezés (SSO).

* Csak-in-time felhasználói kiépítés.

## <a name="add-pagedna-from-the-azure-marketplace"></a>PageDNA hozzáadása az Azure Piactérről

A PageDNA Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a PageDNA-t az Azure Piactérről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com?azure-portal=true)
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a **+ Új alkalmazás** lehetőséget az ablaktábla tetején.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be a PageDNA ( **PageDNA**) értéket. A keresési eredmények között válassza a **PageDNA**lehetőséget, majd az Alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![PageDNA az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a PageDNA-val egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó PageDNA.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a PageDNA-nal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on)** hogy a felhasználók használhassák ezt a funkciót.
1. **[A PageDNA egyszeri bejelentkezés konfigurálásával](#configure-pagedna-single-sign-on)** konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalon.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
1. **[Hozzon létre egy PageDNA-tesztfelhasználót,](#create-a-pagedna-test-user)** hogy a PageDNA-ben egy Britta Simon nevű felhasználó kapcsolódjon az Azure AD-felhasználóhoz, Britta Simon néven.
1. **[Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on)** ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a PageDNA-nal az alábbi lépéseket kell tennie:

1. Az [Azure Portalon](https://portal.azure.com/)a **PageDNA-alkalmazásintegrációs** lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Alapvető SAML-konfiguráció** ablaktáblán tegye a következő lépéseket:

    ![PageDNA tartomány és URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    1. A **Bejelentkezési URL-cím** mezőbe írja be az URL-címet az alábbi minták egyikének használatával:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet az alábbi minták egyikének használatával:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Ezeknek az értékeknek a betöltéséhez forduljon a [PageDNA támogatási csapatához.](mailto:success@pagedna.com) Az Azure Portal **alapszintű SAML-konfigurációs** ablaktáblájában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés a** **Tanúsítvány (Raw)** letöltéséhez lehetőséget a megadott beállításokközül, és mentse a számítógépre.

    ![A Tanúsítvány (Raw) letöltési lehetőség](common/certificateraw.png)

1. A **PageDNA beállítása** csoportban másolja a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezés i URL-címe**
   * **Azure Hirdetés-azonosító**
   * **Kijelentkezés URL-címe**

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA egyszeri bejelentkezésének konfigurálása

Az egyszeri bejelentkezés beállításához küldje el a letöltött tanúsítványt (Raw) és a megfelelő másolt URL-címeket az Azure Portalról a [PageDNA támogatási csapatának.](mailto:success@pagedna.com) A PageDNA csapata gondoskodik arról, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon nevű.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory**   > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A Felhasználók és a "Minden felhasználó" beállítás](common/users.png)

1. A képernyő tetején válassza a **+ Új felhasználó**lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **Felhasználó** ablaktáblán tegye a következő lépéseket:

    ![A Felhasználó ablaktábla](common/user-properties.png)

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **a\@\<BrittaSimon yourcompanydomain>.\< kiterjesztés>. ** Például **BrittaSimon\@contoso.com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi, hogy a felhasználó Britta Simon azure egyszeri bejelentkezés tegyék lehetővé a felhasználói hozzáférést a PageDNA.In this section, you enable the user Britta Simon to use Azure single sign-on by granting the user access to PageDNA.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **PageDNA**lehetőséget.

    ![Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **PageDNA**lehetőséget.

    ![PageDNA az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktáblában a **KEZELÉS csoportban**válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ Felhasználó hozzáadása**lehetőséget , majd a Hozzárendelés **hozzáadása** ablaktáblában válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza **a Britta Simon** elemet a **Felhasználók** listában, majd válassza a **Kijelölés** elemet az ablaktábla alján.

1. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** ablaktáblában válassza ki a felhasználó megfelelő szerepkörét a listából. Az ablaktábla alján válassza a **Kijelölés lehetőséget.**

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-pagedna-test-user"></a>PageDNA-tesztfelhasználó létrehozása

A Felhasználó neve Britta Simon most jön létre PageDNA. A felhasználó létrehozásához semmit sem kell tennie. A PageDNA támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ha egy Britta Simon nevű felhasználó még nem létezik a PageDNA-ben, a hitelesítés után új jelenik meg egy új.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Amikor a **PageDNA** lehetőséget választja a Saját alkalmazások portálon, automatikusan be kell jelentkeznie a PageDNA-előfizetésbe, amelyhez egyszeri bejelentkezést állított be. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

* [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

