---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML 1.1 Token-kompatibilis LOB-alkalmazással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAML 1.1 Token-kompatibilis LOB-alkalmazás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6de8906f15b170511c5548eeaf13173bd39a2734
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160063"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML 1.1 Token-kompatibilis LOB-alkalmazással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAML 1.1 Token-alapú LOB-alkalmazást az Azure Active Directoryval (Azure AD).
Az SAML 1.1 Token-alapú LOB-alkalmazás integrálása az Azure AD-vel a következő előnyökkel jár:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az SAML 1.1 Token-kompatibilis LOB-alkalmazáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve saml 1.1 Token engedélyezve LOB app (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az SAML 1.1 Token-kompatibilis LOB-alkalmazással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* SAML 1.1 Token engedélyezve lob app egyszeri bejelentkezésengedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* SAML 1.1 Token-kompatibilis LOB-alkalmazás támogatja az **SP** által kezdeményezett egyszeri eljárást

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>SAML 1.1 Token-kompatibilis LOB-alkalmazás hozzáadása a galériából

Az SAML 1.1 Token-alapú LOB-alkalmazás Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az SAML 1.1 Token-kompatibilis LOB-alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**SamL 1.1 Token-kompatibilis LOB-alkalmazás hozzáadása a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML 1.1 Token tokenre engedélyezett LOB-alkalmazást**, válassza az **SAML 1.1 Token képes LOB-alkalmazást** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![SAML 1.1 Token-kompatibilis LOB-alkalmazás az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az SAML 1.1 Token képes LOB-alkalmazással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat SAML 1.1 Token engedélyezve LOB-alkalmazás létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az SAML 1.1 Token-alapú LOB-alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAML 1.1 Token token engedélyezése LOB App Single Sign-On](#configure-saml-11-token-enabled-lob-app-single-sign-on)** - konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[SAML 1.1 Token lehetővé tette a LOB-alkalmazás teszt felhasználó](#create-saml-11-token-enabled-lob-app-test-user)** – egy megfelelője Britta Simon SAML 1.1 Token engedélyezett LOB-alkalmazás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az SAML 1.1 Token-kompatibilis LOB-alkalmazással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **SAML 1.1 Token engedélyezett LOB-alkalmazásalkalmazás-integrációs** lapon válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![SAML 1.1 Token engedélyezve LOB App Domain és URL egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://your-app-url`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://your-app-url`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba az SAML 1.1 Token-kompatibilis LOB-alkalmazásügyfél támogatási csapatával, hogy lekérésre ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **SAML 1.1 Token-kompatibilis LOB-alkalmazás** beállítása szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-saml-11-token-enabled-lob-app-single-sign-on"></a>Saml 1.1 Token-kompatibilis LOB-alkalmazás egyszeri bejelentkezésének konfigurálása

Konfigurálása egyszeri bejelentkezés **SAML 1.1 Token engedélyezve LOB App** oldalon, el kell küldenie a letöltött tanúsítvány **(Base64)** és a megfelelő másolt URL-eket az Azure Portalon az SAML 1.1 Token-kompatibilis LOB-alkalmazás támogatási csapat. Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t saml 1.1 token engedélyezett LOB-alkalmazás.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **SAML 1.1 Token-kompatibilis LOB-alkalmazást.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza **az SAML 1.1 Token-kompatibilis LOB-alkalmazást.**

    ![Az SAML 1.1 Token-kompatibilis LOB-alkalmazás hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-saml-11-token-enabled-lob-app-test-user"></a>SAML 1.1 Token-kompatibilis LOB-alkalmazásteszt-felhasználó létrehozása

Ebben a szakaszban hozzon létre egy felhasználó nevű Britta Simon SAML 1.1 Token engedélyezett LOB app. Együttműködve SAML 1.1 Token képes LOB App támogatási csapat a felhasználók hozzáadása az SAML 1.1 Token képes LOB App platform. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az SAML 1.1 Token-alapú LOB-alkalmazás csempéjére kattint, automatikusan be kell jelentkeznie az SAML 1.1 Token-kompatibilis LOB-alkalmazásba, amelyhez beállította az egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

