---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Zscaler között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: dee44b10040fa1f94825ac51825f32cc39cdc788
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825202"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler a Azure Active Directory (Azure AD) szolgáltatással.
A Zscaler és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Zscaler.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Zscaler (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Zscaler egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A Zscaler **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a gyűjteményből

A Zscaler Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Zscaler szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler**kifejezést, válassza a **Zscaler** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zscaler az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Zscaler-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Zscaler kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Zscaler való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zscaler egyszeri bejelentkezés konfigurálása](#configure-zscaler-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Zscaler-teszt felhasználót](#create-zscaler-test-user)** – hogy a Zscaler Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Zscaler való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Zscaler** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Zscaler tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-signonurl.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.zscaler.net`

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [Zscaler](https://www.zscaler.com/company/contact) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A Zscaler-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Zscaler alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:
    
    | Name (Név) | Forrás attribútum |
    | ---------| ------------ |
    | memberOf     | User. assignedroles |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.
    
    f. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Ide kattintva [](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. A **Zscaler beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-zscaler-single-sign-on"></a>Zscaler egyszeri bejelentkezés konfigurálása

1. A Zscaler belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés Zscaler** gombra a Zscaler alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Egyszeri bejelentkezés beállítása](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler, nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. Lépjen az **adminisztráció > hitelesítés > hitelesítési beállítások** lapra, és hajtsa végre a következő lépéseket:
   
    ![Felügyelet](./media/zscaler-tutorial/ic800206.png "Felügyelet")

    a. A hitelesítés típusa területen válassza az **SAML**elemet.

    b. Kattintson az **SAML konfigurálása**elemre.

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket:, majd kattintson a Mentés gombra.  
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** szövegmezőbe írja be a **NameID**nevet.

    c. Kattintson a **feltöltés**gombra, és töltse fel a **nyilvános SSL**-tanúsítványban Azure Portal letöltött Azure SAML-aláíró tanúsítványt.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenített név attribútuma** szövegmezőbe írja be a **DisplayName** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a DisplayName attribútumokhoz.

    f. A **Csoportnév-attribútum** szövegmezőbe írja be a **memberof** értéket, ha engedélyezni szeretné az SAML automatikus kiépítési lehetőséget a memberOf attribútumaihoz.

    g. A **részleg neve attribútumban** adja meg a **részleget** , ha engedélyezni szeretné az SAML automatikus kiépítés részleg attribútumait.

    h. Kattintson a **Save** (Mentés) gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Felügyelet](./media/zscaler-tutorial/ic800207.png)

    a. Vigye az egérmutatót a bal alsó sarokban található **aktiválási** menü fölé.

    b. Kattintson az **aktiválás**gombra.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el az **Internet Explorert**.

2. Az Internetbeállítások párbeszédpanel megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét.   
    
     ![Internetbeállítások](./media/zscaler-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok] száma (./media/zscaler-tutorial/ic769493.png "Kapcsolatok") száma

4. A LAN- **Beállítások** párbeszédpanel megnyitásához kattintson a **LAN-beállítások** elemre.

5. A proxykiszolgáló szakaszban hajtsa végre a következő lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a proxykiszolgáló használata a LAN**-hoz lehetőséget.

    b. A címek szövegmezőbe írja be a következőt: **Gateway.zscaler.net**.

    c. A port szövegmezőbe írja be a következőt: **80**.

    d. Válassza **a proxykiszolgáló kihagyása helyi címeknél**lehetőséget.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`a nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Zscaler hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Zscaler**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler**lehetőséget.

    ![Az Zscaler hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza ki a **Britta Simon** elemet a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. A **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a képernyő alján található **kiválasztás** gombra.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

### <a name="create-zscaler-test-user"></a>Zscaler-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscaler-ben. A Zscaler támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Zscaler-ben, a rendszer egy újat hoz létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Zscaler támogatási csapatával](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler csempére kattint, automatikusan be kell jelentkeznie arra a Zscaler, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

