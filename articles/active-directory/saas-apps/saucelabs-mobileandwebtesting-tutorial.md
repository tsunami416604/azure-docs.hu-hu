---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Sauce Labs- mobil- és webes teszteléssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sauce Labs – Mobile és webtesztelés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091559"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Oktatóanyag: Az Azure Active Directory integrációja a Sauce Labs-szel – Mobil- és webes tesztelés

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sauce Labs – Mobil- és webes tesztelést az Azure Active Directoryval (Azure AD).
A Sauce Labs integrálása – A mobil- és webes tesztelés az Azure AD-vel a következő előnyökkel jár:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Sauce Labs – Mobil- és webes teszteléshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve sauce Labs - Mobil és webes tesztelés (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Sauce Labs – Mobil- és webes teszteléssel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Sauce Labs - Mobil és webes tesztelés egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Sauce Labs - Mobil és webes tesztelés támogatja **IDP** kezdeményezett SSO
* Sauce Labs - Mobil és webes tesztelés támogatja **just in time** felhasználói kiépítése

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Hozzáadása Sauce Labs - Mobil és webes tesztelés a galériában

A Sauce Labs – Mobile and Web Testing azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Sauce Labs – Mobile and Web Testing alkalmazást a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Sauce Labs - Mobile and Web Testing hozzáadásához hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Sauce Labs - Mobile and Web Testing (Mártás labs – mobil-** és webes tesztelés – – továbbkeresés – – mártás – **mobil- és webes tesztelés** ) kifejezést, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

    ![Sauce Labs - Mobil és webes tesztelés az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Sauce Labs – Mobile és Web Testing szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó sauce Labs – mobil és webes tesztelés közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Sauce Labs – Mobile és Web Testing szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Sauce Labs - Mobile and Web Testing Single Sign-On](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** --t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre sauce Labs - Mobil és webes tesztelés teszt felhasználó](#create-sauce-labs---mobile-and-web-testing-test-user)** -, hogy egy megfelelője Britta Simon sauce Labs - Mobil és webes tesztelés, amely kapcsolódik az Azure AD képviselete a felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Sauce Labs – Mobile és Web Testing szolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure portalon](https://portal.azure.com/)a **Sauce Labs – Mobile and Web Testing** alkalmazásintegrációs lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

    ![Sauce Labs - Mobil és web tesztelés domain és URL-ek egyszeri bejelentkezési információk](common/preintegrated.png)

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Sauce Labs - Mobile és web tesztelése szakaszban** másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurálja Sauce Labs - Mobil és webes tesztelés Single Sign-On

1. Egy másik böngészőablakban jelentkezzen be a Sauce Labs – Mobile and Web Testing cég webhelyére rendszergazdaként.

2. Kattintson a **Felhasználó ikonra,** és válassza **a Csapatkezelés** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Írja be **a tartománynevét** a szövegmezőbe.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Kattintson a **Beállítás** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Az **Egyszeri bejelentkezés konfigurálása** csoportban hajtsa végre a következő lépéseket.

    ![Egyszeri bejelentkezés konfigurálása](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kattintson **a Tallózás** gombra, és töltse fel a letöltött metaadat-fájlt az Azure AD-ből.

    b. Jelölje be a **JUST-IN-TIME KIÉPÍTÉS ENGEDÉLYEZÉSE** jelölőnégyzetet.

    c. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést a Sauce Labs – Mobile és Web Testing hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a Sauce Labs – Mobile and Web **Testing lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza **a Sauce Labs - Mobile and Web Testing lehetőséget.**

    ![A Sauce Labs - Mobil és webes tesztelés link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Szószlabs létrehozása - Mobil és webes tesztelés teszt felhasználó

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Sauce Labs - Mobile és web tesztelés. Sauce Labs - Mobil és webes tesztelés támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Sauce Labs – Mobil és webes tesztelés, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a Sauce Labs - Mobile and Web Testing támogatási csapatához.](mailto:support@saucelabs.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Sauce Labs – Mobile and Web Testing csempére kattint, automatikusan be kell jelentkeznie a Sauce Labs - Mobile and Web Testing programba, amelyhez beállítja az Egyszeri bejelentkezés t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

