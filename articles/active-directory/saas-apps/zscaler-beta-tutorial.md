---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Zscaler béta |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Zscaler béta között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07341c1ad30f1242bdff430826fdc82c45e09dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086074"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Oktatóanyag: Zscaler béta az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler béta integrálása az Azure Active Directory (Azure AD).
Zscaler béta integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Zscaler béta Azure AD-ben.
* Engedélyezze a felhasználóknak az automatikusan megtörténik a Zscaler bétaverzióra az Azure AD-fiókjukat. A hozzáférés-vezérlés az egyszeri bejelentkezés (SSO) nevezzük.
* A fiókok egyetlen központi helyen kezelheti az Azure portal használatával.

Az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja szoftverrel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler Beta, a következő elemek szükségesek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Zscaler béta-előfizetés, amely egyszeri bejelentkezést használ.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Zscaler béta SP által kezdeményezett egyszeri Bejelentkezést támogatja.
* Zscaler béta támogatja a felhasználókiépítés just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler béta hozzáadása az Azure Marketplace-ről

Zscaler béta integrálása az Azure AD beállítása, adja hozzá az Azure Marketplace-en Zscaler béta a felügyelt SaaS-alkalmazások listájában.

Az Azure Marketplace-ről Zscaler béta hozzáadásához kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory-gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza **új alkalmazás** a párbeszédpanel tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Zscaler béta**. Válassza ki **Zscaler béta** az eredmény panelen, és válassza ki a **Hozzáadás**.

     ![Az eredmények listájában Zscaler bétaverzió](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és tesztelés az Azure AD egyszeri bejelentkezés Zscaler béta a tesztfelhasználó számára Britta Simon alapján.
Az egyszeri bejelentkezés működjenek, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az Zscaler bétaverziós.

Az Azure AD egyszeri bejelentkezés a Zscaler béta tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

- [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
- [Zscaler béta egyszeri bejelentkezés konfigurálása](#configure-zscaler-beta-single-sign-on) az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazás oldalán.
- [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
- [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
- [Hozzon létre egy Zscaler béta tesztfelhasználót](#create-a-zscaler-beta-test-user) egy megfelelője a Britta Simon Zscaler bétaverzió, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
- [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zscaler bétaverzió, kövesse az alábbi lépéseket.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Zscaler béta** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza ki a **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon jelölje be **szerkesztése** megnyitásához a **alapszintű SAML-konfigurációja** párbeszédpanel bezárásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** területén kövesse az alábbi lépéseket:

    ![Zscaler béta tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-intiated.png)

    - Az a **bejelentkezési URL-cím** adja meg, jelentkezzen be a Zscaler béta alkalmazását a felhasználók által használt URL-CÍMÉT.

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-Címének értékét. Az érték beszerzéséhez forduljon a [Zscaler béta ügyfél-támogatási csapatának](https://www.zscaler.com/company/contact).

5. A Zscaler béta alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Egyéni attribútum-leképezéshez hozzá kell adnia a SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Válassza ki **szerkesztése** megnyitásához a **felhasználói attribútumok** párbeszédpanel bezárásához.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

6. A Zscaler béta alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. Az a **felhasználói jogcímek** című rész a **felhasználói attribútumok** párbeszédpanelen kövesse az alábbi lépéseket az SAML-jogkivonat attribútum hozzáadása az alábbi táblázatban látható módon.
    
    | Name (Név) | Adatforrás-attribútum | 
    | ---------------| --------------- |
    | Tagja(Pénzügy)  | user.assignedroles |

    a. Válassza ki **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel bezárásához.

    ![Felhasználói jogcímek párbeszédpanel](common/new-save-attribute.png)

    ![Felhasználói jogcímek párbeszédpanel kezelése](common/new-attribute-details.png)

    b. Az a **neve** mezőben megadhatja azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** mező üres.

    d. A **forrás**válassza **attribútum**.

    e. Az a **forrásattribútum** listában, adja meg az adott sorhoz feltüntetett attribútum értéke.

    f. Kattintson az **OK** gombra.

    g. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az Azure AD-szerepkörök konfigurálása, lásd: [a szerepkörjogcím konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be **letöltése** letöltéséhez a **tanúsítvány (Base64)** . Mentse a számítógépre.

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/certificatebase64.png)

8. Az a **Zscaler béta beállítása** területén másolja az URL-címeket, a követelmények kielégítése érdekében:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure AD-azonosító
    - Kijelentkezési URL

### <a name="configure-zscaler-beta-single-sign-on"></a>Zscaler béta egyszeri bejelentkezés konfigurálása

1. Automatizálhatja a Zscaler béta konfigurációra, telepítse a **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kiválasztásával **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. Kiválasztása után adja hozzá a bővítményt a böngészőben, **Zscaler béta beállítása** irányítja a Zscaler béta alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatok a Zscaler bétaverzióra történő bejelentkezés. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6. lépést.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Zscaler béta manuális beállítása, nyisson meg egy új böngészőablakban. Jelentkezzen be rendszergazdaként vállalati Zscaler béta webhelyét, és kövesse az alábbi lépéseket.

4. Lépjen a **felügyeleti** > **hitelesítési** > **hitelesítési beállítások**, és kövesse az alábbi lépéseket.
   
    ![Felügyeleti](./media/zscaler-beta-tutorial/ic800206.png "felügyelete")

    a. A **hitelesítési típus**válassza **SAML**.

    b. Válassza ki **SAML konfigurálása**.

5. Az a **szerkesztése SAML** ablakban hajtsa végre az alábbi lépéseket: 
            
    ![Felhasználók és hitelesítés kezeléséhez](./media/zscaler-beta-tutorial/ic800208.png "felhasználók és hitelesítés kezeléséhez")
    
    a. Az a **SAML portál URL-CÍMÉT** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt.

    b. Az a **bejelentkezési név attribútum** mezőbe írja be **NameID**.

    c. Az a **nyilvános SSL-tanúsítvány** jelölje ki **feltöltése** az Azure-portálról letöltött Azure SAML aláíró tanúsítvány feltöltéséhez.

    d. Váltógomb **SAML automatikus kiépítés engedélyezése**.

    e. Az a **felhasználói megjelenítési név attribútum** mezőbe írja be **displayName** Ha engedélyezi a SAML autoprovisioning displayName attribútum.

    f. Az a **csoport neve attribútum** mezőbe írja be **tagja(Pénzügy)** szeretné engedélyezni a SAML autoprovisioning tagja(Pénzügy) attribútumok esetén, ha.

    g. Az a **részleg neve attribútum** mezőbe írja be **részleg** Ha engedélyezi a SAML autoprovisioning részleg attribútumokat.

    h. Kattintson a **Mentés** gombra.

6. Az a **felhasználói hitelesítés konfigurálása** párbeszédpanel lapon, kövesse az alábbi lépéseket:

    ![Az aktiválás menüben, és az aktiválás gombra](./media/zscaler-beta-tutorial/ic800207.png)

    a. A kurzort a **aktiválási** menüjének bal alsó.

    b. Válassza ki **aktiválása**.

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
A Proxybeállítások konfigurálása az Internet Explorerben, kövesse az alábbi lépéseket.

1. Indítsa el **az Internet Explorer**.

2. Válassza ki **Internetbeállítások** származó a **eszközök** menü megnyitásához a **Internetbeállítások** párbeszédpanel bezárásához. 
    
     ![Internetbeállítások](./media/zscaler-beta-tutorial/ic769492.png "Internetbeállítások")

3. Válassza ki a **kapcsolatok** fülre. 
  
     ![Kapcsolatok lap](./media/zscaler-beta-tutorial/ic769493.png "kapcsolatok")

4. Válassza ki **LAN-beállítások** megnyitásához a **helyi hálózati (LAN) beállításai** párbeszédpanel bezárásához.

5. Az a **proxykiszolgáló** területén kövesse az alábbi lépéseket: 
   
    ![Proxy server szakasz](./media/zscaler-beta-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki a **proxykiszolgáló használata a helyi hálózaton** jelölőnégyzetet.

    b. Az a **cím** mezőbe írja be **átjáró. Zscaler Beta.net**.

    c. Az a **Port** mezőbe írja be **80-as**.

    d. Válassza ki a **proxykiszolgáló kihagyása helyi címek esetén** jelölőnégyzetet.

    e. Válassza ki **OK** gombra kattintva zárja be a **helyi hálózati (LAN) beállításai** párbeszédpanel bezárásához.

6. Válassza ki **OK** gombra kattintva zárja be a **Internetbeállítások** párbeszédpanel bezárásához.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![Felhasználók és a felhasználók minden kapcsolat](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Válassza ki a **Show jelszó** jelölőnégyzetet. Írja le az értéket, amely jeleníti meg a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Britta Simon által biztosított hozzáférés Zscaler béta Azure egyszeri bejelentkezés használata lehetővé.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás** > **Zscaler béta**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját adja meg, és válassza **Zscaler béta**.

    ![Az alkalmazások listáját a Zscaler béta-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![Felhasználók és csoportok hivatkozás](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![Felhasználó hozzáadása gomb](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki a felhasználó **Britta Simon** a listából. Válassza a **kiválasztása** a képernyő alján.

    ![Felhasználók és csoportok párbeszédpanelen](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Az a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő felhasználói szerepkörhöz a listában. Válassza a **kiválasztása** a képernyő alján.

    ![Válassza ki a szerepkör párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

    ![Hozzárendelés párbeszédpanel hozzáadása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler béta tesztfelhasználó létrehozása

Ebben a szakaszban a felhasználó Britta Simon Zscaler bétaverziós jön létre. Támogatja a Zscaler béta **just-in-time-felhasználók létrehozásának**, amely alapértelmezés szerint engedélyezve van. Nincs mit ebben a szakaszban elvégzéséhez. Ha a felhasználó még nem létezik a Zscaler béta, egy új jön létre a hitelesítés után.

>[!Note]
>Hozza létre manuálisan egy felhasználó, lépjen kapcsolatba a [Zscaler béta támogatási csapatának](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Az Azure AD egyszeri bejelentkezés beállításainak ellenőrzéséhez a hozzáférési panelen.

A Zscaler béta csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Zscaler Beta, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

