---
title: 'Oktatóanyag: Azure Active Directory-integráció az Learning munkaállomás LMS |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Learning munkaállomás LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bb056fcf-4135-478e-85b1-5015d1f07b85
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: jeedes
ms.openlocfilehash: d1a15617830f7b517210c7e86146a88d38ae8343
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046821"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-seat-lms"></a>Oktatóanyag: Azure Active Directory-integráció az Learning munkaállomás segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan Learning munkaállomás LMS integrálása az Azure Active Directory (Azure AD).

Learning munkaállomás LMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Learning munkaállomás segítségével az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Learning munkaállomás LMS (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd. [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Learning munkaállomás LMS, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Learning munkaállomás LMS egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Learning munkaállomás LMS hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-learning-seat-lms-from-the-gallery"></a>Learning munkaállomás LMS hozzáadása a katalógusból
Az Azure AD integrálása Learning munkaállomás segítségével a konfigurálása, hozzá kell Learning munkaállomás LMS a galériából a felügyelt SaaS-alkalmazások listájára.

**Learning munkaállomás LMS hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **Learning munkaállomás LMS**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learningseatlms-tutorial/tutorial_learnconnect_search.png)

5. Az eredmények panelen válassza ki a **Learning munkaállomás LMS**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a teszt "Britta Simon." nevű felhasználó Learning munkaállomás segítségével az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Learning munkaállomás LMS mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és tanulás munkaállomás LMS a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

A hivatkozás kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a Learning munkaállomás segítségével.

Az Azure AD egyszeri bejelentkezés a Learning munkaállomás LMS tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Learning munkaállomás LMS tesztfelhasználó létrehozása](#creating-a-learnconnect-test-user)**  – egy megfelelője a Britta Simon Learning munkaállomás segítségével, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Learning munkaállomás LMS alkalmazását az egyszeri bejelentkezés konfigurálása.

**Learning munkaállomás segítségével a konfigurálása az Azure AD egyszeri bejelentkezés, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Learning munkaállomás LMS** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_learnconnect_samlbase.png)

3. Az a **Learning munkaállomás LMS tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_learnconnect_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.learningseatlms.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.learningseatlms.com/Account/AssertionConsumerService`

4. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_learnconnect_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.learningseatlms.com`
     
    > [!NOTE] 
    > Ezek az értékei nem a valós értékeket. Frissítse a tényleges azonosítója, válasz URL-cím és a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Learning munkaállomás-ügyfélszolgálathoz](http://help.learningseatlms.com/help) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_learnconnect_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_general_400.png)

7. Az egyszeri bejelentkezés konfigurálása **Learning munkaállomás LMS** oldalon kell küldenie a letöltött **metaadatainak XML** való [Learning munkaállomás-ügyfélszolgálathoz](http://help.learningseatlms.com/help).

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learningseatlms-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learningseatlms-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learningseatlms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/learningseatlms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-learning-seat-lms-test-user"></a>Learning munkaállomás LMS tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Learning munkaállomás segítségével a felhasználó hoz létre. Ügyfél [Learning munkaállomás-ügyfélszolgálathoz](http://help.learningseatlms.com/help) összes felhasználói információ a felhasználók hozzáadása az Learning munkaállomás LMS-alkalmazásban.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Learning munkaállomás LMS Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Learning munkaállomás LMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Learning munkaállomás LMS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/learningseatlms-tutorial/tutorial_learnconnect_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen. 

A Learning munkaállomás LMS csempére a hozzáférési panelen, akkor lesz lehet automatikusan bejelentkezett a Learning munkaállomás LMS-alkalmazásba. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnconnect-tutorial/tutorial_general_01.png
[2]: ./media/learnconnect-tutorial/tutorial_general_02.png
[3]: ./media/learnconnect-tutorial/tutorial_general_03.png
[4]: ./media/learnconnect-tutorial/tutorial_general_04.png

[100]: ./media/learnconnect-tutorial/tutorial_general_100.png

[200]: ./media/learnconnect-tutorial/tutorial_general_200.png
[201]: ./media/learnconnect-tutorial/tutorial_general_201.png
[202]: ./media/learnconnect-tutorial/tutorial_general_202.png
[203]: ./media/learnconnect-tutorial/tutorial_general_203.png

