---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az ArcGIS Online-nal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az ArcGIS Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74cf0c1e055570df7702465df79dcdfbd8ea9e9f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232089"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Oktatóanyag: Az Azure Active Directory integrációja az ArcGIS Online-nal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az ArcGIS Online-t az Azure Active Directoryval (Azure AD).
Az ArcGIS Online integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az ArcGIS Online-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az ArcGIS Online -ba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az ArcGIS Online-nal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* ArcGIS Online egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az ArcGIS Online támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-arcgis-online-from-the-gallery"></a>ArcGIS Online hozzáadása a galériából

Az ArcGIS Online Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az ArcGIS Online-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az ArcGIS Online-t a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **ArcGIS Online**kifejezést, válassza az **ArcGIS Online** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ArcGIS Online az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az ArcGIS Online-nal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó arcgis online kapcsolatát kell létrehozni.

Az ArcGIS Online-nal egyszeri bejelentkezés konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az ArcGIS Online single sign-on-t](#configure-arcgis-online-single-sign-on)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre ArcGIS Online tesztfelhasználót](#create-arcgis-online-test-user)** – ha az ArcGIS Online-ban britta Simon megfelelője kapcsolódik a felhasználó Azure AD-megjelenítéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az ArcGIS Online-nal való egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **ArcGIS Online** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![ArcGIS online tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.maps.arcgis.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az ArcGIS Online ügyféltámogatási csapatával,](https://support.esri.com/en/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **ArcGIS Online**konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Hozzáadása után kiterjesztés a böngésző, kattintson a **beállítás ArcGIS Online** irányítja, hogy az ArcGIS Online alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat az ArcGIS Online-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja az **ArcGIS Online egyszeri bejelentkezés konfigurálása**című szakasz lépéseit.

### <a name="configure-arcgis-online-single-sign-on"></a>Az arcgis online egyszeri bejelentkezéskonfigurálása

1. Ha manuálisan szeretné beállítani az ArcGIS Online-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az ArcGIS vállalati webhelyére, és hajtsa végre a következő lépéseket:

2. Kattintson **a Beállítások szerkesztése gombra.**

    ![Beállítások szerkesztése](./media/arcgis-tutorial/ic784742.png "Beállítások szerkesztése")

3. Kattintson a **Biztonság gombra.**

    ![Biztonság](./media/arcgis-tutorial/ic784743.png "Biztonság")

4. A **Vállalati bejelentkezések csoportban**kattintson az **IDENTITÁSszolgáltató beállítása gombra.**

    ![Vállalati bejelentkezések](./media/arcgis-tutorial/ic784744.png "Vállalati bejelentkezések")

5. Az **Identitásszolgáltató konfigurációjának beállítása** lapon hajtsa végre az alábbi lépéseket:

    ![Identitásszolgáltató beállítása](./media/arcgis-tutorial/ic784745.png "Identitásszolgáltató beállítása")

    a. A **Név** mezőbe írja be a szervezet nevét.

    b. A **vállalati identitásszolgáltató metaadatainak a használatával történő megadható**területen válassza az A **fájl**lehetőséget.

    c. A letöltött metaadatfájl feltöltéséhez kattintson a **Fájl kiválasztása**gombra.

    d. Kattintson **az IDENTITÁSszolgáltató beállítása gombra.**

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést az ArcGIS Online-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **ArcGIS Online lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **ArcGIS Online**lehetőséget.

    ![Az ArcGIS Online hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az ArcGIS Online-ba, ki kell építeni őket az ArcGIS Online-ba.  
Az ArcGIS Online esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be az **ArcGIS-bérlőbe.**

2. Kattintson **a TAGOK MEGHÍVÁSA gombra.**
   
    ![Tagok meghívása](./media/arcgis-tutorial/ic784747.png "Tagok meghívása")

3. Válassza **a Tagok automatikus hozzáadása e-mail küldése nélkül**lehetőséget, majd kattintson a **TOVÁBB**gombra.
   
    ![Tagok automatikus hozzáadása](./media/arcgis-tutorial/ic784748.png "Tagok automatikus hozzáadása")

4. A **Tagok** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
     ![Hozzáadás és véleményezés](./media/arcgis-tutorial/ic784749.png "Hozzáadás és véleményezés")
    
     a. Adja meg a kiépíteni kívánt érvényes Azure AD-fiók **e-mail**, **utónév**és **vezetéknév.**
  
     b. Kattintson **a Hozzáadás és véleményezés gombra.**
5. Tekintse át a beírt adatokat, majd kattintson **a TAGOK HOZZÁADÁSA gombra.**
   
    ![Tag hozzáadása](./media/arcgis-tutorial/ic784750.png "Tag hozzáadása")
        
    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és kövesse a hivatkozást, hogy erősítse meg a fiók, mielőtt aktívvá válik.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az ArcGIS Online csempére kattint, automatikusan be kell jelentkeznie az ArcGIS Online-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

