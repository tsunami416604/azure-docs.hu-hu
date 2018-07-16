---
title: 'Oktatóanyag: Azure Active Directory-integráció az Ziflow |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Ziflow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: fdde8cbec7fc249eecfcc0c1682bb5eed94c1585
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050489"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Oktatóanyag: Azure Active Directory-integráció az Ziflow

Ebben az oktatóanyagban elsajátíthatja, hogyan Ziflow integrálása az Azure Active Directory (Azure AD).

Ziflow integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Ziflow Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Ziflow (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Ziflow az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Ziflow egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Ziflow hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ziflow-from-the-gallery"></a>Ziflow hozzáadása a katalógusból
Az Azure AD integrálása a Ziflow konfigurálásához hozzá kell Ziflow a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ziflow hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Ziflow**válassza **Ziflow** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Ziflow](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Ziflow a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Ziflow mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Ziflow hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Ziflow tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Ziflow tesztfelhasználót](#create-a-ziflow-test-user)**  – egy megfelelője a Britta Simon Ziflow, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Ziflow alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Ziflow, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Ziflow** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Az a **Ziflow tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Ziflow tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Az előző értékek nem valódi. Az egyedi azonosító értéket az azonosító és a bejelentkezési URL-cím a tényleges érték, amelynek az ismertetése az oktatóanyag későbbi részében frissíti. Kapcsolattartó [Ziflow támogatási csapatának](mailto:support@ziflow.com) a bejelentkezési URL-cím altartomány értékéhez.
    
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/ziflow-tutorial/tutorial_general_400.png)

6. Az a **Ziflow konfigurációs** területén kattintson **konfigurálása Ziflow** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Ziflow konfiguráció](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. Egy másik böngészőablakban, jelentkezzen be egy biztonsági-rendszergazdaként Ziflow.


8. Kattintson az Avatar a jobb felső sarokban, és kattintson **fiók kezelése**.

    ![Ziflow konfiguráció kezelése](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. A bal felső sarokban, kattintson **egyszeri bejelentkezés**.

    ![Ziflow konfigurációs bejelentkezési](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Az a **egyszeri bejelentkezés** lapon, a következő lépésekkel:

    ![Ziflow konfigurációs egyetlen](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Válassza ki **típus** , **SAML2.0**.

    b.In a **bejelentkezési az URL-CÍMÉT** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    c. Töltse fel a base-64 kódolású tanúsítványt be az Azure Portalról letöltött a **aláíró tanúsítvány X509**.

    d. A a **bejelentkezési kijelentkezési URL-cím** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    e. Az a **konfigurációs beállításait az azonosító szolgáltató** szakaszt, másolja a kijelölt egyedi azonosító értéket, és fűzze hozzá a azonosítója és a bejelentkezési URL-címet a **Ziflow tartomány és URL-címek szakaszt** a Az Azure Portalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/ziflow-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/ziflow-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/ziflow-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/ziflow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-ziflow-test-user"></a>Ziflow tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Ziflow jelentkezzen be, akkor ki kell építeni Ziflow be. Ziflow a kiépítés manuális feladat.

Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be egy biztonsági-rendszergazdaként Ziflow.

2. Navigáljon a **személyek** felső.

    ![Ziflow konfigurációs személyek](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kattintson a **Hozzáadás** majd **felhasználó hozzáadása**.

    ![Ziflow konfigurációs felvenni a felhasználót](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Az a **felhasználó hozzáadása** előugró ablakban hajtsa végre az alábbi lépéseket:

    ![Ziflow konfigurációs felvenni a felhasználót](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó brittasimon@contoso.com.

    b. A **Utónév** szöveg Britta például a felhasználó utónevét adja meg.

    c. A **Vezetéknév** szöveget adja meg a Simon például a felhasználó vezetékneve.

    d. Válassza ki a Ziflow szerepét.

    e. Kattintson a **adjon hozzá 1 felhasználó**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Ziflow Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Ziflow, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Ziflow**.

    ![Az alkalmazások listáját a Ziflow hivatkozásra](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Ziflow csempére kattint, meg kell lekérése automatikusan bejelentkezett az Ziflow alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

