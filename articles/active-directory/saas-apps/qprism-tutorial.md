---
title: 'Oktatóanyag: Azure Active Directory-integráció az QPrism |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és QPrism között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 9b37c6d1c1c2e7ec002ac1b4ea5768c8972dd9e8
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039988"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Oktatóanyag: Azure Active Directory-integráció az QPrism

Ebben az oktatóanyagban elsajátíthatja, hogyan QPrism integrálása az Azure Active Directory (Azure AD).

QPrism integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá QPrism Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezve QPrism (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

QPrism az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy QPrism egyszeri bejelentkezés engedélyezve van az előfizetés

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Az éles környezetben ne használja, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. QPrism hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-qprism-from-the-gallery"></a>QPrism hozzáadása a katalógusból
Az Azure AD integrálása a QPrism konfigurálásához hozzá kell QPrism a katalógusból a felügyelt SaaS-alkalmazások listájára.

**QPrism hozzáadása a katalógusból:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. A párbeszédpanel tetején egy új alkalmazás hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **QPrism**, és válassza ki **QPrism** eredmény panelen. Kattintson a **Hozzáadás** , vegye fel az alkalmazást.

    ![Az eredmények listájában QPrism](./media/qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az QPrism, a teszt "Britta Simon." nevű felhasználó

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik a QPrism megfelelőjére felhasználót, hogy egy felhasználó Azure AD-ben. Más szóval lennie kell a társított kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó QPrism között.

Ezt a kapcsolatot létesíteni a QPrism, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév**.

Az Azure AD egyszeri bejelentkezés az QPrism tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Hozzon létre egy QPrism tesztfelhasználót](#create-a-qprism-test-user) van egy megfelelője a Britta Simon QPrism, akik kapcsolódik az Azure AD felhasználói ábrázolása.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az QPrism alkalmazás egyszeri bejelentkezés konfigurálása.

1. Az Azure Portalon az a **QPrism** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanelen jelölje ki **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/qprism-tutorial/tutorial_qprism_samlbase.png)

3. Az a **QPrism tartomány és URL-címek** területén tegye a következőket:

    ![QPrism tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/qprism-tutorial/tutorial_qprism_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet, amely használja a következő mintának: `https://<customer domain>.qmyzone.com/login`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet, amely használja a következő mintának: `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval és bejelentkezés URL-CÍMÉT. Kapcsolattartó [QPrism ügyfél-támogatási csapatának](mailto:qsupport-ce@quatrro.com) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

     ![A tanúsítvány letöltési hivatkozás](./media/qprism-tutorial/tutorial_qprism_certificate.png)

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása Mentés gombra](./media/qprism-tutorial/tutorial_general_400.png)
    
6. Az egyszeri bejelentkezés konfigurálása **QPrism** oldalon kell küldenie a **alkalmazás összevonási metaadatainak URL-címe** való [QPrism támogatási csapatának](mailto:qsupport-ce@quatrro.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure ad-ben:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/qprism-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/qprism-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel tetején a **minden felhasználó** párbeszédpanelen jelölje ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/qprism-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:

    ![A felhasználó párbeszédpanel](./media/qprism-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-qprism-test-user"></a>QPrism tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű QPrism hoz létre. Együttműködik a [QPrism támogatási csapatának](mailto:qsupport-ce@quatrro.com) felhasználót is hozzáadhat a QPrism platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés QPrism Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése QPrism:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és keresse meg a könyvtár nézetben. Lépjen a **vállalati alkalmazások**, és válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **QPrism**.

    ![Az alkalmazások listáját a QPrism hivatkozásra](./media/qprism-tutorial/tutorial_qprism_app.png)  

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza a **Hozzáadás** lehetőséget. Ezután a **hozzárendelés hozzáadása**válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **kiválasztása**.

7. A **hozzárendelés hozzáadása**válassza **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A hozzáférési panelen a QPrism csempe kiválasztásakor meg kell lekérése automatikusan bejelentkezve QPrism alkalmazását.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/qprism-tutorial/tutorial_general_01.png
[2]: ./media/qprism-tutorial/tutorial_general_02.png
[3]: ./media/qprism-tutorial/tutorial_general_03.png
[4]: ./media/qprism-tutorial/tutorial_general_04.png

[100]: ./media/qprism-tutorial/tutorial_general_100.png

[200]: ./media/qprism-tutorial/tutorial_general_200.png
[201]: ./media/qprism-tutorial/tutorial_general_201.png
[202]: ./media/qprism-tutorial/tutorial_general_202.png
[203]: ./media/qprism-tutorial/tutorial_general_203.png

