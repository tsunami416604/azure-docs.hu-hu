---
title: 'Oktatóanyag: Azure Active Directory-integráció laposabb fájlokkal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és laposabb fájlok között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 4ae5c9834352bd2413075e87c79e27a9a3c354b9
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047643"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Oktatóanyag: Azure Active Directory-integráció az laposabb fájlok

Ebben az oktatóanyagban elsajátíthatja, hogyan laposabb fájlok integrálása az Azure Active Directory (Azure AD).

Laposabb fájlok integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy az Azure AD-hozzáféréssel rendelkező laposabb fájlok
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett laposabb fájlok (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása laposabb fájlokkal, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- Egy laposabb fájlok egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Laposabb fájlok hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-flatter-files-from-the-gallery"></a>Laposabb fájlok hozzáadása a katalógusból
Konfigurálja az integráció laposabb fájlok az Azure AD-be, szüksége laposabb fájlok hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Laposabb-fájlok hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **laposabb fájlok**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatterfiles_search.png)

5. Az eredmények panelen válassza ki a **laposabb fájlok**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a teszt "Britta Simon" nevű felhasználó laposabb fájlokat.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó laposabb fájlokban mi egy felhasználó számára az Azure ad-ben. Más szóval közötti egy az Azure AD-felhasználót és a kapcsolódó felhasználó laposabb fájlokban hivatkozást kapcsolatot kell létrehozni.

Laposabb fájlok, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Laposabb fájlokat az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Laposabb fájlok tesztfelhasználó létrehozása](#creating-a-flatter-files-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása laposabb fájlokban.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és laposabb fájlok alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés laposabb fájlokat, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **laposabb fájlok** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

3. Az a **laposabb fájlok tartomány és URL-címek** szakaszban, a felhasználónak nem kell végrehajthatja a lépéseket, ahogy az alkalmazás már előre integrálva van az Azure-ral.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_general_400.png)

6. Az a **laposabb fájlok konfigurációs** területén kattintson **laposabb fájlok konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

7. Bejelentkezés rendszergazdaként laposabb fájlok alkalmazását.

8. Kattintson a **IRÁNYÍTÓPULT**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

9. Kattintson a **beállítások**, és hajtsa végre az alábbi lépéseket a a **vállalati** lapon: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Válassza ki **SAML 2.0-s hitelesítéshez használandó**.
    
    b. Kattintson a **SAML konfigurálása**.

8. Az a **SAML-konfigurációja** párbeszédpanelen hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Az a **tartomány** szövegmezőbe írja be a regisztrált tartomány.
   
    >[!NOTE]
    >Ha nem rendelkezik egy regisztrált tartomány még, forduljon a laposabb fájlok támogatja-e a csapat [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. A **identitásszolgáltató szolgáltató URL-címe** szövegmezőbe, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** másolta, amely űrlapon az Azure Portalon.
   
    c.  Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **szolgáltató Identitástanúsítványt** szövegmezőbe.

    d. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/flatter-files-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-flatter-files-test-user"></a>Laposabb fájlok tesztfelhasználó létrehozása

Ez a szakasz célja egy laposabb fájlokban Britta Simon nevű felhasználó létrehozásához.

**Britta Simon laposabb fájlok nevű felhasználó létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **laposabb fájlok** rendszergazdaként a vállalati webhely.

2. A bal oldali navigációs panelen, kattintson a **beállítások**, majd kattintson a **felhasználók** fülre.
   
    ![Fájlok laposabb felhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Kattintson a **felhasználó hozzáadása**. 

4. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Fájlok laposabb felhasználó létrehozása](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Az a **Utónév** szövegmezőbe írja be **Britta**.
   
    b. Az a **Vezetéknév** szövegmezőbe írja be **Simon**. 
   
    c. Az a **E-mail cím** szövegmezőbe írja be a Britta e-mail címet az Azure Portalon.
   
    d. Kattintson a **elküldése**.   


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés laposabb fájlok Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon fájlokhoz hozzárendelni kívánt laposabb, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **laposabb fájlok**.

    ![Egyszeri bejelentkezés konfigurálása](./media/flatter-files-tutorial/tutorial_flatterfiles_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a laposabb fájlok csempére kattint, meg kell lekérése automatikusan bejelentkezett az laposabb fájlok alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/flatter-files-tutorial/tutorial_general_203.png

