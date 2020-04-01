---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a TigerText Secure Messengerrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a TigerText Secure Messenger között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088663"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Oktatóanyag: Az Azure Active Directory integrációja a TigerText Secure Messengerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a TigerText Secure Messengert az Azure Active Directoryval (Azure AD).

A TigerText Secure Messenger integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a TigerText Secure Messengerhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek a TigerText Secure Messengerbe (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

A szoftver szolgáltatásként (SaaS) alkalmazásintegráció az Azure AD-vel kapcsolatos részletekért olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a TigerText Secure Messengerrel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* TigerText Secure Messenger-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését egy tesztkörnyezetben, és integrálhatja a TigerText Secure Messengert az Azure AD-vel.

A TigerText Secure Messenger támogatja az SP által kezdeményezett egyszeri bejelentkezést (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>TigerText Secure Messenger hozzáadása az Azure Piactérről

A TigerText Secure Messenger Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a TigerText Secure Messengert az Azure Piactérről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com?azure-portal=true)
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory beállítás](common/select-azuread.png)

1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza a **+ Új alkalmazás** lehetőséget az ablaktábla tetején.

    ![Az Új alkalmazás opció](common/add-new-app.png)

1. A keresőmezőbe írja be a **TigerText Secure Messenger**. A keresési eredmények között válassza a **TigerText Secure Messenger**lehetőséget, majd az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![TigerText Secure Messenger az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a TigerText Secure Messengerrel egy **Britta Simon**nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó tigertext biztonságos messenger.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a TigerText Secure Messenger rel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on)** hogy a felhasználók használhassák ezt a funkciót.
1. **[Konfigurálja a TigerText Secure Messenger egyszeri bejelentkezést](#configure-tigertext-secure-messenger-single-sign-on)** az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
1. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
1. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
1. **[Hozzon létre egy TigerText Secure Messenger tesztfelhasználót,](#create-a-tigertext-secure-messenger-test-user)** hogy a TigerText Secure Messengerben egy Britta Simon nevű felhasználó kapcsolódjon az Azure AD-felhasználóhoz, Britta Simon néven.
1. **[Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on)** ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a TigerText Secure Messengerrel az alábbi lépésekkel:

1. Az [Azure Portalon](https://portal.azure.com/)a **TigerText Secure Messenger** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési beállítás konfigurálása](common/select-sso.png)

1. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **egyszerű SAML-konfiguráció** ablaktábla megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Alapvető SAML-konfiguráció** ablaktáblán tegye a következő lépéseket:

    ![TigerText Secure Messenger tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    1. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:

       `https://home.tigertext.com`

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Az **azonosító (entitásazonosító)** értéke nem valós. Frissítse ezt az értéket a tényleges azonosítóval. Az érték megszerzéséhez lépjen kapcsolatba a [TigerText Secure Messenger támogatási csapatával.](mailto:prosupport@tigertext.com) Az Azure Portal **alapszintű SAML-konfigurációs** ablaktáblájában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán az **SAML aláíró tanúsítvány szakaszában** válassza a **Letöltés** lehetőséget, ha letöltheti az **összevonási metaadat-XML-t** a megadott lehetőségekközül, és mentheti a számítógépre.

    ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **TigerText Secure Messenger beállítása** csoportban másolja a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezés i URL-címe**
   * **Azure Hirdetés-azonosító**
   * **Kijelentkezés URL-címe**

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>A TigerText Secure Messenger egyszeri bejelentkezésének konfigurálása

Az egyszeri bejelentkezés konfigurálásához a TigerText Secure Messenger oldalán el kell küldenie a letöltött összevonási metaadat-XML-t és a megfelelő másolt URL-címeket az Azure Portalról a [TigerText Secure Messenger támogatási csapatának.](mailto:prosupport@tigertext.com) A TigerText Secure Messenger csapata gondoskodik arról, hogy az SAML SSO kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy britta Simon nevű tesztfelhasználót az Azure Portalon.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést, ha hozzáférést biztosít számukra a TigerText Secure Messengerhez.

1. Az Azure portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **TigerText Secure Messenger**lehetőséget.

    ![Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **TigerText Secure Messenger**lehetőséget.

    ![TigerText Secure Messenger az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktáblában a **KEZELÉS csoportban**válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ Felhasználó hozzáadása**lehetőséget , majd a Hozzárendelés **hozzáadása** ablaktáblában válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

1. A **Felhasználók és csoportok** ablaktáblán válassza **a Britta Simon** elemet a **Felhasználók** listában, majd válassza a **Kijelölés** elemet az ablaktábla alján.

1. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** ablaktáblában válassza ki a felhasználó megfelelő szerepkörét a listából. Az ablaktábla alján válassza a **Kijelölés lehetőséget.**

1. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-tigertext-secure-messenger-test-user"></a>TigerText Secure Messenger tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a TigerText Secure Messenger alkalmazásban. Együttműködve a [TigerText Secure Messenger támogatási csapatával,](mailto:prosupport@tigertext.com) hogy Britta Simont felhasználóként felvethesse a TigerText Secure Messenger programban. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Amikor a **TigerText Secure Messenger** alkalmazást választja a My Apps portálon, automatikusan be kell jelentkeznie a TigerText Secure Messenger-előfizetésbe, amelyhez egyetlen bejelentkezést állított be. A Saját alkalmazások portálról további információt az [Access és az alkalmazások használata a Saját alkalmazások portálon című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

* [A SaaS-alkalmazások Azure Active Directoryval való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
