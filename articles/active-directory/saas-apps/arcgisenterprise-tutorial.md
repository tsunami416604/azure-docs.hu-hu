---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az ArcGIS Enterprise vállalattal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az ArcGIS Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e21b1c72f191f3644975afd511a900667a04ce9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73157896"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Oktatóanyag: Az Azure Active Directory integrációja az ArcGIS Enterprise vállalattal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az ArcGIS Enterprise-t az Azure Active Directoryval (Azure AD).
Az ArcGIS Enterprise és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az ArcGIS Enterprise-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az ArcGIS Enterprise (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az ArcGIS Enterprise-szal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* ArcGIS Enterprise egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.



* Az ArcGIS Enterprise támogatja az **SP és az IDP** által kezdeményezett sso-t
* Az ArcGIS Enterprise támogatja **a Just In Time** felhasználói kiépítést


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Az ArcGIS Enterprise hozzáadása a galériából

Az ArcGIS Enterprise Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az ArcGIS Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az ArcGIS Enterprise-t a katalógusból szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **ArcGIS Enterprise ( ArcGIS Enterprise**) kifejezést, válassza az **ArcGIS Enterprise** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![ArcGIS Enterprise az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az [Alkalmazás név] szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó [Alkalmazás neve] létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az [Alkalmazás neve] beállításához a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az ArcGIS Enterprise Single Sign-On --t](#configure-arcgis-enterprise-single-sign-on)** az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre ArcGIS Enterprise tesztfelhasználó](#create-arcgis-enterprise-test-user)** - egy megfelelője Britta Simon az ArcGIS Enterprise, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az [Alkalmazás név] segítségével hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **ArcGIS Enterprise** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket, ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni:

    ![Az ArcGIS Enterprise Domain és url-ek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`<EXTERNAL_DNS_NAME>.portal`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Az ArcGIS Enterprise Domain és url-ek egyszeri bejelentkezési információi](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az ArcGIS Enterprise Client támogatási csapatával](mailto:support@esri.com) az értékek lefelvételéhez. Az azonosító értékét az **Identitásszolgáltató beállítása szakaszból**kapja, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>Az ArcGIS Enterprise egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az ArcGIS Enterprise vállalati webhelyére.

2. Válassza **a Szervezeti >a Beállítások szerkesztése lehetőséget.**

    ![ArcGIS vállalati konfiguráció](./media/arcgisenterprise-tutorial/configure1.png)

3. Válassza a **Biztonság** fület.

    ![ArcGIS vállalati konfiguráció](./media/arcgisenterprise-tutorial/configure2.png)

4. Görgessen le a **Vállalati bejelentkezések saml-en keresztül** szakaszhoz, és válassza **az ENTERPRISE LOGIN beállítása**lehetőséget.

    ![ArcGIS vállalati konfiguráció](./media/arcgisenterprise-tutorial/configure3.png)

5. Az **Identitásszolgáltató beállítása** csoportban hajtsa végre az alábbi lépéseket:

    ![ArcGIS vállalati konfiguráció](./media/arcgisenterprise-tutorial/configure4.png)

    a. Adjon meg egy nevet, például **az Azure Active Directory-tesztet** a **Név** szövegmezőben.

    b. Az **URL-cím** beírt szövegdobozába illessze be az **Azure-rendszerből másolt Alkalmazásösszevonási metaadat-url-értéket.**

    c. Kattintson **a Speciális beállítások megjelenítése** elemre, és másolja az **entitásazonosító** értékét, és illessze be az Azure Portal **ArcGIS vállalati tartomány és URL-címek** **csoportazonosító** szövegmezőjébe.
    
    ![ArcGIS vállalati konfiguráció](./media/arcgisenterprise-tutorial/configure5.png)

    d. Kattintson **az IDENTITÁSSZOLGÁLTATÓ FRISSÍTÉSE gombra.**

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

Ebben a szakaszban engedélyezheti Britta Simon számára az Azure egyszeri bejelentkezést az ArcGIS Enterprise-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **ArcGIS Enterprise lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **ArcGIS Enterprise**lehetőséget.

    ![Az ArcGIS Enterprise hivatkozás a Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-arcgis-enterprise-test-user"></a>ArcGIS Vállalati tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az ArcGIS Enterprise-ban. Az ArcGIS Enterprise támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az ArcGIS Enterprise-ban, a hitelesítés után egy új jön létre.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, forduljon [az ArcGIS Enterprise támogatási csapatához.](mailto:support@esri.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az ArcGIS Enterprise csempére kattint, automatikusan be kell jelentkeznie az ArcGIS Enterprise-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

