---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Riskware-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Riskware között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027116"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Oktatóanyag: Az Azure Active Directory integrációja a Riskware-rel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Riskware-t az Azure Active Directoryval (Azure AD).
A Riskware integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Riskware-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Riskware (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Riskware-rel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Kockázatmentes egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Riskware támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-riskware-from-the-gallery"></a>A Riskware hozzáadása a galériából

A Riskware azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Riskware-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Riskware-t a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a Riskware ( **Riskware**( Kockázat – az eredménypanel – Válassza a **Kockázati eszköz** – majd az Alkalmazás hozzáadásához a **Hozzáadás** gombra választógombot választógombot .

    ![Riskware az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Riskware-rel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Riskware közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Riskware-rel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Riskware Single Sign-On --hoz](#configure-riskware-single-sign-on)** konfigurálni az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre riskware teszt felhasználó](#create-riskware-test-user)** - egy megfelelője Britta Simon a Riskware, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Riskware** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![A kockázatmentes tartomány és az URL-címek egyszeri bejelentkezési információi](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:
    
    | Környezet| URL-minta|
    |--|--|
    | Felhasználói tesztelés|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | Prod| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | Demo| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Az **Azonosító (entitásazonosító)** mezőbe írja be az URL-címet:
    
    | Környezet| URL-minta|
    |--|--|
    | Felhasználói tesztelés| `https://riskcloud.net/uat` |
    | Prod| `https://riskcloud.net/prod` |
    | Demo| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > A Bejelentkezés url-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Riskware ügyféltámogatási csapatával](mailto:support@pansoftware.com.au) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Riskware beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-riskware-single-sign-on"></a>A Riskware egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Riskware vállalati webhelyére rendszergazdaként.

1. A jobb felső sarokban kattintson a **Karbantartás** gombra a karbantartási lap megnyitásához.

    ![A kockázatmentes konfigurációk karbantartása](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás lapon kattintson a **Hitelesítés gombra.**

    ![Kockázatmentes konfiguráció authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. A **Hitelesítés konfigurációja** lapon hajtsa végre a következő lépéseket:

    ![Kockázatmentes konfiguráció authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Válassza a **Típus** **SAML-ként** lehetőséget a hitelesítéshez.

    b. A **Kód** mezőbe írja be a kódot úgy, mint ha AZURE_UAT.

    c. A **Leírás** mezőbe írja be a leírást, például az AZURE-konfiguráció az sso-hoz.

    d. Az **Egyszeri bejelentkezés az oldalon** szövegmezőbe illessze be a **bejelentkezési URL-címet,** amelyet az Azure Portalról másolt.

    e. A **Kijelentkezési lap** szövegmezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    f. Az **Űrlapmező könyvelése** mezőbe írja be az SAMLResponse-hez hasonló SAML-hez hasonló SAML-t tartalmazó válaszbe írt mezőnevet.

    g. Az **XML-azonosítócímke neve** szövegmezőben írja be az attribútumot, amely az SAML-válasz egyedi azonosítóját ( NameID) tartalmazza.

    h. Nyissa meg a letöltött **metaadat-XML-t** az Azure Portalon a jegyzettömbben, másolja a tanúsítványt a metaadatfájlból, és illessze be a **Tanúsítvány** szövegmezőbe

    i. A **Fogyasztói URL-cím** szövegmezőjébe illessze be a **Válasz URL értékét,** amelyet a támogatási csoporttól kap.

    j. A **Kiállító** szövegmezőbe illessze be az **azonosító**értékét, amelyet a támogatási csoporttól kap.

    > [!Note]
    > Lépjen kapcsolatba [a Riskware ügyféltámogatási csapatával,](mailto:support@pansoftware.com.au) hogy megkapja ezeket az értékeket

    k. Jelölje be **a POST használata** jelölőnégyzetet.

    l. Jelölje be **az SAML-kérelem használata** jelölőnégyzetet.

    m. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a Riskware.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza **a Riskware**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Riskware**lehetőséget.

    ![A Riskware hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-riskware-test-user"></a>Riskware tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Riskware-be, ki kell építeni őket a Riskware-be. A Riskware-ben a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Riskware-be biztonsági rendszergazdaként.

1. A jobb felső sarokban kattintson a **Karbantartás** gombra a karbantartási lap megnyitásához. 

    ![A kockázatmentes szoftver konfigurációja karbantartást tesz le](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. A karbantartás lapon kattintson a **Kapcsolatok**gombra.

    ![Kockázatmentes konfigurációs személyek](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Válassza a **Részletek** lapot, és hajtsa végre a következő lépéseket:

    ![A kockázatmentes eszközök konfigurációjának részletei](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Válassza a **Személytípus** (Alkalmazott) lehetőséget.

    b. A **Keresztnév** mezőbe írja be a felhasználó keresztnevét, például **Britta.**

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

1. A **Biztonság** lapon hajtsa végre az alábbi lépéseket:

    ![Kockázatmentes konfiguráció biztonsága](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. A **Hitelesítés csoportban** válassza ki a **Hitelesítési** módot, amely az SSO AZURE-konfigurációja beállítást is bejelöli.

    b. A **Bejelentkezés részletei** csoport **Felhasználói azonosító** mezőjébe írja be a `brittasimon@contoso.com`felhasználó e-mail címét, például.

    c. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

1. A **Szervezet** lapon hajtsa végre az alábbi lépéseket:

    ![Riskware konfigurációs szervezet](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Válassza ki a beállítást **1.**

    b. A **Személy elsődleges munkahelye** csoport **Hely** mezőjébe írja be a tartózkodási helyét.

    c. Az **Alkalmazott csoportban** válassza **az Alkalmazott állapota** lehetőséget, mint az Alkalmi lehetőséget.

    d. Kattintson a **Mentés** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Riskware csempére kattint, automatikusan be kell jelentkeznie a Riskware-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
