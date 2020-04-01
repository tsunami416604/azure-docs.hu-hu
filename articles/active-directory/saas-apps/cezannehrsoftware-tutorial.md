---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Cezanne HR szoftverrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Cezanne HR-szoftver között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa689e6138f8d965e59f7cfa7a85e0835301086c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73158700"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Oktatóanyag: Az Azure Active Directory integrációja a Cezanne HR szoftverrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Cezanne HR-szoftvert az Azure Active Directoryval (Azure AD).
A Cezanne HR-szoftver integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Cezanne HR-szoftverhez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Cezanne HR szoftverbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Cezanne HR szoftverrel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Cezanne HR Software egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Cezanne HR szoftver támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Cezanne HR szoftver hozzáadása a galériából

A Cezanne HR-szoftver Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cezanne HR-szoftvert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha cezanne HR szoftvert szeretne hozzáadni a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Cezanne HR Software**kifejezést , válassza a **Cezanne HR Software** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Cezanne HR Szoftver az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Cezanne HR szoftverrel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a Cezanne HR szoftver közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Cezanne HR szoftverrel a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Cezanne HR szoftver egyszeri bejelentkezési](#configure-cezanne-hr-software-single-sign-on)** -, hogy konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Cezanne HR szoftver teszt felhasználó](#create-cezanne-hr-software-test-user)** - hogy egy megfelelője Britta Simon a Cezanne HR szoftver, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Cezanne HR szoftverrel hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Cezanne HR-szoftveralkalmazás-integrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Cezanne HR szoftvertartomány és URL-ek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. Az **Azonosító (entitásazonosító)** mezőbe írja be az URL-címet:`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Lépjen kapcsolatba [a Cezanne HR szoftverügyfél támogatási csapatával,](https://cezannehr.com/services/support/) hogy megkapja ezeket az értékeket.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Cezanne HR-szoftver beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurálja a Cezanne HR szoftver egyszeri bejelentkezést

1. Egy másik böngészőablakban jelentkezzen be a Cezanne HR Software-bérlőhöz rendszergazdaként.

2. A bal oldali navigációs ablakban kattintson a **Rendszer beállítása gombra.** Nyissa meg a **Biztonsági beállítások lehetőséget.** Ezután keresse meg az **Egyszeri bejelentkezés konfigurációját.**

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. A **Felhasználók bejelentkezés engedélyezése a következő Egyszeri bejelentkezés (SSO) szolgáltatás panelen** jelölje be az **SAML 2.0** jelölőnégyzetet, és válassza a **Speciális konfiguráció** lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Kattintson **az Új hozzáadása** gombra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Hajtsa végre az alábbi lépéseket az **SAML 2.0 IDENTITY PROVIDERS** című szakaszon.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Adja meg az identitásszolgáltató nevét **megjelenítendő névként.**

    b. Az **entitásazonosító** szövegdobozba illessze be az **Azure-hirdetési azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Módosítsa az **SAML kötést** "POST"-ra.

    d. A **Biztonsági jogkivonat-szolgáltatás végpontszövegbe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    e. A Felhasználói azonosító attribútumneve mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`be a mezőbe a mezőt.

    f. Kattintson a **Feltöltés** ikonra a letöltött tanúsítvány Azure Portalról való feltöltéséhez.

    g. Kattintson az **Ok** gombra.

6. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Cezanne HR-szoftverhez való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Cezanne HR-szoftver lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Cezanne HR Software lehetőséget.**

    ![A Cezanne HR szoftver hivatkozás az alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR szoftverteszt-felhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Cezanne HR szoftverbe, ki kell építeni őket a Cezanne HR szoftverbe. A Cezanne HR szoftver esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Cezanne HR Software cég honlapján, mint egy rendszergazda.

2. A bal oldali navigációs ablakban kattintson a **Rendszer beállítása gombra.** Nyissa meg a **Felhasználók kezelése**lehetőséget. Ezután keresse meg az **Új felhasználó hozzáadása ( Új felhasználó hozzáadása**) ( Új felhasználó hozzáadása ) ( Új felhasználó hozzáadása )

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Új felhasználó")

3. A **PERSON DETAILS** szakaszban hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Új felhasználó")

    a. Állítsa **a belső felhasználót** KI beállításra.

    b. Az **Utónév** mezőbe írja be a Felhasználó keresztnevét, például **Britta.**  

    c. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    d. Az **E-mail** mezőbe írja be a felhasználó Brittasimon@contoso.come-mail címét, például .

4. A **Fiókadatok** szakaszban hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Új felhasználó")

    a. A **Felhasználónév** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    b. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    c. Válassza ki **a HR Professional-t** **biztonsági szerepkörként.**

    d. Kattintson az **OK** gombra.

5. Nyissa meg **az Egyszeri bejelentkezés** lapot, és válassza az Új hozzáadása **lehetőséget** az **SAML 2.0 azonosítók** területen.

    ![Felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Felhasználó")

6. Válassza ki az identitásszolgáltatót az **identitásszolgáltatóhoz,** és a **Felhasználói azonosító**szövegmezőjében adja meg Britta Simon-fiók e-mail címét.

    ![Felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Felhasználó")

7. Kattintson a **Mentés** gombra.

    ![Felhasználó](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Felhasználó")

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Cezanne HR szoftver csempére kattint, automatikusan be kell jelentkeznie a Cezanne HR szoftverbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
