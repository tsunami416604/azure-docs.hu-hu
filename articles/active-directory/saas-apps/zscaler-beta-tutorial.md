---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zscaler béta |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Zscaler béta között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: f3212e55eab7ce74a44da9948cad8a0599580f42
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050545"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Oktatóanyag: Azure Active Directory-integráció az Zscaler bétaverzió

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler béta integrálása az Azure Active Directory (Azure AD).

Zscaler béta integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Zscaler béta Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Zscaler bétaverzióra (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler Beta, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- A Zscaler béta egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zscaler béta hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zscaler-beta-from-the-gallery"></a>Zscaler béta hozzáadása a katalógusból
Zscaler béta integrálása az Azure AD beállítása, hozzá kell Zscaler béta a galériából a felügyelt SaaS-alkalmazások listájára.

**Zscaler béta hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Zscaler béta**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_search.png)

5. Az eredmények panelen válassza ki a **Zscaler béta**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és tesztelés az Azure AD egyszeri bejelentkezés Zscaler béta "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zscaler bétaverziós mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a Zscaler bétaverziónak kapcsolódó felhasználó hivatkozás kapcsolatának kell létrehozni.

Zscaler bétaverzió, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Zscaler béta tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Proxybeállítások konfigurálása](#configuring-proxy-settings)**  – az Internet Explorerben a Proxybeállítások konfigurálása
3. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Zscaler béta tesztfelhasználó létrehozása](#creating-a-zscaler-beta-test-user)**  – a Zscaler bétaverziónak kapcsolódó felhasználói reprezentációja az Azure AD-megfelelője a Britta Simon rendelkezik.
5. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Zscaler béta alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zscaler bétaverzió, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zscaler béta** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_samlbase.png)

3. Az a **Zscaler béta tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_url.png)

    A bejelentkezési URL-címe szövegmezőbe írja be a bejelentkezéshez Zscaler béta alkalmazását a felhasználók által használt URL-CÍMÉT.

    > [!NOTE] 
    > Frissíteni ezt az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [Zscaler bétaverziós ügyfélalkalmazását támogatási csapatának](https://www.zscaler.com/company/contact) lekérni ezt az értéket. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_general_400.png)

6. Az a **Zscaler béta konfigurációs** területén kattintson **Zscaler béta konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a Zscaler béta vállalati hely rendszergazdaként.

8. A felső menüben kattintson **felügyeleti**.
   
    ![Felügyeleti](./media/zscaler-beta-tutorial/ic800206.png "felügyelete")

9. A **szerepkörök és a rendszergazdák kezelése**, kattintson a **felhasználók kezelése és a hitelesítési**.   
            
    ![Felhasználók és hitelesítés kezeléséhez](./media/zscaler-beta-tutorial/ic800207.png "felhasználók és hitelesítés kezeléséhez")

10. Az a **hitelesítési beállítások kiválasztása a szervezet** szakaszban, hajtsa végre az alábbi lépéseket:   
                
    ![Hitelesítési](./media/zscaler-beta-tutorial/ic800208.png "hitelesítés")
   
    a. Válassza ki **hitelesítés SAML egyszeri bejelentkezés használatával**.

    b. Kattintson a **SAML egyszeri bejelentkezés paramétereinek konfigurálása**.

11. Az a **konfigurálása SAML egyszeri bejelentkezéses paraméterek** párbeszédpanel lapon hajtsa végre az alábbi lépéseket, és kattintson **kész**

    ![Egyszeri bejelentkezés](./media/zscaler-beta-tutorial/ic800209.png "egyszeri bejelentkezés")
    
    a. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **URL-címe, amelyhez a felhasználók eljuthatnak a hitelesítéshez a SAML-portál** szövegmezőbe.
    
    b. Az a **bejelentkezési nevet tartalmazó attribútum** szövegmezőbe írja be **NameID**.
    
    c. A letöltött tanúsítvány feltöltéséhez kattintson **Zscaler pem**.
    
    d. Válassza ki **SAML automatikus kiépítés engedélyezése**.

12. Az a **felhasználói hitelesítés konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

    ![Felügyeleti](./media/zscaler-beta-tutorial/ic800210.png "felügyelete")
    
    a. Kattintson a **Save** (Mentés) gombra.

    b. Kattintson a **aktiválása**.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el **az Internet Explorer**.

2. Válassza ki **Internetbeállítások** származó a **eszközök** nyílt menüje a **Internetbeállítások** párbeszédpanel.   
    
     ![Internetbeállítások](./media/zscaler-beta-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-beta-tutorial/ic769493.png "kapcsolatok")

4. Kattintson a **LAN-beállítások** megnyitásához a **LAN-beállítások** párbeszédpanel.

5. A Proxy server szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-beta-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki **proxykiszolgáló használata a helyi hálózaton**.

    b. A cím szövegmezőbe írja be **gateway.zscalerbeta.net**.

    c. Írja be a Port szövegmező **80-as**.

    d. Válassza ki **proxykiszolgáló kihagyása helyi címek esetén**.

    e. Kattintson a **OK** gombra kattintva zárja be a **helyi hálózati (LAN) beállításai** párbeszédpanel.

6. Kattintson a **OK** gombra kattintva zárja be a **Internetbeállítások** párbeszédpanel.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-beta-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-zscaler-beta-test-user"></a>Zscaler béta tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Zscaler bétaverzióra történő bejelentkezéshez, akkor ki kell építeni Zscaler bétaverzióra. Zscaler Beta esetén kiépítése a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Zscaler béta** bérlő.

2. Kattintson a **felügyeleti**.   
   
    ![Felügyeleti](./media/zscaler-beta-tutorial/ic781035.png "felügyelete")

3. Kattintson a **felhasználókezelés**.   
        
     ![Adjon hozzá](./media/zscaler-beta-tutorial/ic781036.png "hozzáadása")

4. Az a **felhasználók** lapra, majd **Hozzáadás**.
      
    ![Adjon hozzá](./media/zscaler-beta-tutorial/ic781037.png "hozzáadása")

5. A felhasználó hozzáadása a szakaszban a következő lépésekkel:
        
    ![Felhasználó hozzáadása](./media/zscaler-beta-tutorial/ic781038.png "felhasználó hozzáadása")
   
    a. Írja be a **UserID**, **felhasználó megjelenített neve**, **jelszó**, **jelszó megerősítése**, majd válassza ki **csoportok**és a **részleg** egy érvényes Azure AD-fiókot kíván üzembe helyezni.

    b. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Zscaler béta felhasználói fiók létrehozása eszközöket és Zscaler bétaverzió által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zscaler béta Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Zscaler bétaverzió, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Zscaler béta**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler béta csempére kattint, meg kell beolvasása automatikusan bejelentkezett a Zscaler béta alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-beta-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-beta-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-beta-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-beta-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-beta-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-beta-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-beta-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-beta-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-beta-tutorial/tutorial_general_203.png

