---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zscaler Beta programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler Beta között.
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
ms.openlocfilehash: 07b0f8112f724c857ffb46378f7aa7ef605b9bbb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68943287"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Oktatóanyag: Az Azure Active Directory integrációja a Zscaler Beta programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler Beta szolgáltatást az Azure Active Directoryval (Azure AD).
Ha integrálja a Zscaler Beta-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zscaler Beta.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkeznek a Zscaler Beta-ba az Azure AD-fiókjukkal. Ezt a hozzáférés-vezérlést egyszeri bejelentkezésnek (SSO) nevezzük.
* Egyetlen központi helyen kezelheti fiókjait az Azure Portal használatával.

A szoftverszolgáltatásként (SaaS) alkalmazásintegrációaz Azure AD-vel kapcsolatos további tudnivalókat a [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Zscaler Beta alkalmazással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* Zscaler Béta-előfizetés, amely egyszeri bejelentkezést használ.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Zscaler Beta támogatja az SP által kezdeményezett egyszeri vevőt.
* A Zscaler Beta támogatja a just-in-time felhasználói kiépítést.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler beta hozzáadása az Azure Piactérről

A Zscaler Beta azure AD-be való integrálásának konfigurálásához adja hozzá a Zscaler Beta-t az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

Zscaler Beta hozzáadása az Azure Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Beta**értéket. Válassza a **Zscaler Beta** elemet az eredménypanelen, majd kattintson a **Hozzáadás gombra.**

     ![Zscaler Béta az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést a Zscaler Beta-val a tesztfelhasználó Britta Simon alapján.
Egyszeri bejelentkezés a munka, hozzon létre egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó Zscaler Beta.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Zscaler Beta szolgáltatással hajtsa végre a következő építőelemeket:

- [Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on) hogy a felhasználók használhassák ezt a funkciót.
- [Konfigurálja a Zscaler Beta egyszeri bejelentkezést](#configure-zscaler-beta-single-sign-on) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
- [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
- [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
- [Hozzon létre egy Zscaler Béta teszt felhasználó,](#create-a-zscaler-beta-test-user) hogy egy megfelelője Britta Simon zscaler béta, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
- [Egyszeri bejelentkezés tesztelése annak](#test-single-sign-on) ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezéskonfigurálásához a Zscaler Beta alkalmazással hajtsa végre az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler bétaalkalmazás-integrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen jelölje ki az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza a **Szerkesztés** lehetőséget az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **egyszerű SAML-konfiguráció** szakaszban kövesse ezt a lépést:

    ![Zscaler Beta tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-intiated.png)

    - A **Bejelentkezési URL-cím** mezőbe írja be a felhasználók által a Zscaler Béta alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges Bejelentkezés az URL-értékkel. Az érték lefelvételéhez lépjen kapcsolatba a [Zscaler Beta ügyféltámogatási csapatával.](https://www.zscaler.com/company/contact)

5. A Zscaler Béta alkalmazás az SAML-állításokat egy adott formátumban várja. Egyéni attribútumleképezéseket kell hozzáadnia az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. A **Szerkesztés gombra** a **Felhasználói attribútumok** párbeszédpanel megnyitásához kattintson a Szerkesztés gombra.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

6. A Zscaler Béta alkalmazás elvárja, hogy még néhány attribútumot vissza kell adni az SAML válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában kövesse az alábbi lépéseket az SAML token attribútum hozzáadásához, ahogy az az alábbi táblázatban látható.
    
    | Név | Forrás attribútum | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. A **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához válassza az Új **jogcím hozzáadása** lehetőséget.

    ![Felhasználói jogcímek párbeszédpanel](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése párbeszédpanel](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sorattribútum nevét.

    c. Hagyja üresen a **Névtér** mezőt.

    d. A **Forrás csoportban**válassza **az Attribútum**lehetőséget.

    e. A **Forrás attribútumlistában** adja meg a sorhoz megjelenített attribútumértéket.

    f. Válassza **az OK gombot.**

    g. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A szerepkörök Azure AD-ben való konfigurálásáról [a Szerepkörjogcím konfigurálása című](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)témakörben olvashat.

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés** lehetőséget a **tanúsítvány letöltéséhez (Base64)** elemre. Mentse a számítógépre.

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler Beta beállítása szakaszban** másolja a követelményekhez szükséges URL-címeket:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure Hirdetés-azonosító
    - Kijelentkezés URL-címe

### <a name="configure-zscaler-beta-single-sign-on"></a>A Zscaler Beta egyszeri bejelentkezéskonfigurálása

1. A Zscaler Beta konfigurációjának automatizálásához telepítse a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadja a bővítményt a böngészőhöz, a **Zscaler Beta beállítása** lehetőséget választva a Zscaler Beta alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat a Zscaler Beta-ba való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. A Zscaler Beta manuális beállításához nyisson meg egy új böngészőablakot. Jelentkezzen be a Zscaler Beta vállalati webhelyére rendszergazdaként, és kövesse az alábbi lépéseket.

4. Nyissa meg a **Felügyeleti** > **hitelesítés hitelesítés** > **beállításai lehetőséget,** és kövesse az alábbi lépéseket.
   
    ![Felügyelet](./media/zscaler-beta-tutorial/ic800206.png "Adminisztráció")

    a. A **Hitelesítés típusa csoportban**válassza az **SAML**lehetőséget.

    b. Válassza **az SAML konfigurálása**lehetőséget.

5. Az **SAML szerkesztése** ablakban hajtsa végre az alábbi lépéseket: 
            
    ![A felhasználók & a hitelesítés kezelése](./media/zscaler-beta-tutorial/ic800208.png "A felhasználók & a hitelesítés kezelése")
    
    a. Az **SAML Portal URL-címe** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    b. A **Bejelentkezési név attribútum** mezőbe írja be a **NameID azonosítót.**

    c. A **Nyilvános SSL-tanúsítvány** mezőben válassza **a Feltöltés lehetőséget** az Azure SAML-aláírási tanúsítvány feltöltéséhez, amelyet az Azure Portalról töltött le.

    d. Váltás az **SAML automatikus kiépítés engedélyezése között.**

    e. A **Felhasználó megjelenítendő neve attribútum** mezőbe írja be a **displayName értéket,** ha engedélyezni szeretné az SAML automatikus kiépítését a displayName attribútumokhoz.

    f. A **Group Name Attribute (Csoportnév attribútum)** mezőbe írja be a **memberOf** értéket, ha engedélyezni szeretné az SAML automatikus kiépítését a memberOf attribútumokhoz.

    g. A **Részlegnév attribútum** mezőbe írja be a **részleget,** ha engedélyezni szeretné az SAML automatikus kiépítést a részlegattribútumokhoz.

    h. Kattintson a **Mentés** gombra.

6. A **Felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Aktiválás menü és Aktiválás gomb](./media/zscaler-beta-tutorial/ic800207.png)

    a. Mutasson az **Aktiválás** menüre a bal alsó sarokban.

    b. Válassza **az Aktiválás**lehetőséget.

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
Az Internet Explorer proxybeállításainak konfigurálásához hajtsa végre az alábbi lépéseket.

1. Indítsa el **az Internet Explorer**t .

2. Az **Internetbeállítások** párbeszédpanel megnyitásához válassza az **Eszközök** menü **Internetbeállítások** parancsát. 
    
     ![Az Internetbeállítások párbeszédpanel](./media/zscaler-beta-tutorial/ic769492.png "Internetbeállítások")

3. Válassza a **Kapcsolatok** lapot. 
  
     ![Connections (Kapcsolatok) lap](./media/zscaler-beta-tutorial/ic769493.png "Kapcsolatok")

4. A HELYI HÁLÓZAT **(LAN) beállításai** párbeszédpanel megnyitásához válassza a **LAN-beállítások lehetőséget.**

5. A **Proxykiszolgáló** szakaszban hajtsa végre az alábbi lépéseket: 
   
    ![Proxykiszolgáló szakasz](./media/zscaler-beta-tutorial/ic769494.png "Proxykiszolgáló")

    a. Jelölje be a **Proxykiszolgáló használata a helyi hálózathoz** jelölőnégyzetet.

    b. A **Cím** mezőbe írja be az **átjárót. Zscaler Beta.net**.

    c. A **Port** mezőbe írja be a **80 értéket.**

    d. Jelölje be a **Proxykiszolgáló mellőzése helyi címekhez** jelölőnégyzetet.

    e. A **Helyi hálózat (LAN) beállításai** párbeszédpanel bezárásához válassza az **OK** gombot.

6. Az **Internetbeállítások** párbeszédpanel bezárásához **kattintson** az OK gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon néven.

1. Az Azure Portalbal a bal oldali ablaktáblában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![Felhasználók és minden felhasználó hivatkozásai](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőben adja meg a következőt: `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Írja le a **Jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Engedélyezze Britta Simon számára az Azure egyszeri bejelentkezést a Zscaler Beta-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások** > **Minden alkalmazás** > **Zscaler Beta**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában adja meg és válassza **a Zscaler Beta**lehetőséget.

    ![Zscaler Béta hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![Felhasználók és csoportok hivatkozása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Felhasználó hozzáadása gomb](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználót, például **Britta Simont** a listából. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.

    ![Felhasználók és csoportok párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. A **Szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.

    ![Szerepkör kiválasztása párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

    ![Hozzárendelés hozzáadása párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler Béta tesztfelhasználó létrehozása

Ebben a szakaszban a felhasználó Britta Simon jön létre zscaler béta. A Zscaler Beta támogatja **a just-in-time felhasználói kiépítést,** amely alapértelmezés szerint engedélyezve van. Nincs mit tenned ebben a részben. Ha a felhasználó már nem létezik a Zscaler Beta, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan szeretne felhasználót létrehozni, lépjen kapcsolatba a [Zscaler Béta támogatási csapatával.](https://www.zscaler.com/company/contact)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Tesztelje az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával.

Ha a Hozzáférési panelen kiválasztja a B-skálás bétaverzió csempét, automatikusan be kell jelentkeznie a Zscaler Béta verzióba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

