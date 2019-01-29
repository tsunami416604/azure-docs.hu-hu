---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TimeOffManager |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TimeOffManager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: fc114d59ae97813c0ce8f70a6ad58f0faf02a47c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157618"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TimeOffManager

Ebben az oktatóanyagban elsajátíthatja, hogyan TimeOffManager integrálása az Azure Active Directory (Azure AD).

TimeOffManager integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá TimeOffManager Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TimeOffManager (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

TimeOffManager az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy TimeOffManager egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TimeOffManager hozzáadása a katalógusból
1. Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-timeoffmanager-from-the-gallery"></a>TimeOffManager hozzáadása a katalógusból
Az Azure AD integrálása a TimeOffManager konfigurálásához hozzá kell TimeOffManager a katalógusból a felügyelt SaaS-alkalmazások listájára.

**TimeOffManager hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **TimeOffManager**válassza **TimeOffManager** eredmény panelen, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Katalógus hozzáadása](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TimeOffManager a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TimeOffManager mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TimeOffManager hivatkozás kapcsolata kell létrehozni.

TimeOffManager, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az TimeOffManager tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy TimeOffManager tesztfelhasználót](#create-a-timeoffmanager-test-user)**  – egy megfelelője a Britta Simon TimeOffManager, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és TimeOffManager alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés TimeOffManager, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TimeOffManager** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![SAML-alapú bejelentkezésre](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

1. Az a **TimeOffManager tartomány és URL-címek** területén tegye a következőket:

     ![A szakasz TimeOffManager tartomány és URL-címek](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges válasz URL-cím. Ezt az értéket kap **egyszeri bejelentkezést a beállítások lapon** amely ismertetését később az oktatóanyag, vagy forduljon a [TimeOffManager támogatási csapatának](https://www.purelyhr.com/contact-us).
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítvány szakaszban](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

1. Ez a szakasz célja kidolgozására engedélyezése a felhasználóknak a hitelesítést TimeOffManger fiókkal az Azure AD összevonási SAML protokoll használatával.
    
    TimeOffManger alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![SAML-jogkivonat attribútumai](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "saml-jogkivonat attribútumai")
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | Keresztnév |User.givenname |
    | Vezetéknév |User.surname |
    | E-mail |User.mail |
    
    a.  Kattintson a fenti táblázatban szereplő minden egyes adatok sorban **adja hozzá a felhasználói attribútum**.
    
    ![SAML-jogkivonat attribútumai](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "saml-jogkivonat attribútumai")
    
    ![SAML-jogkivonat attribútumai](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "saml-jogkivonat attribútumai")
    
    b.  Az a **attribútumnév** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c.  Az a **attribútumérték** szövegmezőben válassza ki az adott sorhoz feltüntetett attribútum értéke.
    
    d.  Kattintson az **OK** gombra.
    
1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/timeoffmanager-tutorial/tutorial_general_400.png)

1. Az a **TimeOffManager konfigurációs** területén kattintson **konfigurálása TimeOffManager** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![TimeOffManager konfigurációs szakasz](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a TimeOffManager vállalati hely rendszergazdaként.

1. Lépjen a **fiók \> fiókokat \> egyszeri bejelentkezési beállításainak**.
   
   ![Egyszeri bejelentkezés beállításai](./media/timeoffmanager-tutorial/ic795917.png "egyszeri bejelentkezés beállításai")
1. Az a **egyszeri bejelentkezési beállításainak** szakaszban, hajtsa végre az alábbi lépéseket:
   
   ![Egyszeri bejelentkezés beállításai](./media/timeoffmanager-tutorial/ic795918.png "egyszeri bejelentkezés beállításai")
   
   a. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **X.509-tanúsítvány** szövegmezőbe.
   
   b. A **identitásszolgáltató kibocsátója** szövegmezőjébe illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.
   
   c. A **IdP-végpont URL-címe** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
   
   d. Mint **kényszerítése SAML**válassza **nem**.
   
   e. Mint **automatikus létrehozása a felhasználók**válassza **Igen**.
   
   f. A **kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.
   
   g. Kattintson a **módosítások mentése**.

1. A **egyszeri bejelentkezési beállításai** lapon, másolja az értéket a **helyességi feltétel fogyasztói URL-címe** , és illessze be a a **válasz URL-cím** alatti szövegmezőbe **TimeOffManager Tartomány és URL-címek** szakaszban az Azure Portalon. 

      ![Egyszeri bejelentkezés beállításai](./media/timeoffmanager-tutorial/ic795915.png "egyszeri bejelentkezés beállításai")

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok--> minden felhasználó](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Hozzáadás gomb](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Felhasználói párbeszédpanel lap](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-timeoffmanager-test-user"></a>TimeOffManager tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TimeOffManager szolgáltatásba való bejelentkezéshez, akkor ki kell építenie TimeOffManager.  

TimeOffManager csak idő felhasználókiépítés támogatja. Nincs művelet elem az Ön számára.  

A felhasználók egyszeri bejelentkezés használata az első bejelentkezéskor automatikusan hozzáadódnak.

>[!NOTE]
>Eszközt is használhat bármilyen más TimeOffManager felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése TimeOffManager által biztosított API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TimeOffManager Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel TimeOffManager, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **TimeOffManager**.

    ![Az Alkalmazáslista TimeOffManager](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a TimeOffManager csempére kattint, meg kell lekérése automatikusan bejelentkezett az TimeOffManager alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

