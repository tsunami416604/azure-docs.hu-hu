---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091271"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Oktatóanyag: Az Azure Active Directory integrációja a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszert az Azure Active Directoryval (Azure AD).
A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadása a galériából

A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszert**, válassza a **SensoScientific wireless temperature monitoring system (SensoScientific Wireless Temperature Monitoring System)** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszert egyszeri bejelentkezéskor](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** – az alkalmazás oldalon az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer teszt felhasználó](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** - hogy egy megfelelője Britta Simon a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer, amely kapcsolódik az Azure AD képviselete a felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerrel hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tartomány és URL-ek egyszeri bejelentkezési információk](common/preintegrated.png)

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer beállítása** szakaszon másolja a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer konfigurálása egyszeri bejelentkezés

1. Jelentkezzen be a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer alkalmazásba rendszergazdaként.

1. A felső navigációs menüben kattintson a **Konfiguráció** gombra, és az **Egyszeri bejelentkezés** **csoportkonfigurálás** gombjára kattintva nyissa meg az egyszeri bejelentkezési beállításokat, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Válassza ki a Kiállító neve Azure AD.Select **Issuer Name** as Azure AD.

    b. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosítót,** amelyet az Azure Portalról másolt.

    c. Az **egyszeri bejelentkezési szolgáltatás URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    d. Az **egyszeri kijelentkezési szolgáltatás URL-címmezőjébe** illessze be a **kijelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    e. Böngésszen az Azure Portalról letöltött tanúsítvány között, és töltse fel itt.

    f. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **SensoScientific wireless temperature monitoring system (SensoScientific Wireless Temperature Monitoring System**) lehetőséget.

    ![A SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer tesztfelhasználójának létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerbe, ki kell építeni őket a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerbe. Együttműködve [SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer támogatási csapatával](https://www.sensoscientific.com/contact-us/) a felhasználók hozzáadása a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer platformján. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszer csempéjére kattint, automatikusan be kell jelentkeznie a SensoScientific vezeték nélküli hőmérséklet-figyelő rendszerbe, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

