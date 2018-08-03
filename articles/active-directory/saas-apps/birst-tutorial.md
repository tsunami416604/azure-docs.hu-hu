---
title: 'Oktatóanyag: Azure Active Directory-integráció az Birst Agilis üzleti elemzés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Birst Agilis üzleti elemzés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 677183b1-5348-4302-88cc-5c8ab63a3c6c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: f3c9ff07b0cbb7b3f7aa6a23887ef86a0b53af0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427492"
---
# <a name="tutorial-azure-active-directory-integration-with-birst-agile-business-analytics"></a>Oktatóanyag: Azure Active Directory-integráció az Birst Agilis üzleti elemzés céljából

Ebben az oktatóanyagban elsajátíthatja, hogyan Birst Agilis üzleti elemzés integrálása az Azure Active Directory (Azure AD).

Birst Agilis üzleti elemzés integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Birst Agilis üzleti elemzés céljából az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Birst Agilis üzleti Analytics (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Birst Agilis üzleti elemzés céljából, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- Egy Birst Agilis üzleti elemzés egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Birst Agilis üzleti elemzés hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-birst-agile-business-analytics-from-the-gallery"></a>Birst Agilis üzleti elemzés hozzáadása a katalógusból
Konfigurálása az Azure AD integrálása a Birst Agilis üzleti elemzés céljából, hozzá kell Birst Agilis üzleti elemzések a galériából a felügyelt SaaS-alkalmazások listájára.

**Birst Agilis üzleti elemzés hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Birst Agilis üzleti elemzés**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/tutorial_birst_search.png)

1. Az eredmények panelen válassza ki a **Birst Agilis üzleti elemzés**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/tutorial_birst_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés elemzésekkel Birst Agilis üzleti egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Birst Agilis üzleti elemzés céljából mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Birst Agilis üzleti elemzések a hivatkozás kapcsolata kell létrehozni.

Birst Agilis üzleti elemzés céljából, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Birst Agilis üzleti Analytics tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Birst Agilis üzleti elemzés tesztfelhasználó létrehozása](#creating-a-birst-agile-business-analytics-test-user)**  – egy megfelelője a Britta Simon Birst Agilis üzleti elemzés, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Birst Agilis üzleti elemzés alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Birst Agilis üzleti elemzés céljából, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Birst Agilis üzleti elemzés** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_birst_samlbase.png)

1. Az a **Birst Agilis üzleti elemzési tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_birst_url.png)

     Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

     Az URL-címe attól függ, hogy az adatközpontban, hogy megtalálható-e a Birst fiókja: 

     * Egyesült Államokbeli adatközpontok használatra a minta a következő: `https://login.bws.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID` 

     * Európai adatközpontok használja a következő mintának: `https://login.eu1.birst.com/SAMLSSO/Services.aspx?birst.idpid=TENANTIDPID`

    > [!NOTE] 
    > Az érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Birst Agilis üzleti elemzési ügyfél-támogatási csapatának](mailto:info@birst.com) a gépkulcsengedélyek értékének. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_birst_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_general_400.png)

1. Az a **Birst Agilis üzleti elemzési konfigurációs** területén kattintson **konfigurálása Birst Agilis üzleti elemzés** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_birst_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Birst Agilis üzleti elemzés** oldalon kell küldenie a letöltött **tanúsítvány (Base64)**, **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezés Szolgáltatás URL-címe** való [Birst Agilis üzleti elemzés támogatási csoportjának](mailto:info@birst.com). 

    > [!NOTE]
    > Birst csapatának megemlíteni, hogy ez az integráció SHA256 algoritmust kell-e (SHA1 nem támogatja), például, hogy az egyszeri bejelentkezés állíthatják a megfelelő kiszolgálón **app2101** stb.
  

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/birst-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-birst-agile-business-analytics-test-user"></a>Birst Agilis üzleti elemzés tesztfelhasználó létrehozása

Ez a szakasz célja Birst Agilis üzleti elemzés Britta Simon nevű felhasználó létrehozásához. Együttműködve [Birst Agilis üzleti elemzés támogatási csoportjának](mailto:info@birst.com) a felhasználók hozzáadása a Birst fiókban. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Birst Agilis üzleti elemzés Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Birst Agilis üzleti elemzés céljából, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Birst Agilis üzleti elemzés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/birst-tutorial/tutorial_birst_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a Birst Agilis üzleti Analytics csempére kattint, meg kell lekérése automatikusan bejelentkezett az Birst Agilis üzleti elemzési alkalmazás. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/birst-tutorial/tutorial_general_01.png
[2]: ./media/birst-tutorial/tutorial_general_02.png
[3]: ./media/birst-tutorial/tutorial_general_03.png
[4]: ./media/birst-tutorial/tutorial_general_04.png

[100]: ./media/birst-tutorial/tutorial_general_100.png

[200]: ./media/birst-tutorial/tutorial_general_200.png
[201]: ./media/birst-tutorial/tutorial_general_201.png
[202]: ./media/birst-tutorial/tutorial_general_202.png
[203]: ./media/birst-tutorial/tutorial_general_203.png

