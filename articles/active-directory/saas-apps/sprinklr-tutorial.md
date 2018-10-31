---
title: 'Oktatóanyag: Azure Active Directory-integráció az Sprinklr |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Sprinklr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: b5f240091cf5ce9331ada818c218db04f508dbc8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241803"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Oktatóanyag: Azure Active Directory-integráció az Sprinklr

Ebben az oktatóanyagban elsajátíthatja, hogyan Sprinklr integrálása az Azure Active Directory (Azure AD).

Sprinklr integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Sprinklr Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Sprinklr (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Sprinklr az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Sprinklr egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Sprinklr hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinklr hozzáadása a katalógusból
Az Azure AD integrálása a Sprinklr konfigurálásához hozzá kell Sprinklr a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Sprinklr hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Sprinklr**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

1. Az eredmények panelen válassza ki a **Sprinklr**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Sprinklr az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Sprinklr mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Sprinklr hivatkozás kapcsolata kell létrehozni.

Sprinklr, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Sprinklr tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Sprinklr tesztfelhasználó létrehozása](#creating-a-sprinklr-test-user)**  – egy megfelelője a Britta Simon Sprinklr, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Sprinklr alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Sprinklr, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Sprinklr** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

1. Az a **Sprinklr tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.sprinklr.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse az értéket a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Sprinklr ügyfél-támogatási csapatának](https://www.sprinklr.com/contact-us/) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sprinklr-tutorial/tutorial_general_400.png)

1. Az a **Sprinklr konfigurációs** területén kattintson **konfigurálása Sprinklr** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

1. Egy másik böngészőablakban jelentkezzen be a Sprinklr vállalati hely rendszergazdaként.

1. Lépjen a **felügyeleti \> beállítások**.
   
    ![Felügyeleti](./media/sprinklr-tutorial/ic782907.png "felügyelete")

1. Lépjen a **kezelése Partner \> egyszeri bejelentkezési** a bal oldali panelén.
   
    ![Partner kezelése](./media/sprinklr-tutorial/ic782908.png "Partner kezelése")

1. Kattintson a **+ Hozzáadás egyetlen bejelentkezéseknél**.
   
    ![Egyszeri bejelentkezés – Ons](./media/sprinklr-tutorial/ic782909.png "bejelentkezéseknél – egyszeri")

1. Az a **az egyszeri bejelentkezés** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés – Ons](./media/sprinklr-tutorial/ic782910.png "bejelentkezéseknél – egyszeri")

    a. Az a **neve** szövegmezőbe írja be a konfiguráció nevét (például: *WAADSSOTest*).

    b. Válassza ki **engedélyezve**.

    c. Válassza ki **új SSO-tanúsítvány használatára**.
             
    e. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató Identitástanúsítványt** szövegmezőbe.

    f. Illessze be a **SAML Entitásazonosító** érték, amely az Azure Portalról másolta a **entitásazonosító** szövegmezőbe.

    g. Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** érték, amely az Azure Portalról másolta a **Identity Provider bejelentkezési URL-cím** szövegmezőbe.

    h. Illessze be a **kijelentkezéses URL-cím** érték, amely az Azure Portalról másolta a **Identity Provider kijelentkezési URL-címe** szövegmezőbe.
     
    i. Mint **SAML felhasználói azonosító típusa**válassza **helyességi feltétel tartalmaz sprinklr.com felhasználónév**.

    j. Mint **SAML felhasználói azonosító hely**, jelölje be **felhasználói Azonosítóját a tulajdonos utasítás Névazonosító elemében van**.

    k. Kattintson a **Save** (Mentés) gombra.
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sprinklr-test-user"></a>Sprinklr tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként a Sprinklr vállalati webhely.

1. Lépjen a **felügyeleti \> beállítások**.
   
    ![Felügyeleti](./media/sprinklr-tutorial/ic782907.png "felügyelete")

1. Lépjen a **kezelése ügyfél \> felhasználók** a bal oldali ablaktáblán.
   
    ![Beállítások](./media/sprinklr-tutorial/ic782914.png "beállításai")

1. Kattintson a **felhasználó hozzáadása**.
   
    ![Beállítások](./media/sprinklr-tutorial/ic782915.png "beállításai")

1. Az a **szerkesztési felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Felhasználó szerkesztése](./media/sprinklr-tutorial/ic782916.png "felhasználó szerkesztése") 

    a. Az a **E-mail**, **Utónév** és **Vezetéknév** , beleértve a szövegdobozokat, írja be az adatokat egy Azure AD felhasználói fiók kíván üzembe helyezni.

    b. Válassza ki **jelszó le van tiltva**.

    c. Válassza ki **nyelvi**.

    d. Válassza ki **felhasználótípus**.

    e. Kattintson a **frissítés**.
   
     >[!IMPORTANT]
     >**Jelszó le van tiltva** meg kell adnia egy felhasználó egy identitásszolgáltató használatával bejelentkezni. 
     
1. Lépjen a **szerepkör**, és hajtsa végre az alábbi lépéseket:
   
    ![Partner-szerepkörök](./media/sprinklr-tutorial/ic782917.png "Partner-szerepkörök")

    a. Az a **globális** listáról válassza ki **összes\_engedélyek**.  

    b. Kattintson a **frissítés**.

>[!NOTE]
>Eszközt is használhat bármilyen más Sprinklr felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Sprinklr által biztosított API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Sprinklr Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Sprinklr, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Sprinklr**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Sprinklr csempére kattint, szerezheti be automatikusan bejelentkezett a Sprinklr alkalmazásba a hozzáférési panelen, kapcsolatos további információt talál [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

