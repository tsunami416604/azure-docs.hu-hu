---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zscaler ZSCloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Zscaler ZSCloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: ea6008092f8fbfde8963da50d5273d36a4a30fe3
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050010"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Oktatóanyag: Azure Active Directory-integráció az Zscaler ZSCloud

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler ZSCloud integrálható az Azure Active Directory (Azure AD).

Zscaler ZSCloud integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Zscaler ZSCloud Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Zscaler ZSCloud (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler ZSCloud, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Zscaler ZSCloud egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zscaler ZSCloud hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Zscaler ZSCloud hozzáadása a katalógusból
Az Azure AD integrálása a Zscaler ZSCloud konfigurálásához hozzá kell Zscaler ZSCloud a galériából a felügyelt SaaS-alkalmazások listájára.

**Zscaler ZSCloud hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Zscaler ZSCloud**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. Az eredmények panelen válassza ki a **Zscaler ZSCloud**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján Zscaler ZSCloud az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zscaler ZSCloud mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zscaler ZSCloud hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a Zscaler ZSCloud.

Az Azure AD egyszeri bejelentkezés a Zscaler ZSCloud tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Proxybeállítások konfigurálása](#configuring-proxy-settings)**  – az Internet Explorerben a Proxybeállítások konfigurálása
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Zscaler ZSCloud tesztfelhasználó létrehozása](#creating-a-zscaler-zscloud-test-user)**  – egy megfelelője a Britta Simon Zscaler ZSCloud, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Zscaler ZSCloud alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zscaler ZSCloud, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zscaler ZSCloud** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. Az a **Zscaler ZSCloud tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     Az a **bejelentkezési URL-** beviteli mező, írja be az URL-cím való bejelentkezés ZScaler ZSCloud alkalmazását a felhasználók által használt-e.
    
    > [!NOTE] 
    > Frissíteni ezt az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [Zscaler ZSCloud ügyfél-támogatási csapatának](https://help.zscaler.com/zia) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_general_400.png)

6. Az a **Zscaler ZSCloud konfigurációs** területén kattintson **konfigurálása Zscaler ZSCloud** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a ZScaler ZSCloud vállalati hely rendszergazdaként.

8. A felső menüben kattintson **felügyeleti**.
   
    ![Felügyeleti](./media/zscaler-zscloud-tutorial/ic800206.png "felügyelete")

9. A **szerepkörök és a rendszergazdák kezelése**, kattintson a **felhasználók kezelése és a hitelesítési**.   
            
    ![Felhasználók és hitelesítés kezeléséhez](./media/zscaler-zscloud-tutorial/ic800207.png "felhasználók és hitelesítés kezeléséhez")

10. Az a **hitelesítési beállítások kiválasztása a szervezet** szakaszban, hajtsa végre az alábbi lépéseket:   
                
    ![Hitelesítési](./media/zscaler-zscloud-tutorial/ic800208.png "hitelesítés")
   
    a. Válassza ki **hitelesítés SAML egyszeri bejelentkezés használatával**.

    b. Kattintson a **SAML egyszeri bejelentkezés paramétereinek konfigurálása**.

11. Az a **konfigurálása SAML egyszeri bejelentkezéses paraméterek** párbeszédpanel lapon hajtsa végre az alábbi lépéseket, és kattintson **kész**

    ![Egyszeri bejelentkezés](./media/zscaler-zscloud-tutorial/ic800209.png "egyszeri bejelentkezés")
    
    a. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** be értéket a **URL-címe, amelyhez a felhasználók eljuthatnak a hitelesítéshez a SAML-portál** szövegmezőbe.
    
    b. Az a **bejelentkezési nevet tartalmazó attribútum** szövegmezőbe írja be **NameID**.
    
    c. A letöltött tanúsítvány feltöltéséhez kattintson **Zscaler pem**.
    
    d. Válassza ki **SAML automatikus kiépítés engedélyezése**.

12. Az a **felhasználói hitelesítés konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

    ![Felügyeleti](./media/zscaler-zscloud-tutorial/ic800210.png "felügyelete")
    
    a. Kattintson a **Save** (Mentés) gombra.

    b. Kattintson a **aktiválása**.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el **az Internet Explorer**.

2. Válassza ki **Internetbeállítások** származó a **eszközök** nyílt menüje a **Internetbeállítások** párbeszédpanel.   
    
     ![Internetbeállítások](./media/zscaler-zscloud-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-zscloud-tutorial/ic769493.png "kapcsolatok")

4. Kattintson a **LAN-beállítások** megnyitásához a **LAN-beállítások** párbeszédpanel.

5. A Proxy server szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-zscloud-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki **proxykiszolgáló használata a helyi hálózaton**.

    b. A cím szövegmezőbe írja be **gateway.zscalerone.net**.

    c. Írja be a Port szövegmező **80-as**.

    d. Válassza ki **proxykiszolgáló kihagyása helyi címek esetén**.

    e. Kattintson a **OK** gombra kattintva zárja be a **helyi hálózati (LAN) beállításai** párbeszédpanel.

6. Kattintson a **OK** gombra kattintva zárja be a **Internetbeállítások** párbeszédpanel.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Zscaler ZSCloud tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ZScaler ZSCloud jelentkezzen be, akkor ki kell építenie ZScaler ZSCloud.  
ZScaler ZSCloud, esetén kiépítése a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Zscaler** bérlő.

2. Kattintson a **felügyeleti**.   
   
    ![Felügyeleti](./media/zscaler-zscloud-tutorial/ic781035.png "felügyelete")

3. Kattintson a **felhasználókezelés**.   
        
     ![Adjon hozzá](./media/zscaler-zscloud-tutorial/ic781037.png "hozzáadása")

4. Az a **felhasználók** lapra, majd **Hozzáadás**.
      
    ![Adjon hozzá](./media/zscaler-zscloud-tutorial/ic781037.png "hozzáadása")

5. A felhasználó hozzáadása a szakaszban a következő lépésekkel:
        
    ![Felhasználó hozzáadása](./media/zscaler-zscloud-tutorial/ic781038.png "felhasználó hozzáadása")
   
    a. Írja be a **UserID**, **felhasználó megjelenített neve**, **jelszó**, **jelszó megerősítése**, majd válassza ki **csoportok**és a **részleg** kiépítendő érvényes AAD-fiókok.

    b. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más ZScaler ZSCloud felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését ZScaler ZSCloud által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zscaler ZSCloud Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Zscaler ZSCloud, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Zscaler ZSCloud**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha szeretné tesztelni az egyszeri bejelentkezés beállításai, nyissa meg a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler ZSCloud csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zscaler ZSCloud alkalmazásba.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-zscloud-tutorial/tutorial_general_203.png

