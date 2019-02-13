---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iLMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68f76f6ba451ff5f3e81b3aaabf3825155ba15
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201533"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iLMS

Ebben az oktatóanyagban elsajátíthatja, hogyan iLMS integrálása az Azure Active Directory (Azure AD).

ILMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá iLMS Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az Azure AD-fiókjukkal (egyszeri bejelentkezés) iLMS
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ILMS az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy iLMS egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ILMS hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ilms-from-the-gallery"></a>ILMS hozzáadása a katalógusból
Az Azure AD integrálása a iLMS konfigurálásához hozzá kell iLMS a katalógusból a felügyelt SaaS-alkalmazások listájára.

**ILMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **iLMS**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/tutorial_ilms_search.png)

1. Az eredmények panelen válassza ki a **iLMS**, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés iLMS a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó iLMS mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó iLMS hivatkozás kapcsolata kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** iLMS a.

Az Azure AD egyszeri bejelentkezés az iLMS tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy iLMS tesztfelhasználó létrehozása](#creating-an-ilms-test-user)**  – egy megfelelője a Britta Simon iLMS, amely kapcsolódik az Azure ad-ben ábrázolása számára a rendelkeznie.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és iLMS alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **iLMS** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

1. Az a **iLMS tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. Az a **azonosító** szövegmezőjébe illessze be a **azonosító** másolt érték **szolgáltató** SAML beállításai iLMS felügyeleti portálon.

    b. Az a **válasz URL-cím** szövegmezőjébe illessze be a **végpontja (URL)** másolt érték **szolgáltató** SAML-beállítások a következő mintának kellene iLMS felügyeleti portál szakaszában `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >A "123456" Példa érték azonosító.

1. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_url1.png)

    Az a **bejelentkezési URL-** szövegmezőjébe illessze be a **végpontja (URL)** másolt érték **szolgáltató** iLMS felügyeleti portálon, az SAML-beállításai `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

1. Igény szerinti kiépítés engedélyezéséhez iLMS alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/4.png)
    
    Hozzon létre **szervezeti egység, régió** és **osztás** attribútumok, és adja hozzá ezek az attribútumok nevével iLMS. Az összes ezek az attribútumok fenti szükség.  

    > [!NOTE] 
    > Engedélyeznie kell a **Un-recognized felhasználói fiók létrehozása** a iLMS ezek az attribútumok leképezésére. Kövesse az utasításokat [Itt](http://support.inspiredelearning.com/customer/portal/articles/2204526) az attribútumok konfigurációjában képet kapjon.

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | körzet | user.department |
    | régió | User.state |
    | Szervezeti egység | user.jobtitle |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban, jelentkezzen be a **iLMS felügyeleti portál** rendszergazdaként.

1. Kattintson a **SSO:SAML** alatt **beállítások** lapon nyissa meg az SAML-beállítások, és hajtsa végre az alábbi lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png) 

    a. Bontsa ki a **szolgáltató** szakaszt, és másolja a **azonosító** és **végpontja (URL)** értéket.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

    b. A **identitásszolgáltató** területén kattintson **metaadatok importálása**.
    
    c. Válassza ki a **metaadatok** az Azure-portálról letöltött fájl **SAML-aláíró tanúsítvány** szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Ha szeretné engedélyezni, igény szerinti kiépítés iLMS fiókok létrehozása az ENSZ-ismeri fel a felhasználók, kövesse az alábbi lépéseket:
        
       - Ellenőrizze **nem felismert felhasználói fiók létrehozása**.
       
       ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Az attribútumok leképezésére iLMS attribútumokat az Azure AD-ben. Az attribútum oszlopának adja meg az attribútum neve vagy az alapértelmezett értéket.

    e. Lépjen a **üzleti szabályok** lapra, és hajtsa végre az alábbi lépéseket: 
        
       ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

       - Ellenőrizze **Un-recognized régióban hozzon létre, részlegek és szervezeti egységek** hozhat létre a régióban, részlegek és szervezeti egységek, amelyek még nem léteznek az egyszeri bejelentkezés időpontjában.
        
       - Ellenőrizze **Update felhasználói profil során bejelentkezési** , adja meg, hogy a felhasználói profil frissül minden egyszeri bejelentkezés. 
        
       - Ha a **"Frissítés üres értékek a nem kötelező mezőket a felhasználói profil"** beállítás be van jelölve, a profil nem kötelező mezők, amelyek üres lesz bejelentkezés után is a felhasználó iLMS profilt tartalmazhatnak üres értékeket az adott mezők.
        
       - Ellenőrizze **hiba az értesítési E-mail küldése** , és adja meg az e-mail a felhasználó, ahol a hiba értesítő e-mailt kapni szeretné.

1. Kattintson a **mentése** gombra kattintva mentse a beállításokat.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/save.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-ilms-test-user"></a>Egy iLMS tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére automatikusan létrehozza az alkalmazásban. Igény szerinti fog működni, ha akkor kattintott a **Un-recognized felhasználói fiók létrehozása** jelölőnégyzet iLMS felügyeleti portálon található SAML konfigurációs beállítása során.

Ha létrehoz egy felhasználót manuálisan kell, majd kövesse az alábbi lépéseket:

1. Jelentkezzen be rendszergazdaként a iLMS vállalati webhely.

1. Kattintson a **"Felhasználó regisztrálása"** alatt **felhasználók** elemére kattintva nyissa meg **regisztrálása felhasználó** lapot. 
   
   ![Alkalmazott hozzáadása](./media/ilms-tutorial/3.png)

1. Az a **"Felhasználó regisztrálása"** lapon, a következő lépésekkel.

    ![Alkalmazott hozzáadása](./media/ilms-tutorial/create_testuser_add.png)

    a. Az a **Utónév** szövegmezőbe írja be az első név Britta.
   
    b. Az a **Vezetéknév** szövegmezőbe írja be a vezetéknevet Simon.

    c. Az a **E-mail-azonosító** szövegmezőbe írja be a Britta Simon fiók e-mail-címét.

    d. Az a **régió** legördülő menüben válassza ki a régiót értékét.

    e. Az a **osztás** legördülő menüben válassza ki a részleg értékét.

    f. Az a **részleg** legördülő menüben válassza ki a részleg értékét.

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > Felhasználói regisztráció e-mail a kiválasztásával küldhet **regisztrációs üzenet küldése** jelölőnégyzetet.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezést saját iLMS való hozzáférést.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel iLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **iLMS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a iLMS csempére kattint, meg kell lekérése automatikusan bejelentkezett az iLMS alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

