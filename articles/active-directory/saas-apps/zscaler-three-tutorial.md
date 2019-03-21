---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler három |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Zscaler három között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eced1b92aba24c8083cbdc2ece099fab95567db7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877955"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>Oktatóanyag: A Zscaler három Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler három integrálása az Azure Active Directory (Azure AD).

Az Azure AD integrálása Zscaler három nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Zscaler három Azure AD-ben.
- A felhasználók automatikusan első bejelentkezett Zscaler három (egyszeri bejelentkezés), engedélyezheti az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler három, a következőkre van szükség:

- Azure AD-előfizetés
- A Zscaler három egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból Zscaler három hozzáadása
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zscaler-three-from-the-gallery"></a>A katalógusból Zscaler három hozzáadása

Az Azure AD integrálása a Zscaler három konfigurálásához hozzá kell Zscaler három a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zscaler három a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Zscaler három**válassza **Zscaler három** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában három Zscaler](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zscaler három a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zscaler három mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zscaler három hivatkozás kapcsolatát kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Zscaler három tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zscaler három egyszeri bejelentkezés konfigurálása](#configure-zscaler-three-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Hozzon létre három Zscaler tesztfelhasználót](#create-zscaler-three-test-user)**  – egy megfelelője a Britta Simon Cisco rendszerére, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és három Zscaler alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a Zscaler három, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zscaler három** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](common/tutorial_general_301.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](common/editconfigure.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Zscaler három tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://login.zscalerthree.net/sfc_sso`

5. Zscaler három alkalmazást az SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok & jogcímek** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML lap**, kattintson a **szerkesztése** gombra kattintva nyissa meg **felhasználói attribútumok & jogcímek** párbeszédpanel.

    ![Az attribútumkapcsolat](./media/zscaler-three-tutorial/tutorial_zscalerthree_attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név)  | Adatforrás-attribútum  |
    | ---------| ------------ |
    | Tagja(Pénzügy)     | user.assignedroles |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. Az a **forrásattribútum** listájában, selelct az attribútum értéke.

    c. Kattintson az **OK** gombra.

    d. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Kattintson a [Itt](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) tudni, hogy az Azure AD-szerepkör konfigurálása

7. Az a **SAML-aláíró tanúsítvány** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez **tanúsítvány (Base64)**, és mentse a tanúsítványfájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png) 

8. Az a **Zscaler három beállítása** területén másolja a megfelelő URL-címet a követelmény alapján.

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

    ![Zscaler három konfiguráció](common/configuresection.png)

### <a name="configure-zscaler-three-single-sign-on"></a>Zscaler három egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zscaler három vállalati hely rendszergazdaként.

1. Lépjen a **Adminisztráció > hitelesítés > hitelesítési beállítások** , és hajtsa végre az alábbi lépéseket:
   
   ![Felügyeleti](./media/zscaler-three-tutorial/ic800206.png "felügyelete")

   a. Hitelesítés típusa alatt válassza ki a **SAML**.

   b. Kattintson a **SAML konfigurálása**.

1. Az a **szerkesztése SAML** ablakban hajtsa végre az alábbi lépéseket: kattintson a Mentés gombra.  
            
   ![Felhasználók és hitelesítés kezeléséhez](./media/zscaler-three-tutorial/ic800208.png "felhasználók és hitelesítés kezeléséhez")
    
   a. Az a **SAML portál URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** Azure Portalról másolt.

   b. Az a **bejelentkezési név attribútum** szövegmezőbe írja be **NameID**.

   c. Kattintson a **feltöltése**töltheti fel az Azure SAML aláíró tanúsítvány az Azure Portalról letöltött a **nyilvános SSL-tanúsítvány**.

   d. Váltás a **SAML automatikus kiépítés engedélyezése**.

   e. Az a **felhasználói megjelenítési név attribútum** szövegmezőbe írja be **displayName** szeretné engedélyezni a SAML automatikus kiépítés displayName attribútumok esetén, ha.

   f. Az a **csoport neve attribútum** szövegmezőbe írja be **tagja(Pénzügy)** szeretné engedélyezni a SAML automatikus kiépítés tagja(Pénzügy) attribútumok esetén, ha.

   g. Az a **részleg neve attribútum** Enter **részleg** Ha engedélyezi a SAML automatikus kiépítés részleg attribútumokat.

   i. Kattintson a **Save** (Mentés) gombra.

1. Az a **felhasználói hitelesítés konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

   ![Adminisztráció](./media/zscaler-three-tutorial/ic800207.png)

   a. A kurzort a **aktiválási** menüjének bal alsó.

   b. Kattintson a **aktiválása**.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el **az Internet Explorer**.

1. Válassza ki **Internetbeállítások** származó a **eszközök** nyílt menüje a **Internetbeállítások** párbeszédpanel.   
    
     ![Internetbeállítások](./media/zscaler-three-tutorial/ic769492.png "Internetbeállítások")

1. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-three-tutorial/ic769493.png "kapcsolatok")

1. Kattintson a **LAN-beállítások** megnyitásához a **LAN-beállítások** párbeszédpanel.

1. A Proxy server szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-three-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki **proxykiszolgáló használata a helyi hálózaton**.

    b. A cím szövegmezőbe írja be **átjáró. Zscaler Three.net**.

    c. Írja be a Port szövegmező **80-as**.

    d. Válassza ki **proxykiszolgáló kihagyása helyi címek esetén**.

    e. Kattintson a **OK** gombra kattintva zárja be a **helyi hálózati (LAN) beállításai** párbeszédpanel.

1. Kattintson a **OK** gombra kattintva zárja be a **Internetbeállítások** párbeszédpanel.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![Az Azure AD-felhasználó létrehozása][100]

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_01.png) 

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](common/create_aaduser_02.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-zscaler-three-test-user"></a>Zscaler három tesztfelhasználó létrehozása

Ez a szakasz célja a Zscaler három Britta Simon nevű felhasználó létrehozásához. Zscaler három támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó Zscaler három eléréséhez, ha még nem létezik tett kísérlet során jön létre.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Zscaler három támogatási csoportjának](https://www.zscaler.com/company/contact).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zscaler három Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Zscaler három**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra, majd válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználó **Britta Simon** a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

6. A a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő felhasználói szerepkört a listában, majd kattintson a **válassza** gombra a képernyő alján.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler három csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zscaler három alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
