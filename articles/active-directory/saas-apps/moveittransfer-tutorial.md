---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a MOVEit-átvitellel – Azure AD-integráció | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a MOVEit Transfer – Azure AD-integráció között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 43383e82d983c998d159728997da4757cc364999
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161322"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Oktatóanyag: Az Azure Active Directory integrációja a MOVEit Transfer - Azure AD-integrációval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a MOVEit Transfer - Azure AD-integrációt az Azure Active Directoryval (Azure AD).
A MOVEit Transfer integrálása – az Azure AD-integráció és az Azure AD a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a MOVEit Transfer - Azure AD-integrációhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a MOVEit Transfer - Azure AD integrációba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a MOVEit Transfer - Azure AD-integrációval a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* MOVEit Transfer – Az Azure AD-integráció egyszeri bejelentkezéssel rendelkező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* MOVEit Transfer - Az Azure AD-integráció támogatja az **SP** által kezdeményezett egyszeri szolgáltatást

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit-átvitel hozzáadása – Azure AD-integráció a galériából

A MOVEit Transfer - Azure AD-integráció Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a MOVEit Transfer - Azure AD-integrációt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**MovEit Transfer - Azure AD-integráció hozzáadásához hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **MOVEit Transfer - Azure AD-integráció**kifejezést, válassza **a MOVEit Transfer - Azure AD-integrációt** az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![MOVEit Transfer - Az Azure AD-integráció az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a MOVEit Transfer - Azure AD-integrációval **egy Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a MOVEit Transfer – Azure AD-integráció létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a MOVEit Transfer - Azure AD-integrációval a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a MOVEit Transfer - Azure AD integráció egyszeri bejelentkezés](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** – konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre MOVEit Transfer - Azure AD integrációs teszt felhasználó](#create-moveit-transfer---azure-ad-integration-test-user)** -, hogy egy megfelelője Britta Simon MOVEit Transfer - Azure AD-integráció, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a MOVEit Transfer – Azure AD-integrációval hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **MOVEit Transfer – Azure AD integrációs** alkalmazás integrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **Azonosító** és **a Válasz URL-értéke** automatikusan feltöltődik az **Egyszerű SAML konfiguráció szakaszban:**

    ![MOVEit-átvitel – Az Azure AD-integrációs tartomány és url-ek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://contoso.com`

    > [!NOTE]
    > A **bejelentkezési URL-érték** nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a MOVEit Transfer – Azure AD integrációs ügyfél támogatási](https://community.ipswitch.com/s/support) csapatával az érték lekért. A Szolgáltató **metaadat-fájlját** letöltheti a **Szolgáltató metaadat-URL-címéből,** amelyet később az oktatóanyag **MOVEit-átvitel konfigurálása – Azure AD-integráció egyszeri bejelentkezés** című szakaszában ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **MOVEit-átvitel beállítása – Az Azure AD-integrációs** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>MOVEit-átvitel konfigurálása – Az Azure AD-integráció egyszeri bejelentkezése

1. Jelentkezzen be a MOVEit Transfer bérlőhöz rendszergazdaként.

2. A bal oldali navigációs ablakban kattintson a **Beállítások gombra.**

    ![Beállítások szakasz az alkalmazás oldalán](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kattintson az **Egyszeri bejelentkezés** hivatkozásra, amely a **Biztonsági házirendek -> User Auth**területen található.

    ![Biztonsági házirendek az alkalmazás oldalon](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. A metaadat-dokumentum letöltéséhez kattintson a metaadat-URL-hivatkozásra.

    ![Szolgáltató metaadatok url-címe](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Ellenőrizze, hogy **az entitásazonosító** **megegyezik-e az azonosítóval** az **Egyszerű SAML konfiguráció s** ebben a szakaszban.
   * Ellenőrizze, **hogy az AssertionConsumerService** helyURL-címe megegyezik-e a **VÁLASZ URL-címével** az **Egyszerű SAML-konfiguráció szakaszban.**
    
     ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Új összevont identitásszolgáltató hozzáadásához kattintson **az Identitásszolgáltató hozzáadása** gombra.

    ![Identitásszolgáltató hozzáadása](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. A **Tallózás gombra** kattintva jelölje ki az Azure Portalról letöltött metaadatfájlt, majd a letöltött fájl feltöltéséhez kattintson az **Identitásszolgáltató hozzáadása** gombra.

    ![SAML-identitásszolgáltató](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Válassza az **"Igen**" lehetőséget **engedélyezve ként** az **Összevont identitásszolgáltató beállításainak szerkesztése...** lapon, majd kattintson a **Mentés gombra.**

    ![Összevont identitásszolgáltató beállításai](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Az **Összevont identitásszolgáltató felhasználói beállításainak szerkesztése** lapon hajtsa végre a következő műveleteket:
    
    ![Összevont identitásszolgáltató-beállítások szerkesztése](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Jelölje ki **az SAML nameID azonosítót** **bejelentkezési névként.**
    
    b. Válassza az **Egyéb** **teljes névként** lehetőséget, és az `http://schemas.microsoft.com/identity/claims/displayname` **Attribútumnév** mezőbe írja be az értéket: .
    
    c. Válassza az **Egyéb** **e-mailként lehetőséget,** és `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`az Attribútum **neve** mezőbe írja be az értéket: .
    
    d. Válassza az **Igen** automatikus **létrehozási fiók**ként lehetőséget a bejelentkezéskor .
    
    e. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a MOVEit Transfer - Azure AD-integrációhoz.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **MOVEit Transfer - Azure AD integráció lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **MOVEit Transfer - Azure AD-integráció lehetőséget.**

    ![A MOVEit Transfer - Azure AD integrációs hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit-átvitel létrehozása – Az Azure AD integrációs tesztfelhasználója

Ez a szakasz célja, hogy hozzon létre egy felhasználó nevű Britta Simon a MOVEit Transfer - Azure AD integráció. MOVEit Transfer - Az Azure AD-integráció támogatja a just-in-time kiépítése, amely engedélyezte. Ebben a szakaszban nincs műveletelem. Egy új felhasználó jön létre a MOVEit Transfer - Azure AD-integráció elérésére tett kísérlet során, ha még nem létezik.

>[!NOTE]
>Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [MOVEit Transfer - Azure AD integrációs ügyfél támogatási csapatával.](https://community.ipswitch.com/s/support)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a ACCESS Panel MOVEit Transfer - Azure AD integrációs csempéjére kattint, automatikusan be kell jelentkeznie a MOVEit Transfer - Azure AD integrációba, amelyhez beállítja az egyszeri bejelentkezést. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

