---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Meta Networks Connector programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Meta Networks Connector között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a09eda25e8c7cc087770210cdfbe7e2bc9832acf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160637"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Oktatóanyag: Az Azure Active Directory integrációja a Meta Networks Connector-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Meta Networks Connector t az Azure Active Directory (Azure AD) használatával.
A Meta Networks Connector integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Meta Networks Connector-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve meta networks connector (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció beállítása a Meta Networks Connector, szüksége van a következő elemeket:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Meta Networks Connector egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Meta Networks Connector támogatja az **SP** és **az IDP** által kezdeményezett sso-t
 
* A Meta Networks Connector támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta networksconnector hozzáadása a galériából

A Meta Networks Connector azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Meta Networks Connector-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Meta Networks Connectort a gyűjteményből szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Meta Networks Connector**( Meta Networks Connector ) kifejezést, válassza a **Meta Networks Connector** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Meta Networks Connector az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Meta Networks Connector szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó meta networks összekötő létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Meta Networks Connector segítségével a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Meta Networks összekötő egyszeri bejelentkezést](#configure-meta-networks-connector-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Meta Networks Connector teszt felhasználó](#create-meta-networks-connector-test-user)** – egy megfelelője Britta Simon a Meta Networks Connector, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Meta Networks Connector használatával hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Meta Networks Connector** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![MetaNetworks összekötő tartomány és URL-címek egyszeri bejelentkezési információi](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![MetaNetworks összekötő tartomány és URL-címek egyszeri bejelentkezési információi](common/both-advanced-urls.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. A **Továbbítási állapot** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel az oktatóanyag későbbi részében.

6. A Meta Networks Connector alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)
    
7. A fentieken kívül a Meta Networks Connector alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:
    
    | Név | Forrás attribútum | Névtér|
    | ---------------| --------------- | -------- |
    | Utónév | user.givenname | |
    | Vezetéknév | user.vezetéknév | |
    | e-mail cím| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | név | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | user.phonenumber (telefonszám) | |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. A **Meta Networks összekötő beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-meta-networks-connector-single-sign-on"></a>Meta Networks összekötő egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Meta Networks Connector rendszergazdai fiókjába.
    
    > [!NOTE]
    > Meta Networks Connector egy biztonságos rendszer. Tehát elérése előtt a portálon meg kell, hogy a nyilvános IP-címet hozzá egy engedélyezési lista az ő oldalukon. Ahhoz, hogy a nyilvános IP-cím,kövesse az alábbi linkre [itt](https://whatismyipaddress.com/)megadott . Küldje el IP-címét a [Meta Networks Connector ügyfél támogatási csapatának,](mailto:support@metanetworks.com) hogy az IP-címét hozzáadhassa egy engedélyezési listához.
    
2. Nyissa meg a **Rendszergazda lapot,** és válassza a **Beállítások lehetőséget.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Győződjön meg **arról, hogy a Log Internet Traffic** és a Force **VPN MFA** ki van kapcsolva.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Nyissa meg a **Rendszergazdát,** és válassza az **SAML lehetőséget.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Hajtsa végre a következő lépéseket a **RÉSZLETEK** lapon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Másolja az **Egyszeri bejelentkezés URL-értékét,** és illessze be a **Meta Networks összekötő tartomány és URL-címek** csoport **Bejelentkezési URL-címmezőjébe.**
    
    b. Másolja **a címzett URL-értékét,** és illessze be a **Válasz URL-szövegmezőjébe** a **Meta Networks összekötő tartomány és URL-címek** szakaszban.
    
    c. A **Közönség URI-értékének másolása és** beillesztése a **MetaNetworks összekötő tartomány és URL-címek** csoport azonosító **(entitásazonosítója)** szövegmezőjébe.
    
    d. Az SAML engedélyezése
    
6. Az **ÁLTALÁNOS** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure5.png)

    a. Az **identitásszolgáltató egyszeri bejelentkezési URL-címében**illessze be a **bejelentkezési URL-cím,** amely et az Azure Portalról másolt.

    b. Az **identitásszolgáltató kiállítója**illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítványt az Azure Portalról a jegyzettömbben, és illessze be az **X.509 tanúsítvány** szövegdobozába.

    d. Engedélyezze a **just-in-time kiépítést.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t a Meta Networks Connector hozzáférést biztosít.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Meta Networks Connector**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Meta Networks Connector (Meta Networks Connector) lehetőséget.**

    ![A Meta Networks összekötő hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks összekötő tesztfelhasználójának létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Meta Networks Connector. A Meta Networks Connector támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Meta Networks Connector alkalmazásban, egy új jön létre, amikor megpróbál hozzáférni a Meta Networks Connector-hoz.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon a [Meta Networks Connector ügyféltámogatási csapatához.](mailto:support@metanetworks.com)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Meta Networks connector csempére kattint, automatikusan be kell jelentkeznie a Meta Networks connector-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

