---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Recognize | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Felismerés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: c0a513ad8fe772839813615d065616f2c911a031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943339"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Oktatóanyag: Az Azure Active Directory integrációja az Recognize szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Felismerés t az Azure Active Directoryval (Azure AD).
Az Recognize with Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy kinek van hozzáférése az Recognize szolgáltatáshoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezzenek az Recognize (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció felismeréssel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Egyszeri bejelentkezéssel rendelkező előfizetés felismerése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A felismerés támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-recognize-from-the-gallery"></a>Felismerés hozzáadása a galériából

Az Recognize integrálásának konfigurálásához az Azure AD-be, hozzá kell adnia a galéria felismerését a felügyelt SaaS-alkalmazások listájához.

**Ha a gyűjteményből szeretné hozzáadni az Recognize funkciót, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Felismerés**, az Eredménypanel **Felismerés parancsát,** majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

     ![Felismerés az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Recognize szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat felismerése kell létrehozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az egyszeri bejelentkezés felismerése](#configure-recognize-single-sign-on)** beállítást - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre felismerési teszt felhasználó](#create-recognize-test-user)** - egy megfelelője Britta Simon a Recognize, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Recognize szolgáltatással való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az Alkalmazásintegráció **felismerése** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **a Szolgáltató metaadatfájlja**található, hajtsa végre az alábbi lépéseket:

    >[!NOTE]
    >A Szolgáltató **metaadat-fájlját** az oktatóanyag **Egyszeri bejelentkezés felismerésének konfigurálása** szakaszából kapja meg.

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. A metaadatfájl sikeres feltöltése után az **azonosító** értéke automatikusan feltöltődik az egyszerű SAML-konfiguráció szakaszban.

    ![A tartomány és az URL-címek egyszeri bejelentkezési adatainak felismerése](common/sp-identifier.png)

     A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Ha az **azonosító** értéke nem lesz automatikusan feltöltve, akkor az azonosító értékét úgy kapja meg, hogy megnyitja a Szolgáltató metaadat-URL-címét az sso-beállítások szakaszban, amelyet később, az **oktatóanyag Egyszeri bejelentkezés felismerése** című szakaszában ismertetünk ki. A bejelentkezési URL-érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [az ügyfél támogatási csapatával](mailto:support@recognizeapp.com) az érték lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Beállítási felismerés** csoportban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-recognize-single-sign-on"></a>Egyszeri bejelentkezés felismerése konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az Recognize tenant-be.

2. A jobb felső sarokban kattintson a **Menü gombra.** Nyissa meg a **Vállalati rendszergazda lehetőséget.**
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/recognize-tutorial/tutorial_recognize_000.png)

3. A bal oldali navigációs ablakban kattintson a **Beállítások gombra.**
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Hajtsa végre az alábbi lépéseket az **SSO-beállítások** szakaszban.
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Az **SSO engedélyezése mezőben**válassza **a BE**lehetőséget.

    b. Az **IDP-entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.
    
    c. Az **Sso cél URL-szövegmezőbe** illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.
    
    d. A **Slo cél URL-szövegmezőbe** illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt. 
    
    e. Nyissa meg a letöltött **tanúsítványfájlt (Base64)** a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a **Tanúsítvány** szövegmezőbe.
    
    f. Kattintson a **Beállítások mentése** gombra. 

5. Az **SSO Settings** szakasz mellett másolja az URL-t a **Szolgáltató metaadatai url-cím alá.**
   
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Nyissa meg a **metaadat-URL-hivatkozást** egy üres böngésző alatt a metaadat-dokumentum letöltéséhez. Ezután másolja az EntityDescriptor értékét (entityID) a fájlból, és illessze be **az Azure** Portal **alapszintű SAML-konfigurációjának** azonosító szövegmezőjébe.
    
    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon](./media/recognize-tutorial/tutorial_recognize_004.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező brittasimon@yourcompanydomain.extensiontípusa mezőben. Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít az recognize.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd kattintson a **Felismerés gombra.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Felismerés**lehetőséget.

    ![Az Alkalmazások listájában található Felismerés hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-recognize-test-user"></a>Felismerési tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az Recognize-ba, ki kell építeni őket az Recognize-ba. Felismerés esetén a kiépítés manuális feladat.

Ez az alkalmazás nem támogatja az SCIM-kiépítést, de van egy másik felhasználói szinkronizálás, amely a felhasználók számára. 

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a vállalati webhely felismerése.

2. A jobb felső sarokban kattintson a **Menü gombra.** Nyissa meg a **Vállalati rendszergazda lehetőséget.**

3. A bal oldali navigációs ablakban kattintson a **Beállítások gombra.**

4. Hajtsa végre az alábbi lépéseket a **Felhasználószinkronizálás** szakaszban.
   
    ![Új felhasználó](./media/recognize-tutorial/tutorial_recognize_005.png "Új felhasználó")
   
    a. A **Szinkronizálás engedélyezve**lehetőséget válassza **a BE**lehetőséget.
   
    b. A **Szinkronizálásszolgáltató kiválasztása csoportban**válassza a **Microsoft / Office 365**lehetőséget.
   
    c. Kattintson **a Felhasználószinkronizálás futtatása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Felismerés csempére kattint, automatikusan be kell jelentkeznie arra az felismerésre, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

