---
title: 'Oktatóanyag: Azure Active Directory integráció a ArcGIS Online-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a ArcGIS Online között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74232089"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Oktatóanyag: Azure Active Directory integráció a ArcGIS Online-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ArcGIS Online-t a Azure Active Directory (Azure AD) használatával.
A ArcGIS Online és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a ArcGIS Online-hoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a ArcGIS Online (egyszeri bejelentkezés) Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a ArcGIS Online-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* ArcGIS Online egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A ArcGIS Online támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-arcgis-online-from-the-gallery"></a>Online ArcGIS hozzáadása a katalógusból

A ArcGIS Online Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájára kell ArcGIS Online-t hozzáadnia.

**Ha online ArcGIS szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **ArcGIS Online**kifejezést, válassza a **ArcGIS Online** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ArcGIS Online az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a ArcGIS Online-hoz konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a ArcGIS Online kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a ArcGIS Online szolgáltatással való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. A **[ArcGIS Online egyszeri bejelentkezés konfigurálása](#configure-arcgis-online-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[ArcGIS-alapú online tesztelési felhasználó létrehozása](#create-arcgis-online-test-user)** – ha a ArcGIS Online-hoz tartozó Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partneri kapcsolattal rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a ArcGIS Online szolgáltatással való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **ArcGIS Online** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![ArcGIS Online tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.maps.arcgis.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez forduljon a [ArcGIS Online ügyfélszolgálati csapatához](https://support.esri.com/en/) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **ArcGIS Online-on**belüli konfiguráció automatizálásához a **bővítmény telepítése**lehetőségre kattintva telepítenie kell **az alkalmazások biztonságos bejelentkezési böngésző bővítményét** .

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítő ArcGIS Online** lehetőségre a ArcGIS Online-alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a ArcGIS Online-ba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja a **ArcGIS Online egyszeri bejelentkezés konfigurálása**szakasz lépéseit.

### <a name="configure-arcgis-online-single-sign-on"></a>ArcGIS Online egyszeri bejelentkezés konfigurálása

1. Ha manuálisan szeretné beállítani a ArcGIS Online-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a ArcGIS vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

2. Kattintson a **beállítások szerkesztése**elemre.

    ![Beállítások szerkesztése](./media/arcgis-tutorial/ic784742.png "Beállítások szerkesztése")

3. Kattintson a **Biztonság**elemre.

    ![Biztonság](./media/arcgis-tutorial/ic784743.png "Biztonság")

4. A **vállalati bejelentkezések**területen kattintson az **Identity Provider beállítása**elemre.

    ![Vállalati bejelentkezések](./media/arcgis-tutorial/ic784744.png "Vállalati bejelentkezések")

5. Az **identitás-szolgáltató** konfigurálása lapon hajtsa végre a következő lépéseket:

    ![Identitás-szolgáltató beállítása](./media/arcgis-tutorial/ic784745.png "Identitás-szolgáltató beállítása")

    a. A **név** szövegmezőbe írja be a szervezet nevét.

    b. **A vállalati identitás-szolgáltató metaadatait a használatával fogja megadni**. Válasszon ki **egy fájlt**.

    c. A letöltött metaadat-fájl feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre.

    d. Kattintson a **személyazonosság-szolgáltató beállítása**elemre.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a ArcGIS Online-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **ArcGIS Online**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza a **ArcGIS Online**lehetőséget.

    ![Az ArcGIS Online hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-arcgis-online-test-user"></a>ArcGIS Online tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a ArcGIS Online-ba, a ArcGIS Online-ban kell kiépíteni őket.  
ArcGIS Online esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **ArcGIS** -bérlőbe.

2. Kattintson a **tagok meghívása**elemre.
   
    ![Tagok meghívása](./media/arcgis-tutorial/ic784747.png "Tagok meghívása")

3. Válassza **a tagok hozzáadása automatikusan e-mail küldése nélkül**lehetőséget, majd kattintson a **tovább**gombra.
   
    ![Tagok automatikus hozzáadása](./media/arcgis-tutorial/ic784748.png "Tagok automatikus hozzáadása")

4. A **tagok** párbeszédpanelen hajtsa végre a következő lépéseket:
   
     ![Hozzáadás és felülvizsgálat](./media/arcgis-tutorial/ic784749.png "Hozzáadás és felülvizsgálat")
    
     a. Adja meg a kiépíteni kívánt érvényes Azure AD **-fiók e-mail-címét**, **utónevét**és **vezetéknevét** .
  
     b. Kattintson **a Hozzáadás és áttekintés**gombra.
5. Tekintse át a beírt adataikat, majd kattintson a **Tagok hozzáadása**lehetőségre.
   
    ![Tag hozzáadása](./media/arcgis-tutorial/ic784750.png "Tag hozzáadása")
        
    > [!NOTE]
    > A Azure Active Directory fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követve erősítse meg a fiókját, mielőtt az aktívvá válna.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a ArcGIS Online csempére kattint, automatikusan be kell jelentkeznie a ArcGIS Online-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

