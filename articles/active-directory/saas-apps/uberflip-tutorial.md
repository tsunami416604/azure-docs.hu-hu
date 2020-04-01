---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Uberflipkel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Uberflip között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048474"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Oktatóanyag: Az Azure Active Directory integrációja az Uberflipkel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Uberflipet az Azure Active Directoryval (Azure AD).

Az Uberflip integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Uberfliphez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek az Uberflipbe (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

A szoftver szolgáltatásként (SaaS) alkalmazásintegráció az Azure AD-vel kapcsolatos részletekért olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és az Uberflip konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Uberflip-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

Az Uberflip a következő funkciókat támogatja:

* Sp által kezdeményezett és IDP által kezdeményezett egyszeri bejelentkezés (SSO).
* Csak-in-time felhasználói kiépítés.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Uberflip hozzáadása az Azure Piactérről

Az Uberflip Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Uberflip-et az Azure Piactérről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

   ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

   ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a **+ Új alkalmazás** lehetőséget az ablaktábla tetején.

   ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be az **Uberflip**. A keresési eredmények között válassza az **Uberflip**lehetőséget, majd az Alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

   ![Uberflip az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Uberflipkel egy **B Simon**nevű tesztfelhasználó alapján. Az egyszeri bejelentkezés hez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és egy kapcsolódó felhasználó között az Uberflipben.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Uberflip segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on)** hogy a felhasználók használhassák ezt a funkciót.
1. **[Az Uberflip egyszeri bejelentkezés konfigurálásával](#configure-uberflip-single-sign-on)** konfigurálhatja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
1. **[Hozzon létre egy Uberflip tesztfelhasználót,](#create-an-uberflip-test-user)** hogy az Uberflipben egy B. Simon nevű felhasználó kapcsolódjon az Azure AD-felhasználóhoz, B. Simon néven.
1. **[Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on)** ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Uberflipkel való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Uberflip** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** ablaktáblán a ttól függően, hogy melyik Egyszeri szolgáltatás módot szeretné konfigurálni, tegye a következő lépések egyikét:

   * Az alkalmazás IDP által kezdeményezett Egyszeri bejelentkezés módban történő konfigurálásához a **Válasz URL -címe (Helyességi feltétel fogyasztói szolgáltatás URL-címe)** mezőbe írja be az URL-címet a következő minta használatával:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip domain és URL-ek egyszeri bejelentkezési információk](common/both-replyurl.png)

     > [!NOTE]
     > Ez az érték nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címével. A tényleges érték értéséhez forduljon az [Uberflip támogatási csapatához.](mailto:support@uberflip.com) Az Azure Portal **alapszintű SAML-konfigurációs** ablaktáblájában látható mintákat is hivatkozhat.

   * Az alkalmazás SP által kezdeményezett Egyszeri bejelentkezés módban történő konfigurálásához válassza **a További URL-címek beállítása**lehetőséget, és a **Bejelentkezési URL-cím** mezőbe írja be ezt az URL-címet:

     `https://app.uberflip.com/users/login`

     ![Uberflip domain és URL-ek egyszeri bejelentkezési információk](common/both-signonurl.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány szakaszában** válassza a **Letöltés** lehetőséget, ha letöltheti az **összevonási metaadat-XML-t** a megadott lehetőségekközül, és mentheti a számítógépre.

   ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. Az **Uberflip beállítása** ablaktáblán másolja a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezés i URL-címe**
   * **Azure Hirdetés-azonosító**
   * **Kijelentkezés URL-címe**

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Az Uberflip egyszeri bejelentkezéskonfigurálása

Az uberflip oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött összevonási metaadat-XML-t és a megfelelő másolt URL-címeket az Azure Portalról az [Uberflip támogatási csapatának.](mailto:support@uberflip.com) Az Uberflip csapat gondoskodik arról, hogy az SAML SSO kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B. Simon nevű tesztfelhasználót az Azure Portalon.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

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

Ebben a szakaszban lehetővé teszi B. Simon azure egyszeri bejelentkezés t engedve az Uberflip.

1. Az Azure portalon válassza az **Összes vállalati** > **alkalmazás** > **Uberflip**lehetőséget.

    ![Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza az **Uberflip**lehetőséget.

    ![Uberflip az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktáblában a **KEZELÉS csoportban**válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ Felhasználó hozzáadása**lehetőséget , majd a Hozzárendelés **hozzáadása** ablaktáblában válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza a **B Simon** elemet a **Felhasználók** listában, majd válassza a **Kijelölés** elemet az ablaktábla alján.

1. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** ablaktáblában válassza ki a felhasználó megfelelő szerepkörét a listából. Az ablaktábla alján válassza a **Kijelölés lehetőséget.**

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-an-uberflip-test-user"></a>Uberflip tesztfelhasználó létrehozása

Egy B. Simon nevű felhasználó most jön létre az Uberflip-ben. A felhasználó létrehozásához semmit sem kell tennie. Az Uberflip támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ha egy B. Simon nevű felhasználó még nem létezik az Uberflipben, a hitelesítés után egy új jön létre.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon az [Uberflip támogatási csapatához.](mailto:support@uberflip.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Amikor az **Uberflip** lehetőséget választod a My Apps portálon, automatikusan be kell jelentkezned az Uberflip-előfizetésbe, amelyhez egyszeri bejelentkezést hoztál létre. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

* [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
