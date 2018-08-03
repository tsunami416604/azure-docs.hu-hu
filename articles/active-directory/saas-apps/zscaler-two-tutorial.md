---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zscaler két |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Zscaler két között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 4f44f6eef4c40f60de8af0a7ca517b0f28cd34f4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438392"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Oktatóanyag: Azure Active Directory-integráció az Zscaler két

Ebben az oktatóanyagban megismerheti, hogyan integrálható a Zscaler két Azure Active Directoryval (Azure AD).

Az Azure AD integrálása Zscaler két nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Zscaler két Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Zscaler két (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler két, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Zscaler két egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból Zscaler két hozzáadása
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zscaler-two-from-the-gallery"></a>A katalógusból Zscaler két hozzáadása
Az Azure AD integrálása a Zscaler két konfigurálásához hozzá kell Zscaler két a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Zscaler két a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Zscaler két**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/tutorial_zscalertwo_search.png)

1. Az eredmények panelen válassza ki a **Zscaler két**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zscaler két "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zscaler két mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zscaler két hivatkozás kapcsolatát kell létrehozni.

A Zscaler két hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés a Zscaler két tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Proxybeállítások konfigurálása](#configuring-proxy-settings)**  – az Internet Explorerben a Proxybeállítások konfigurálása
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A Zscaler két tesztfelhasználót létrehozása](#creating-a-zscaler-two-test-user)**  – egy megfelelője a Britta Simon Zscaler két, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Zscaler két alkalmazás egyszeri bejelentkezés konfigurálása.

**A Zscaler két konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zscaler két** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_zscalertwo_samlbase.png)

1. Az a **Zscaler két tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

   A bejelentkezési URL-címe szövegmezőbe írja be a bejelentkezéshez a ZScaler két alkalmazásnak a felhasználók által használt URL-cím.

    > [!NOTE] 
    > Frissíteni ezt az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Kapcsolattartó [Zscaler két ügyfél-támogatási csapatának](https://www.zscaler.com/company/contact) beolvasni ezeket az értékeket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_general_400.png)

1. Az a **Zscaler két konfigurációs** területén kattintson **Zscaler két konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_zscalertwo_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a ZScaler két vállalati hely rendszergazdaként.

1. A felső menüben kattintson **felügyeleti**.
   
    ![Felügyeleti](./media/zscaler-two-tutorial/ic800206.png "felügyelete")

1. A **szerepkörök és a rendszergazdák kezelése**, kattintson a **felhasználók kezelése és a hitelesítési**.   
            
    ![Felhasználók és hitelesítés kezeléséhez](./media/zscaler-two-tutorial/ic800207.png "felhasználók és hitelesítés kezeléséhez")

1. Az a **hitelesítési beállítások kiválasztása a szervezet** szakaszban, hajtsa végre az alábbi lépéseket:   
                
    ![Hitelesítési](./media/zscaler-two-tutorial/ic800208.png "hitelesítés")
   
    a. Válassza ki **hitelesítés SAML egyszeri bejelentkezés használatával**.

    b. Kattintson a **SAML egyszeri bejelentkezés paramétereinek konfigurálása**.

1. Az a **konfigurálása SAML egyszeri bejelentkezéses paraméterek** párbeszédpanel lapon hajtsa végre az alábbi lépéseket, és kattintson **kész**

    ![Egyszeri bejelentkezés](./media/zscaler-two-tutorial/ic800209.png "egyszeri bejelentkezés")
    
    a. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról történő másolta a **URL-címe, amelyhez a felhasználók eljuthatnak a hitelesítéshez a SAML-portál** szövegmezőbe.
    
    b. Az a **bejelentkezési nevet tartalmazó attribútum** szövegmezőbe írja be **NameID**.
    
    c. A letöltött tanúsítvány feltöltéséhez kattintson **Zscaler pem**.
    
    d. Válassza ki **SAML automatikus kiépítés engedélyezése**.

1. Az a **felhasználói hitelesítés konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

    ![Felügyeleti](./media/zscaler-two-tutorial/ic800210.png "felügyelete")
    
    a. Kattintson a **Save** (Mentés) gombra.

    b. Kattintson a **aktiválása**.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A Proxybeállítások konfigurálása az Internet Explorerben

1. Indítsa el **az Internet Explorer**.

1. Válassza ki **Internetbeállítások** származó a **eszközök** nyílt menüje a **Internetbeállítások** párbeszédpanel.   
    
     ![Internetbeállítások](./media/zscaler-two-tutorial/ic769492.png "Internetbeállítások")

1. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/zscaler-two-tutorial/ic769493.png "kapcsolatok")

1. Kattintson a **LAN-beállítások** megnyitásához a **LAN-beállítások** párbeszédpanel.

1. A Proxy server szakaszban hajtsa végre az alábbi lépéseket:   
   
    ![Proxykiszolgáló](./media/zscaler-two-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki **proxykiszolgáló használata a helyi hálózaton**.

    b. A cím szövegmezőbe írja be **gateway.zscalertwo.net**.

    c. Írja be a Port szövegmező **80-as**.

    d. Válassza ki **proxykiszolgáló kihagyása helyi címek esetén**.

    e. Kattintson a **OK** gombra kattintva zárja be a **helyi hálózati (LAN) beállításai** párbeszédpanel.

1. Kattintson a **OK** gombra kattintva zárja be a **Internetbeállítások** párbeszédpanel.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/zscaler-two-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-zscaler-two-test-user"></a>Zscaler két tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be a ZScaler két Azure AD-felhasználók, akkor ki kell építeni a ZScaler két. ZScaler két esetén kiépítése a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Zscaler két** bérlő.

1. Kattintson a **felügyeleti**.   
   
    ![Felügyeleti](./media/zscaler-two-tutorial/ic781035.png "felügyelete")

1. Kattintson a **felhasználókezelés**.   
        
     ![Adjon hozzá](./media/zscaler-two-tutorial/ic781036.png "hozzáadása")

1. Az a **felhasználók** lapra, majd **Hozzáadás**.
      
    ![Adjon hozzá](./media/zscaler-two-tutorial/ic781037.png "hozzáadása")

1. A felhasználó hozzáadása a szakaszban a következő lépésekkel:
        
    ![Felhasználó hozzáadása](./media/zscaler-two-tutorial/ic781038.png "felhasználó hozzáadása")
   
    a. Írja be a **UserID**, **felhasználó megjelenített neve**, **jelszó**, **jelszó megerősítése**, majd válassza ki **csoportok**és a **részleg** egy érvényes Azure AD-fiókot kíván üzembe helyezni.

    b. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> ZScaler két felhasználói fiók létrehozása eszközöket és két ZScaler által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zscaler két Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Zscaler két, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Zscaler két**.

    ![Egyszeri bejelentkezés konfigurálása](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a Zscaler két csempét a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett a Zscaler két alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-two-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-two-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-two-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-two-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-two-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-two-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-two-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-two-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-two-tutorial/tutorial_general_203.png

