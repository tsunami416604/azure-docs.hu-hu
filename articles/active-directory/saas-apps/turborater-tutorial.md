---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a TurboRaterrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TurboRater között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088270"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Oktatóanyag: Az Azure Active Directory integrációja a TurboRaterrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TurboRatert az Azure Active Directoryval (Azure AD).

A TurboRater integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TurboRaterhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek a TurboRaterbe (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

A szoftver szolgáltatásként (SaaS) alkalmazásintegráció az Azure AD-vel kapcsolatos részletekért olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a TurboRater-rel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* TurboRater-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

A TurboRater támogatja az IDP által kezdeményezett egyszeri bejelentkezést (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>TurboRater hozzáadása az Azure Piactérről

A TurboRater Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TurboRatert az Azure Piactérről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com?azure-portal=true)
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások beállítás](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a **+ Új alkalmazás** lehetőséget az ablaktábla tetején.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be a **TurboRater**. A keresési eredmények között válassza a **TurboRater**lehetőséget, majd az Alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![TurboRater az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a TurboRater-rel egy **B Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó TurboRater.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a TurboRater segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on)** hogy a felhasználók használhassák ezt a funkciót.
1. **[Konfigurálja a TurboRater egyszeri bejelentkezést](#configure-turborater-single-sign-on)** az alkalmazás oldalán az egyszeri bejelentkezési beállítások konfigurálásához.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
1. **[Hozzon létre egy TurboRater tesztfelhasználót,](#create-a-turborater-test-user)** hogy a TurboRaterben egy B. Simon nevű felhasználó kapcsolódjon az Azure AD-felhasználóhoz, B. Simon néven.
1. **[Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on)** ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a TurboRater-rel az alábbi lépésekkel:

1. Az [Azure Portalon](https://portal.azure.com/)a **TurboRater** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** ablaktáblán tegye a következő lépéseket:

    ![TurboRater tartomány és URL egyszeri bejelentkezési információk](common/idp-intiated.png)

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet:

       `https://www.itcdataservices.com`

    1. A **Válasz URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőbe írja be az URL-címet az alábbi minta használatával:

       | Környezet | URL-cím |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Élő  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval és a válasz URL-címével. Ezeknek az értékeknek a eléréséhez forduljon a [TurboRater támogatási csapatához.](https://www.getitc.com/support) Az Azure Portal **alapszintű SAML-konfigurációs** ablaktáblájában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány szakaszában** válassza a **Letöltés** lehetőséget, ha letöltheti az **összevonási metaadat-XML-t** a megadott lehetőségekközül, és mentheti a számítógépre.

    ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **TurboRater beállítása** szakaszban másolja a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezés i URL-címe**
   * **Azure Hirdetés-azonosító**
   * **Kijelentkezés URL-címe**

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>A TurboRater egyszeri bejelentkezéskonfigurálása

Az egyszeri bejelentkezés konfigurálásához a TurboRater oldalon el kell küldenie a letöltött összevonási metaadat-XML-t és a megfelelő másolt URL-címeket az Azure Portalról a [TurboRater támogatási csapatának.](https://www.getitc.com/support) A TurboRater csapat gondoskodik arról, hogy az SAML SSO kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory**   > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A Felhasználók és a "Minden felhasználó" beállítás](common/users.png)

1. A képernyő tetején válassza a **+ Új felhasználó**lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **Felhasználó** ablaktáblán tegye a következő lépéseket:

    ![A Felhasználó ablaktábla](common/user-properties.png)

    1. A **Név** mezőbe írja be a **BSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be a **BSimon\@\<\< yourcompanydomain>. kiterjesztés>. ** Például **BSimon\@contoso.com**.

    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b. Simon azure egyszeri bejelentkezés t a TurboRater hozzáférésének biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **TurboRater**lehetőséget.

    ![Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **TurboRater**lehetőséget.

    ![TurboRater az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktáblában a **KEZELÉS csoportban**válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ Felhasználó hozzáadása**lehetőséget , majd a Hozzárendelés **hozzáadása** ablaktáblában válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza a **B. Simon** elemet a **Felhasználók** listában, majd válassza a **Kijelölés** lehetőséget az ablaktábla alján.

1. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** ablaktáblában válassza ki a felhasználó megfelelő szerepkörét a listából. Az ablaktábla alján válassza a **Kijelölés lehetőséget.**

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-turborater-test-user"></a>TurboRater tesztfelhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a TurboRater ben. Működjön együtt a [TurboRater támogatási csapatával,](https://www.getitc.com/support) és adja hozzá B. Simont a TurboRater felhasználójaként. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Amikor a **TurboRater** lehetőséget választja a My Apps portálon, automatikusan be kell jelentkeznie a TurboRater előfizetésbe, amelyhez egyszeri bejelentkezést állított be. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

* [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
