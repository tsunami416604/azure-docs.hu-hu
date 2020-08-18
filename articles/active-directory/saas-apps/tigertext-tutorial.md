---
title: 'Oktatóanyag: Azure Active Directory integráció a TigerText Secure Messengerrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a TigerText Secure Messenger között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9dff60767e923bad1322b689acd98e69eb9c2ac6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516979"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Oktatóanyag: Azure Active Directory integráció a TigerText Secure Messengerrel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a TigerText Secure Messengert Azure Active Directory (Azure AD) használatával.

A TigerText Secure Messenger és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a TigerText Secure Messengerhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a biztonságos TigerText (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Az Azure AD-vel való szolgáltatott szoftverek (SaaS) alkalmazással kapcsolatos részletekért tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció TigerText Secure Messengerrel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Egy TigerText biztonságos Messenger-előfizetés egyszeri bejelentkezéssel.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben konfigurálja és teszteli, és integrálja az TigerText Secure Messengert az Azure AD-vel.

A TigerText Secure Messenger támogatja az SP által kezdeményezett egyszeri bejelentkezést (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>TigerText Secure Messenger hozzáadása az Azure Marketplace-ről

A TigerText Secure Messenger Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a TigerText Secure Messengert az Azure Marketplace-ről a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com?azure-portal=true).
1. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.

    ![Az Azure Active Directory lehetőség](common/select-azuread.png)

1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

1. Új alkalmazás hozzáadásához válassza az **+ új alkalmazás** elemet a panel tetején.

    ![Az új alkalmazás lehetőség](common/add-new-app.png)

1. A keresőmezőbe írja be a **TigerText Secure Messenger**kifejezést. A keresési eredmények között válassza a **TigerText Secure Messenger**elemet, majd válassza a **Hozzáadás** lehetőséget az alkalmazás hozzáadásához.

    ![TigerText Secure Messenger az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az TigerText Secure Messengerrel konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a TigerText Secure Messengerben.

Az Azure AD egyszeri bejelentkezés TigerText Secure Messengerrel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on)** engedélyezheti a felhasználók számára a funkció használatát.
1. **[Konfigurálja a TigerText Secure Messenger egyszeri bejelentkezést](#configure-tigertext-secure-messenger-single-sign-on)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
1. **[Hozzon létre egy TigerText Secure Messenger-teszt felhasználót](#create-a-tigertext-secure-messenger-test-user)** , hogy a Britta Simon nevű felhasználó a TigerText Secure Messengerben, aki a Britta Simon nevű Azure ad-felhasználóhoz van társítva.
1. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a TigerText Secure Messengerrel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **TigerText Secure Messenger** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési lehetőség konfigurálása](common/select-sso.png)

1. Az egyszeri bejelentkezési **módszer kiválasztása** ablaktáblán válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

1. Az **egyszeri bejelentkezés az SAML-vel** panelen való beállítása lapon válassza a **Szerkesztés** (a ceruza ikon) lehetőséget az **alapszintű SAML-konfiguráció** panel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépéseket:

    ![TigerText Secure Messenger domain és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    1. A **bejelentkezési URL** -cím mezőbe írjon be egy URL-címet:

       `https://home.tigertext.com`

    1. Az **azonosító (entitás azonosítója)** mezőbe írja be az URL-címet a következő minta használatával:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Az **azonosító (Entity ID)** érték nem valós. Frissítse ezt az értéket a tényleges azonosítóval. Az érték beszerzéséhez forduljon a [TigerText Secure Messenger támogatási csapatához](mailto:prosupport@tigertext.com). A Azure Portal **alapszintű SAML-konfiguráció** paneljén látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-panelen való beállításához** az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget, hogy letöltse az **összevonási metaadatok XML-** fájlját a megadott beállításokból, és mentse a számítógépre.

    ![Az összevonási metaadatok XML-letöltési lehetősége](common/metadataxml.png)

1. A **TigerText biztonságos Messenger beállítása** szakaszban másolja ki a szükséges URL-címet vagy URL-címeket:

   * **Bejelentkezési URL-cím**
   * **Azure AD-azonosító**
   * **Kijelentkezési URL-cím**

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>A TigerText Secure Messenger egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a biztonságos TigerText szeretné konfigurálni, el kell küldenie a letöltött összevonási metaadatokat tartalmazó XML-fájlt és a megfelelő másolt URL-címeket a Azure Portal a [TigerText Secure Messenger támogatási csapatának](mailto:prosupport@tigertext.com). A TigerText Secure Messenger csapata gondoskodni fog arról, hogy az SAML SSO-kapcsolatok mindkét oldalon megfelelően legyenek beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy Britta Simon nevű teszt felhasználót hoz létre a Azure Portal.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**    >  **felhasználók**  >  **minden felhasználó**lehetőséget.

    ![A felhasználók és a "minden felhasználó" lehetőség](common/users.png)

1. A képernyő felső részén válassza az **+ új felhasználó**lehetőséget.

    ![Új felhasználói beállítás](common/new-user.png)

1. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    ![A felhasználó panel](common/user-properties.png)

    1. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    1. A **Felhasználónév** mezőbe írja be a **BrittaSimon \@ \<yourcompanydomain> . \<extension> **. Például **BrittaSimon \@ contoso.com**.

    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít számukra a TigerText Secure Messengerhez.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **TigerText Secure Messenger**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **TigerText Secure Messenger**elemet.

    ![TigerText Secure Messenger az alkalmazások listájában](common/all-applications.png)

1. A bal oldali ablaktábla **kezelés**területén válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" lehetőség](common/users-groups-blade.png)

1. Válassza a **+ felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

1. A **felhasználók és csoportok** panelen válassza a **Britta Simon** elemet a **felhasználók** listában, majd válassza a **kiválasztás** elemet a panel alján.

1. Ha az SAML-állításban a szerepkör értékét várja, akkor a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a listáról a felhasználó számára. A panel alján válassza a **kiválasztás**lehetőséget.

1. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>TigerText biztonságos Messenger-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a TigerText Secure Messengerben. Működjön együtt a [TigerText Secure Messenger támogatási csapatával](mailto:prosupport@tigertext.com) , és adja hozzá a Britta Simon felhasználót a TigerText Secure Messengerben. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Ha a saját alkalmazások portálon a **TigerText biztonságos Messenger** elemet választja, akkor automatikusan be kell jelentkeznie a TigerText Secure Messenger-előfizetésbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [alkalmazások elérése és használata a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
