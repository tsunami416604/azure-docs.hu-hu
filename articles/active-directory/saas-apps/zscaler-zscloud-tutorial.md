---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Zscaler ZSClouddal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Zscaler ZSCloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 43d7e58f0c267afe8a22c217d9800abb041df8cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68723062"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Oktatóanyag: Az Azure Active Directory integrációja a Zscaler ZSClouddal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Zscaler ZSCloudot az Azure Active Directoryval (Azure AD).
A Zscaler ZSCloud és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Zscaler ZSCloudhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Zscaler ZSCloudba (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Zscaler ZSClouddal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) kaphat
* Zscaler ZSCloud egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Zscaler ZSCloud támogatja az **SP** által kezdeményezett SSO-t

* A Zscaler ZSCloud támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud hozzáadása a galériából

A Zscaler ZSCloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Zscaler ZSCloud-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni a Zscaler ZSCloud-ot a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler ZSCloud**kifejezést , válassza a **Zscaler ZSCloud** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Zscaler ZSCloud az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Zscaler ZSClouddal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, egy Azure AD-felhasználó és a kapcsolódó felhasználó a Zscaler ZSCloud létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Zscaler ZSCloud dal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Zscaler ZSCloud Single Sign-On szolgáltatást](#configure-zscaler-zscloud-single-sign-on)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Zscaler ZSCloud teszt felhasználó](#create-zscaler-zscloud-test-user)** - egy megfelelője Britta Simon zscaler ZSCloud, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Zscaler ZSClouddal hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Zscaler ZSCloud** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Zscaler ZSCloud Domain és URL-ek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írja be a felhasználók által a ZScaler ZSCloud alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az értéket a tényleges bejelentkezési URL-címmel kell frissítenie. Lépjen kapcsolatba [a Zscaler ZSCloud ügyféltámogatási csapatával](https://help.zscaler.com/) az érték értéséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. A Zscaler ZSCloud alkalmazás elvárja az SAML-állításokat egy adott formátumban, amely megköveteli, hogy egyéni attribútumleképezések hozzáadása az SAML token attribútumok konfigurációját. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentiekmellett a Zscaler ZSCloud alkalmazás azt várja, hogy néhány további attribútumot visszakell adni az SAML válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:
    
    | Név | Forrás attribútuma |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.
    
    f. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Kérjük, kattintson [ide,](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) hogy megtudja, hogyan konfigurálhatja a szerepkört az Azure AD-ben

7. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler ZSCloud beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Zscaler ZSCloud egyszeri bejelentkezéskonfigurálása

1. A Zscaler ZSCloud konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadt a böngészőhöz, kattintson a **Setup Zscaler ZSCloud-ra,** amely a Zscaler ZSCloud alkalmazáshoz irányítja Önt. Innen adja meg a rendszergazdai hitelesítő adatokat a Zscaler ZSCloudba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási rendszer](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler ZSCloud-ot, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Zscaler ZSCloud vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Nyissa meg **a Felügyeleti > hitelesítés i > hitelesítési beállításokat,** és hajtsa végre az alábbi lépéseket:
   
    ![Felügyelet](./media/zscaler-zscloud-tutorial/ic800206.png "Adminisztráció")

    a. A Hitelesítés típusa csoportban válassza az **SAML lehetőséget.**

    b. Kattintson **az SAML konfigurálása gombra.**

5. Az **SAML szerkesztése** ablakban hajtsa végre a következő lépéseket: és kattintson a Mentés gombra.  
            
    ![A felhasználók & a hitelesítés kezelése](./media/zscaler-zscloud-tutorial/ic800208.png "A felhasználók & a hitelesítés kezelése")
    
    a. Az **SAML Portal URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-címet.**

    b. A **Bejelentkezési név attribútum** mezőbe írja be a **NameID értéket.**

    c. Kattintson **a Feltöltés**gombra az Azure SAML-aláíró tanúsítvány feltöltéséhez, amelyet az Azure Portalról töltött le a **nyilvános SSL-tanúsítványban.**

    d. Az **SAML automatikus kiépítés engedélyezése**között.

    e. A **Felhasználó megjelenítendő neve attribútum** szövegmezőbe írja be a **displayName értéket,** ha engedélyezni szeretné az SAML automatikus kiépítését a displayName attribútumokhoz.

    f. A **Group Name Attribute** textbox, írja be **memberOf** ha engedélyezni szeretné SAML automatikus kiépítése a memberOf attribútumok.

    g. A **Részlegnév attribútum** enter **részlegében,** ha engedélyezni szeretné az SAML automatikus kiépítést a részlegattribútumokhoz.

    h. Kattintson a **Mentés** gombra.

6. A **Felhasználói hitelesítés konfigurálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Adminisztráció](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Mutasson az **Aktiválás** menüre a bal alsó sarokban.

    b. Kattintson **az Aktiválás gombra.**

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A proxybeállítások konfigurálása az Internet Explorer programban

1. Indítsa el **az Internet Explorer**t .

2. Válassza az **Internetbeállítások** párbeszédpanel megnyitásához válassza az **Eszközök** menü **Internetbeállítások** parancsát.   
    
     ![Internetbeállítások](./media/zscaler-zscloud-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **Kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-zscloud-tutorial/ic769493.png "Kapcsolatok")

4. A **LAN-beállítások gombra** kattintva nyissa meg a **LAN-beállítások párbeszédpanelt.**

5. A Proxykiszolgáló szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-zscloud-tutorial/ic769494.png "Proxykiszolgáló")

    a. Válassza **a Proxykiszolgáló használata a helyi hálózathoz**lehetőséget.

    b. A Cím mezőbe írja be az **átjárót. Zscaler ZSCloud.net**.

    c. A Port szövegmezőbe írja be a **80-at.**

    d. Válassza **a Proxykiszolgáló mellőzése lehetőséget a helyi címekhez.**

    e. A **Helyi hálózat (LAN) beállításai** párbeszédpanel bezárásához kattintson az **OK** gombra.

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson az **OK** gombra.

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a Zscaler ZSCloud-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Zscaler ZSCloud**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Zscaler ZSCloud**lehetőséget.

    ![A Zscaler ZSCloud hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználót, például **Britta Simont** a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. A **Szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson **a** kijelölés gombra a képernyő alján.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés** gombot.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Az alapértelmezett hozzáférési szerepkör nem támogatott, mivel ez megszakítja a kiépítést, így az alapértelmezett szerepkör nem választható ki a felhasználó hozzárendelése közben.

### <a name="create-zscaler-zscloud-test-user"></a>Zscaler ZSCloud tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Zscaler ZSCloud-ban. Zscaler ZSCloud támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Zscaler ZSCloud, egy új jön létre a hitelesítés után.

>[!Note]
>Ha manuálisan kell létrehoznia egy felhasználót, forduljon [a Zscaler ZSCloud támogatási csapatához.](https://help.zscaler.com/)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Zscaler ZSCloud csempére kattint, automatikusan be kell jelentkeznie a Zscaler ZSCloudba, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

