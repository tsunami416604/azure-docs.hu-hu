---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Procore SSO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Procore egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093667"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Oktatóanyag: Az Azure Active Directory integrációja a Procore SSO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Procore SSO-t az Azure Active Directoryval (Azure AD).
A Procore SSO integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Procore SSO-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve procore egyszeri bejelentkezés (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció procore sso-val való konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Procore egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Procore SSO támogatja az **IDP** által kezdeményezett SSO-t

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO hozzáadása a galériából

A Procore SSO azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a Procore SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha procore sso-t szeretne hozzáadni a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Procore SSO**kifejezést , válassza a **Procore SSO** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Procore SSO az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Procore egyszeri bejelentkezéssel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a procore egyszeri bejelentkezés kapcsolódó felhasználó közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Procore egyszeri bejelentkezéssel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Procore Egyszeri bejelentkezést](#configure-procore-sso-single-sign-on)** – az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Procore Egyszeri bejelentkezés tesztfelhasználó](#create-procore-sso-test-user)** létrehozása – britta Simon megfelelője a Procore egyszeri bejelentkezésben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének procore egyszeri bejelentkezéssel való konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Procore Egyszeri bejelentkezés** alkalmazás integrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![Procore Egyszeri bejelentkezés tartomány és URL-címek egyszeri bejelentkezési információi](common/preintegrated.png)

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Procore SSO beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-procore-sso-single-sign-on"></a>Procore egyszeri bejelentkezés konfigurálása

1. A **Procore egyszeri** bejelentkezési oldalon való egyszeri bejelentkezés konfigurálásához jelentkezzen be a procore vállalati webhelyére rendszergazdaként.

2. Az eszköztár legördülő menüjének **adminisztrátora** elemre kattintva nyissa meg az SSO-beállítások lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_tool_admin.png)

3. Illessze be az értékeket az alábbi mezőkbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Az **egyszeri bejelentkezés a kiállító URL-cím mezőjébe** illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. Az **SAML Sign On Target URL-címmezőbe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. Most nyissa meg a fent az Azure portalról letöltött **összevonási metaadat-XML-t,** és másolja a tanúsítványt az **X509Certificate nevű címkébe.** Illessze be a másolt értéket az **Egyszeri bejelentkezés x509 tanúsítvány** mezőbe.

4. Kattintson a **Save Changes** gombra.

5. Ezek után a beállítások után el kell küldenie azt a **tartománynevet** (pl. **contoso.com),** amelyen keresztül bejelentkezik a Procore-ba a [Procore támogatási csapatának,](https://support.procore.com/) és aktiválni fogják az adott tartományösszevont SSO-t.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Procore egyszeri bejelentkezéshez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Procore SSO**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Procore SSO**lehetőséget.

    ![A Procore SSO hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-procore-sso-test-user"></a>Procore SSO-tesztfelhasználó létrehozása

Kérjük, kövesse az alábbi lépéseket, hogy hozzon létre egy Procore teszt felhasználó Procore SSO oldalon.

1. Jelentkezzen be a procore vállalati webhelyére rendszergazdaként.    

2. Az eszköztár legördülő menüjéről kattintson a **Címtár** elemre a vállalati címtárlap megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kattintson a **Személy hozzáadása** opcióra az űrlap megnyitásához és a következő lehetőségek végrehajtásához -

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_add.png)

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **Britta**.

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, **például Simon**.

    c. Az **E-mail cím** mezőbe írja be BrittaSimon@contoso.coma felhasználó e-mail címét, például .

    d. Válassza az **Engedélysablon** t **engedélysablonként később.**

    e. Kattintson **a Létrehozás gombra.**

4. Ellenőrizze és frissítse az újonnan hozzáadott kapcsolattartó adatait.

    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_check.png)

5. Kattintson a **Meghívó mentése és küldése** (ha e-mailen keresztül idvan rá hívás szükséges) vagy a **Mentés** (Mentés közvetlenül) gombra a felhasználói regisztráció befejezéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Procore SSO csempére kattint, automatikusan be kell jelentkeznie a Procore sso-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

