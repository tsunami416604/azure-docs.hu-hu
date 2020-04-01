---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a SilkRoad Life Suite programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a SilkRoad Life Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090824"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Oktatóanyag: Az Azure Active Directory integrációja a SilkRoad Life Suite programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a SilkRoad Life Suite szolgáltatást az Azure Active Directoryval (Azure AD).
A SilkRoad Life Suite és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a SilkRoad Life Suite-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a SilkRoad Life Suite (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a SilkRoad Life Suite csomaggal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* SilkRoad Life Suite egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A SilkRoad Life Suite támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>A SilkRoad Life Suite hozzáadása a galériából

A SilkRoad Life Suite Azure AD-be való integrálásához hozzá kell adnia a SilkRoad Life Suite-ot a galériából a felügyelt SaaS-alkalmazások listájához.

**Ha a SilkRoad Life Suite-ot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SilkRoad Life Suite**kifejezést, válassza a **SilkRoad Life Suite** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SilkRoad Life Suite az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a SilkRoad Life Suite szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Az egyszeri bejelentkezés hez létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SilkRoad Life Suite-ban.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a SilkRoad Life Suite szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a SilkRoad Life Suite Single Sign-On](#configure-silkroad-life-suite-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SilkRoad Life Suite tesztfelhasználót](#create-silkroad-life-suite-test-user)** - hogy britta Simon megfelelője legyen a SilkRoad Life Suite-ban, amely kapcsolódik a felhasználó Azure AD-megjelenítéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a SilkRoad Life Suite csomaggal hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **SilkRoad Life Suite** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    > [!NOTE]
    > Az oktatóanyag későbbi részében ismertetjük a **Szolgáltató metaadatfájlját.**

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![image](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![image](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **azonosító** és a **válasz URL-értéke** automatikusan feltöltődik az Egyszerű SAML konfiguráció szakaszban:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Ha az **azonosító** és a **válasz URL-értékei** nem kerülnek automatikus polulated, majd töltse ki az értékeket manuálisan a követelménynek megfelelően.

    d. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. Az **Egyszerű SAML-konfiguráció** szakaszban, ha nem rendelkezik **a Szolgáltató metaadatfájljával, hajtsa**végre az alábbi lépéseket:

    ![SilkRoad Life Suite Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a SilkRoad Life Suite ügyféltámogatási csapatával,](https://www.silkroad.com/locations/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

7. A **SilkRoad Life Suite beállítása** szakaszon másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurálja a SilkRoad Life Suite egyszeri bejelentkezést

1. Jelentkezzen be a SilkRoad vállalati webhelyére rendszergazdaként.

    > [!NOTE]
    > A SilkRoad Life Suite hitelesítési alkalmazáshoz való hozzáféréshez a Microsoft Azure AD-vel való összevonás konfigurálásához forduljon a SilkRoad támogatási szolgálatához vagy a SilkRoad Services képviselőjéhez.

1. Nyissa meg a **Szolgáltató**t, majd kattintson **az Összevonás részletei gombra.**

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Kattintson **az Összevonási metaadatok letöltése gombra,** majd mentse a metaadatfájlt a számítógépre. A Letöltött összevonásmetaadatok használata **szolgáltatói metaadatfájlként** az Azure Portal **Basic SAML Configuration** szakaszában.

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. A **SilkRoad** alkalmazásban kattintson a **Hitelesítési források elemre.**

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Kattintson **a Hitelesítési forrás hozzáadása gombra.**

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. A **Hitelesítési forrás hozzáadása csoportban** hajtsa végre az alábbi lépéseket:

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. A **2.** **Browse**
  
    b. Kattintson **az Identitásszolgáltató létrehozása fájladatok kal gombra.**

1. A **Hitelesítési források csoportban** kattintson a **Szerkesztés gombra.**

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. A **Hitelesítési forrás szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. **Engedélyezve ként**válassza az **Igen**lehetőséget.

    b. Az **EntityId** szövegdobozba illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **IdP-leírás** mezőbe írja be a konfiguráció leírását (például: *Azure AD SSO).*

    d. A **Metaadat-fájl** szövegmezőben töltse fel az Azure Portalról letöltött **metaadatfájlt.**
  
    e. Az **IdP-név** mezőbe írjon be egy, a konfigurációra jellemző nevet (például: *Azure SP).*
  
    f. A **Kijelentkezési szolgáltatás URL-címmezőjébe** illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    g. A **Bejelentkezési szolgáltatás URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    h. Kattintson a **Mentés** gombra.

1. Tiltsa le az összes többi hitelesítési forrást.

    ![Az Azure AD egyszeri bejelentkezése](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a SilkRoad Life Suite-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **SilkRoad Life Suite**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **SilkRoad Life Suite lehetőséget.**

    ![A SilkRoad Life Suite link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-silkroad-life-suite-test-user"></a>Hozzon létre SilkRoad Life Suite tesztfelhasználót

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a SilkRoad Life Suite alkalmazásban. Működjön együtt a [SilkRoad Life Suite ügyféltámogatási csapatával,](https://www.silkroad.com/locations/) és adja hozzá a felhasználókat a SilkRoad Life Suite platformhoz. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Access Panelen a SilkRoad Life Suite csempére kattint, automatikusan be kell jelentkeznie a SilkRoad Life Suite-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
