---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ADP |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ADP között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2c3318d5c8a2cf25db129cac8587bb424707ddf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456057"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ADP

Ebben az oktatóanyagban elsajátíthatja, hogyan ADP integrálása az Azure Active Directory (Azure AD).
ADP integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá ADP Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve ADP (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

ADP az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* ADP egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a ADP **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-adp-from-the-gallery"></a>ADP hozzáadása a katalógusból

Az Azure AD integrálása a ADP konfigurálásához hozzá kell ADP a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ADP hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ADP**válassza **ADP** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában ADP](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az ADP nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó ADP hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az ADP tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[ADP egyszeri bejelentkezés konfigurálása](#configure-adp-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre ADP tesztfelhasználót](#create-adp-test-user)**  – egy megfelelője a Britta Simon ADP, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ADP, hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon az a **ADP** alkalmazás integrációs oldalán kattintson a **tulajdonságai lap** , és hajtsa végre az alábbi lépéseket: 

    ![Egyszeri bejelentkezési tulajdonságainál](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Állítsa be a **engedélyezett a felhasználók bejelentkezhetnek** érték mező **Igen**.

    b. Másolás a **felhasználói hozzáférési URL-címe** , és illessze be kell **konfigurálása bejelentkezési URL-cím szakasz**, amely kifejtett az oktatóanyag későbbi részében.

    c. Állítsa be a **felhasználó-hozzárendelés szükséges** érték mező **Igen**.

    d. Állítsa be a **a felhasználók számára látható** érték mező **nem**.

2. Az a [az Azure portal](https://portal.azure.com/), az a **ADP** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![ADP tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-identifier.png)

    Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe:  `https://fed.adp.com`

6. ADP alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel. A jogcím neve mindig lesz **"PersonImmutableID"** és az érték, amely azt mutatja, hogy való leképezéséhez **employeeid**. 

    A felhasználóleképezést Azure AD-ből ADP történik meg a **employeeid** , de ez leképezhet az alkalmazás-beállításai alapján egy másik értéket. Ezért kérjük, munkahelyi az [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) először a felhasználó a megfelelő azonosító használja, és képezze le az értéket a **"PersonImmutableID"** jogcím.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Name (Név) | Adatforrás-attribútum | 
    | ---------------| --------------- |
    | PersonImmutableID  | User.EmployeeID |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > Az SAML-előfeltétel konfigurálása előtt kell kapcsolódni a [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) és az egyedi felhasználói azonosító attribútum értékét a bérlő számára. Szüksége lesz erre az értékre az alkalmazás egyéni jogcím konfigurálása. 

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>ADP egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **ADP** oldalán, fel kell töltenie a letöltött **metaadatainak XML** a a [ADP webhely](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ez a folyamat néhány napig is eltarthat.

### <a name="configure-your-adp-services-for-federated-access"></a>Az összevont hozzáférést a ADP szolgáltatás(ok) konfigurálása

>[!Important]
> Az alkalmazottak, akik ADP szolgáltatásait összevont hozzáférést igényelnek a ADP service-alkalmazáshoz, és ezt követően kell hozzárendelni, a felhasználók kell hozzárendelni, az adott ADP szolgáltatásba.
A ADP képviselőjével megerősítését megérkezésekor a ADP szolgáltatás(ok) és hozzárendelése és kezelése felhasználók beállítása az adott ADP szolgáltatásokhoz való hozzáférés szabályozása.

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ADP**válassza **ADP** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában ADP](common/search-new-app.png)

5. Az Azure Portalon az a **ADP** alkalmazás integrációs oldalán kattintson a **tulajdonságai lap** , és hajtsa végre az alábbi lépéseket:  

    ![Egyszeri bejelentkezés társított tulajdonságok](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Állítsa be a **engedélyezett a felhasználók bejelentkezhetnek** érték mező **Igen**.

    b.  Állítsa be a **felhasználó-hozzárendelés szükséges** érték mező **Igen**.

    c.  Állítsa be a **a felhasználók számára látható** érték mező **Igen**.

6. Az a [az Azure portal](https://portal.azure.com/), az a **ADP** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

7. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **mód** , **társított**. az alkalmazás összekapcsolása **ADP**.

    ![Egyszeri bejelentkezés társított](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Keresse meg a **bejelentkezési URL-cím konfigurálása** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés prop](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Illessze be a **felhasználói hozzáférési URL-címe**, amely a fent másolta **tulajdonságai lap** (az alkalmazásból fő ADP).
                                                             
    b. Az alábbiakban az 5 alkalmazásokról, amelyek támogatják a különböző **továbbítási állapot URL-címek**. Rendelkezik a megfelelő hozzáfűzni kívánt **továbbító állapot URL-címe** manuálisan az adott alkalmazás értékét a **felhasználói hozzáférési URL-címe**.
    
    * **Most már a ADP dolgozók számára**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP munkaerő továbbfejlesztettük az idő**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **HCM ADP Vantage**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **HR ADP Enterprise**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Mentés** a módosításokat.

10. A ADP képviselőjével megerősítését beérkezésekor egy vagy két felhasználókkal teszt kezdődik.

    a. Néhány felhasználók hozzárendelése a ADP szolgáltatás összevont hozzáférési tesztelni az alkalmazást.

    b. Teszt akkor sikeres, ha a felhasználók a katalógusban az ADP service-alkalmazás elérésére, és érhetik el a ADP szolgáltatást.
 
11. A megerősítés sikeres vizsgálat rendelje hozzá a az összevont ADP szolgáltatás egyes felhasználókat vagy felhasználói csoportok, amelyek az oktatóanyag későbbi részében kifejtett és bevezetése az alkalmazottaknak.
 
### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ADP Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **ADP**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **ADP**.

    ![Az alkalmazások listáját a ADP hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-adp-test-user"></a>ADP tesztfelhasználó létrehozása

Ez a szakasz célja ADP Britta Simon nevű felhasználó létrehozásához. Együttműködve [ADP támogatási csapatának](https://www.adp.com/contact-us/overview.aspx) a felhasználók hozzáadása a ADP fiókban. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ADP csempére kattint, meg kell lehet automatikusan bejelentkezett a ADP, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

