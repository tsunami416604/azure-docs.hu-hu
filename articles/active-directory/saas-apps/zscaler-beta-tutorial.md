---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler Beta szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler Beta között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: f342371ec065c4fb60c492c4354c6f8c717c9bb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546009"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Oktatóanyag: Azure Active Directory integráció a Zscaler Betatal

Ez az oktatóanyag azt ismerteti, hogyan integrálható a Zscaler Beta Azure Active Directory (Azure AD) szolgáltatással.
A Zscaler Beta és az Azure AD integrálásával a következőket teheti:

* A Zscaler Beta elérését biztosító Azure AD-beli vezérlés.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezzenek a Zscaler Beta szolgáltatásba az Azure AD-fiókjával. A hozzáférés-vezérlés neve egyszeri bejelentkezés (SSO).
* A fiókokat egy központi helyen kezelheti a Azure Portal használatával.

További információ az Azure AD-vel való szolgáltatott szoftver-(SaaS-) alkalmazások integrálásáról: [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Zscaler Beta-nal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést használó Zscaler Beta-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Zscaler Beta támogatja az SP által kezdeményezett egyszeri bejelentkezést.
* A Zscaler Beta az igény szerinti felhasználói üzembe helyezést is támogatja.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Zscaler Beta hozzáadása az Azure Marketplace-ről

A Zscaler Beta Azure AD-be való integrálásának konfigurálásához adja hozzá a Zscaler Beta-t az Azure piactéren a felügyelt SaaS-alkalmazások listájához.

Az Zscaler Beta Azure piactéren való hozzáadásához kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)bal oldali navigációs paneljén válassza a **Azure Active Directory**lehetőséget.

    ![Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler Beta**kifejezést. Válassza a **Zscaler Beta** elemet az eredmények panelen, majd válassza a **Hozzáadás**lehetőséget.

     ![Zscaler Beta az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a Zscaler Beta szolgáltatással konfigurálja és teszteli a Simon Britta teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez hozzon létre egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Zscaler Beta-ban.

Az Azure AD egyszeri bejelentkezés Zscaler Beta használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

- Az [Azure ad egyszeri bejelentkezés konfigurálásával](#configure-azure-ad-single-sign-on) engedélyezheti a felhasználók számára a funkció használatát.
- [Konfigurálja a Zscaler Beta egyszeri bejelentkezést](#configure-zscaler-beta-single-sign-on) az alkalmazás oldalának egyszeri bejelentkezési beállításainak konfigurálásához.
- [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
- [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
- [Hozzon létre egy Zscaler béta-tesztelési felhasználót](#create-a-zscaler-beta-test-user) , hogy a felhasználó Azure ad-képviseletéhez kapcsolódó, a Zscaler Beta-ban található Britta Simon partnere legyen.
- [Tesztelje az egyszeri bejelentkezést](#test-single-sign-on) annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a Zscaler Beta használatával történő konfigurálásához kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com/)a **Zscaler Beta** Application Integration lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri **bejelentkezési módszer kiválasztása** párbeszédpanelen válassza ki az **SAML/ws-fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeres Sign-On beállítása az SAML-vel** lapon válassza a **Szerkesztés** lehetőséget az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban kövesse ezt a lépést:

    ![Zscaler Beta-tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-intiated.png)

    - A **bejelentkezési URL-cím** mezőbe írja be a felhasználók által a Zscaler Beta alkalmazásba való bejelentkezéshez használt URL-címet.

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-értékkel. Az érték beszerzéséhez forduljon a [Zscaler Beta ügyfél-támogatási csapatához](https://www.zscaler.com/company/contact).

5. A Zscaler Beta alkalmazás megadott formátumban várja az SAML-kijelentéseket. Egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Válassza a **Szerkesztés** lehetőséget a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![Felhasználói attribútumok párbeszédpanel](common/edit-attribute.png)

6. A Zscaler Beta alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. Az alábbi táblázatban látható módon adja hozzá az SAML-jogkivonat attribútumot a felhasználói **jogcímek** szakaszban, a **felhasználói attribútumok** párbeszédpanelen.
    
    | Név | Forrás attribútum | 
    | ---------------| --------------- |
    | memberOf  | User. assignedroles |

    a. Válassza az **új jogcím hozzáadása** elemet a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![Felhasználói jogcímek párbeszédpanel](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése párbeszédpanel](common/new-attribute-details.png)

    b. A **név** mezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névtér** mezőt.

    d. A **forrás**mezőben válassza az **attribútum**lehetőséget.

    e. A **forrás attribútum** listáról adja meg az adott sorhoz megjelenő attribútum értékét.

    f. Válassza az **OK** lehetőséget.

    : Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A szerepkörök az Azure AD-ben való konfigurálásával kapcsolatos további információkért lásd: [a szerepkör-jogcím konfigurálása](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** elemet a tanúsítvány letöltéséhez **(Base64)**. Mentse a számítógépére.

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

8. A **Zscaler Beta beállítása** szakaszban másolja a követelményekhez szükséges URL-címeket:

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    - Bejelentkezési URL
    - Azure AD-azonosító
    - Kijelentkezési URL-cím

### <a name="configure-zscaler-beta-single-sign-on"></a>Zscaler Beta egyszeri bejelentkezés konfigurálása

1. A Zscaler Beta-on belüli konfiguráció automatizálásához telepítse a **saját alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, a **Zscaler Beta beállítása** lehetőség választásával a Zscaler Beta alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat a Zscaler Beta rendszerbe való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3 – 6. lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. A Zscaler Beta manuális beállításához nyisson meg egy új böngészőablakot. Jelentkezzen be rendszergazdaként a Zscaler Beta vállalati webhelyre, és kövesse az alábbi lépéseket.

4. Nyissa meg a **felügyeleti**  >  **hitelesítési**  >  **hitelesítési beállításokat**, és kövesse az alábbi lépéseket.
   
    ![Felügyelet](./media/zscaler-beta-tutorial/ic800206.png "Felügyelet")

    a. A **Hitelesítés típusa**területen válassza az **SAML**lehetőséget.

    b. Válassza az **SAML konfigurálása**lehetőséget.

5. Az **SAML szerkesztése** ablakban kövesse az alábbi lépéseket: 
            
    ![Felhasználók kezelése & hitelesítéssel](./media/zscaler-beta-tutorial/ic800208.png "Felhasználók kezelése & hitelesítéssel")
    
    a. Az **SAML-portál URL-címe** mezőbe illessze be a Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A **bejelentkezési név attribútum** mezőbe írja be a **NameID**nevet.

    c. A **nyilvános SSL-tanúsítvány** mezőben válassza a **feltöltés** lehetőséget a Azure Portal letöltött Azure SAML-aláíró tanúsítvány feltöltéséhez.

    d. Az **SAML automatikus kiépítés engedélyezése**.

    e. A **felhasználó megjelenítendő neve attribútum** mezőjébe írja be a **DisplayName** értéket, ha engedélyezni szeretné a DISPLAYNAME attribútumokhoz tartozó SAML-attribútumok kiépítését.

    f. A **Csoportnév-attribútum** mezőben adja meg a **memberof** értéket, ha engedélyezni szeretné az SAML-kiépítést a memberOf attribútumaihoz.

    : A részleg **neve attribútum** mezőben adja meg a **részleg** értéket, ha engedélyezni szeretné a részleg attribútumainak SAML-kiépítését.

    h. Kattintson a **Mentés** gombra.

6. A **felhasználói hitelesítés konfigurálása** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Aktiválás menü és aktiválás gomb](./media/zscaler-beta-tutorial/ic800207.png)

    a. Vigye a kurzort a bal alsó sarokban lévő **aktiválási** menüre.

    b. Válassza az **aktiválás**lehetőséget.

## <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása
A proxybeállítások az Internet Explorerben való konfigurálásához kövesse az alábbi lépéseket.

1. Indítsa el az **Internet Explorert**.

2. **Az Internetbeállítások párbeszédpanel** megnyitásához válassza az **eszközök** menü **Internetbeállítások** elemét. 
    
     ![Internetbeállítások párbeszédpanel](./media/zscaler-beta-tutorial/ic769492.png "Internetbeállítások")

3. Válassza a **kapcsolatok** fület. 
  
     ![Connections (Kapcsolatok) lap](./media/zscaler-beta-tutorial/ic769493.png "Kapcsolatok")

4. A **helyi hálózati (LAN) beállítások** párbeszédpanel megnyitásához válassza a **LAN-beállítások** lehetőséget.

5. A **proxykiszolgáló** szakaszban hajtsa végre az alábbi lépéseket: 
   
    ![Proxykiszolgáló szakasz](./media/zscaler-beta-tutorial/ic769494.png "Proxykiszolgáló")

    a. Jelölje be a **proxykiszolgáló használata a helyi hálózat számára** jelölőnégyzetet.

    b. A **címterület** mezőbe írja be az **átjáró értéket. Zscaler Beta.net**.

    c. A **port** mezőbe írja be a **80**értéket.

    d. Jelölje be a **proxykiszolgáló kihagyása helyi címeknél** jelölőnégyzetet.

    e. A **helyi hálózati (LAN) beállítások** párbeszédpanel bezárásához kattintson **az OK gombra** .

6. Az **Internetbeállítások** párbeszédpanel bezárásához kattintson **az OK gombra** .

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Hozzon létre egy teszt felhasználót a Britta Simon nevű Azure Portalban.

1. A Azure Portal a bal oldali ablaktáblán válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.

    ![Felhasználók és minden felhasználó hivatkozása](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **felhasználó** párbeszédpanelen kövesse az alábbi lépéseket:

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőben adja meg a következőt: `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com.

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a **jelszó** mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Engedélyezze a Britta Simon számára az Azure egyszeri bejelentkezés használatát a Zscaler Beta elérésének biztosításával.

1. A Azure Portal válassza a **Nagyvállalati alkalmazások**  >  **minden alkalmazás**  >  **Zscaler Beta**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában adja meg és válassza a **Zscaler Beta**elemet.

    ![Zscaler Beta-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![Felhasználók és csoportok hivatkozása](common/users-groups-blade.png)

4. Válassza a **Felhasználó hozzáadása** elemet. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok**lehetőséget.

    ![Felhasználó hozzáadása gomb](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza ki a listáról azt a felhasználót, mint a **Britta Simon** . Ezután válassza a **kijelölés** lehetőséget a képernyő alján.

    ![Felhasználók és csoportok párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. A **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.

    ![Szerepkör kiválasztása párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

    ![Hozzárendelés hozzáadása párbeszédpanel](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Zscaler Beta tesztelési felhasználó létrehozása

Ebben a szakaszban a Simon Britta felhasználó a Zscaler Beta-ban jön létre. A Zscaler Beta támogatja az **igény szerinti felhasználói üzembe**helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban semmi teendője van. Ha egy felhasználó még nem létezik a Zscaler Beta-ban, a hitelesítés után létrejön egy újat.

>[!Note]
>Ha manuálisan szeretné létrehozni a felhasználót, forduljon a [Zscaler Beta támogatási csapatához](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Tesztelje az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panel használatával.

Amikor kiválasztja a Zscaler Beta csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Zscaler Beta-ra, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

