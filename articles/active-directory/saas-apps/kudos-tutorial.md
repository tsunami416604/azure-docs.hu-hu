---
title: 'Oktatóanyag: Azure Active Directory-integráció az Kudos |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Kudos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 5807ab0090f401c02599db40f110b7a9b1504060
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047052"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Oktatóanyag: Azure Active Directory-integráció az Kudos

Ebben az oktatóanyagban elsajátíthatja, hogyan Kudos integrálása az Azure Active Directory (Azure AD).

Kudos integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Kudos Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Kudos (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Kudos az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Kudos egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Kudos hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-kudos-from-the-gallery"></a>Kudos hozzáadása a katalógusból
Az Azure AD integrálása a Kudos konfigurálásához hozzá kell Kudos a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Kudos hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Kudos**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/tutorial_kudos_search.png)

5. Az eredmények panelen válassza ki a **Kudos**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Kudos a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Kudos mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Kudos hivatkozás kapcsolata kell létrehozni.

Kudos, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Kudos tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Kudos tesztfelhasználó létrehozása](#creating-a-kudos-test-user)**  – egy megfelelője a Britta Simon Kudos, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Kudos alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Kudos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Kudos** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

3. Az a **Kudos tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_kudos_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Kudos ügyfél-támogatási csapatának](http://success.kudosnow.com/home) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_general_400.png)

6. Az a **Kudos konfigurációs** területén kattintson **konfigurálása Kudos** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_kudos_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a Kudos vállalati hely rendszergazdaként.

8. A felső menüben kattintson **beállítások**.
   
    ![Beállítások](./media/kudos-tutorial/ic787806.png "beállításai")

9. Kattintson a **Integrációk \> SSO**.

10. Az a **SSO** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![EGYSZERI BEJELENTKEZÉS](./media/kudos-tutorial/ic787807.png "EGYSZERI BEJELENTKEZÉS")
   
    a. A **bejelentkezési URL-cím** szövegmezőjébe illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt. 

    b. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmező
   
    c. A **kijelentkezési URL-CÍMRE történő**, illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.
   
    d. Az a **a Kudos URL-cím** szövegmezőbe írja be a cég nevét.
   
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/kudos-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-kudos-test-user"></a>Kudos tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Kudos szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Kudos be. 

Kudos, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Kudos** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **beállítások**.
   
   ![Beállítások](./media/kudos-tutorial/ic787806.png "beállításai")

3. Kattintson a **felhasználói rendszergazda**.

4. Kattintson a **felhasználók** fülre, majd **felhasználó hozzáadása**.
   
   ![Felhasználói rendszergazda](./media/kudos-tutorial/ic787809.png "felhasználói rendszergazda")

5. Az a **felhasználó hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Felhasználó hozzáadása](./media/kudos-tutorial/ic787810.png "felhasználó hozzáadása")
   
    a. Írja be a **Utónév**, **Vezetéknév**, **E-mail** és egyéb részletek egy érvényes Azure Active Directory-fióknevet, amelyet a kapcsolódó szövegmezőkben létrehozásához.
   
    b. Kattintson a **felhasználó létrehozása**.

>[!NOTE]
>Bármely más Kudos felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Kudos által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Kudos Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Kudos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Kudos**.

    ![Egyszeri bejelentkezés konfigurálása](./media/kudos-tutorial/tutorial_kudos_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Kudos csempére kattint, meg kell lekérése automatikusan bejelentkezett az Kudos alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

