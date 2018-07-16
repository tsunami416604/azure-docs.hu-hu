---
title: 'Oktatóanyag: Azure Active Directory-integráció az Vászonalapú Lms |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a vászon LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 9811be78bd6260b17c462c077ed299dff8309b83
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048507"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Oktatóanyag: Azure Active Directory-integráció az vászon segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan Vászonalapú integrálása az Azure Active Directory (Azure AD).

Vászonalapú integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a vászonhoz az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a vászonra (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a vásznon, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy vászon egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Vászon hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-canvas-from-the-gallery"></a>Vászon hozzáadása a katalógusból
Adja meg az Azure AD integrálása a vásznon, szüksége vászon hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Vászon hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Vászonalapú**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. Az eredmények panelen válassza ki a **Vászonalapú**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a "Britta Simon." nevű felhasználó vizsgálati vászon az az Azure AD egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó vásznon mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó vásznon hivatkozás kapcsolata kell létrehozni.

Vászon, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a vászon tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy vászon tesztfelhasználó létrehozása](#creating-a-canvas-test-user)**  – szeretné, hogy egy Britta Simon megfelelője a vásznon, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Vászonalapú alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a vásznon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Vászonalapú** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. Az a **Vászonalapú tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenant-name>.instructure.com`

    b. Az a **azonosító** szövegmezőbe írja be az értéket a következő minta használatával: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Vászonalapú ügyfél-támogatási csapatának](https://community.canvaslms.com/community/help) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_general_400.png)

6. Az a **Vászonalapú konfigurációs** területén kattintson **konfigurálásához vászon** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **jelszó URL-Címének módosítása, kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. Egy másik böngészőablakban jelentkezzen be a vászon vállalati hely rendszergazdaként.

8. Lépjen a **tanfolyamok \> felügyelt fiókok \> Microsoft**.
   
    ![Vászonalapú](./media/canvas-lms-tutorial/IC775990.png "vászon")

9. Válassza a bal oldali navigációs panelen, **hitelesítési**, és kattintson a **új SAML-konfiguráció hozzáadása**.
   
    ![Hitelesítési](./media/canvas-lms-tutorial/IC775991.png "hitelesítés")

10. Az aktuális Integration oldalon hajtsa végre az alábbi lépéseket:
   
    ![Aktuális integrációs](./media/canvas-lms-tutorial/IC775992.png "jelenlegi integrációja")

    a. A **identitásszolgáltató Entitásazonosító** szövegmező, illessze be az értéket a **SAML Entitásazonosító** Azure Portalról másolt.

    b. A **URL-napló** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-** Azure Portalról másolt.

    c. A **Log kijelentkezési URL-cím** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

    d. A **módosítása Hesla** szövegmezőjébe illessze be az értéket, **jelszó URL-Címének módosítása** Azure Portalról másolt. 

    e. A **tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be a **ujjlenyomat** tanúsítvány, amely az Azure Portalról másolt érték.      
        
    f. Az a **bejelentkezési attribútum** listáról válassza ki **NameID**.

    g. Az a **azonosító formátuma** listáról válassza ki **emailAddress**.

    h. Kattintson a **hitelesítési beállítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/canvas-lms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-canvas-test-user"></a>Egy vászon tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a vászonra, akkor ki kell építeni vászonra.

Esetén a vásznon felhasználóátadást készíthet elő a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Vászonalapú** bérlő.

2. Lépjen a **tanfolyamok \> felügyelt fiókok \> Microsoft**.
   
   ![Vászonalapú](./media/canvas-lms-tutorial/IC775990.png "vászon")

3. Kattintson a **felhasználók**.
   
   ![Felhasználók](./media/canvas-lms-tutorial/IC775995.png "felhasználók")

4. Kattintson a **új felhasználó hozzáadása**.
   
   ![Felhasználók](./media/canvas-lms-tutorial/IC775996.png "felhasználók")

5. Új felhasználó párbeszédpanel oldal hozzáadása hajtsa végre az alábbi lépéseket:
   
   ![Felhasználó hozzáadása](./media/canvas-lms-tutorial/IC775997.png "felhasználó hozzáadása")
   
   a. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a felhasználó nevét **BrittaSimon**.

   b. Az a **E-mail** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

   c. Az a **bejelentkezési** szövegmezőbe írja be a felhasználó Azure ad-ben e-mail címe például **brittasimon@contoso.com**.

   d. Válassza ki **információ a fiók létrehozása a felhasználó E-mail**.

   e. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Bármely más Vászonalapú felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Vászonalapú által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon vászonra a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a vásznon, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Vászonalapú**.

    ![Egyszeri bejelentkezés konfigurálása](./media/canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a vásznon csempére kattint, akkor kell lekérése automatikusan bejelentkezett a vásznon alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/canvas-lms-tutorial/tutorial_general_203.png

