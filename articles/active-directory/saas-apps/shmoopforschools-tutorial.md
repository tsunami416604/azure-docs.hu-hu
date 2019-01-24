---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Shmoop az iskolák |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Shmoop az iskolák és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 5c1dbf6429387c02f226e4005164e8824abdc102
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818948"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Oktatóanyag: Az Azure Active Directory integrációja az Shmoop az iskolák

Ebben az oktatóanyagban elsajátíthatja, hogyan Shmoop az iskolák integrálása az Azure Active Directory (Azure AD).

Shmoop az iskolák integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Az Azure ad-ben Shmoop az iskolák hozzáféréssel rendelkező szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Shmoop az iskolák az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Shmoop az iskolák konfigurálni az Azure AD-integráció, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Shmoop az iskolák egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez a következőket javasoljuk:

- Csak akkor, ha szükséges, használja az éles evironment.
- Bevezetés a [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/) Ha még nem rendelkezik egy Azure ad-ben a próbakörnyezet.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. Az iskolák Shmoop hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Az iskolák Shmoop hozzáadása a katalógusból
Az Azure AD-be az iskola Shmoop integráció konfigurálásához, hozzá kell Shmoop az iskolák a galériából a felügyelt SaaS-alkalmazások listájára.

**Az iskolák Shmoop hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Shmoop az iskolák**. Válassza ki **Shmoop az iskolák** az eredmények közül válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Shmoop az iskolák](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni, és a teszt "Britta Simon." nevű felhasználó iskolák részére Shmoop az Azure AD egyszeri bejelentkezés tesztelése

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, akik a partner felhasználó Shmoop a iskolákban, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó Shmoop az iskolák közötti kapcsolat.

Az Azure AD egyszeri bejelentkezés az Shmoop az iskolák tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Az iskolák Shmoop tesztfelhasználó létrehozása](#create-a-shmoop-for-schools-test-user) egy megfelelője a Britta Simon Shmoop az iskolák részére, amely a felhasználó Azure ad-ben reprezentációja van csatolva van.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Shmoop az iskolák alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Shmoop az iskolák, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Shmoop az iskolák** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanel, a legördülő menü alatt **egyszeri bejelentkezési mód**válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Az a **Shmoop az iskolák tartomány és URL-címek** területén az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Az a **bejelentkezési URL-** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Az a **azonosító** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [Shmoop az iskolák ügyfél-támogatási csapatának](mailto:support@shmoop.com) beolvasni ezeket az értékeket. 
 
4. A Shmoop az iskolák alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás integrációs oldalán található. Az alábbi képernyőfelvételen a helyességi feltételek konfigurálása:

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Iskolai Shmoop két szerepkör támogatja a felhasználók számára: **Oktatói** és **tanulói**. Állítsa be ezeket a szerepköröket az Azure ad-ben, hogy a felhasználók a megfelelő szerepkörök rendelhetők. Az Azure AD-szerepkörök konfigurálása ismertetése: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).
    
5. Az a **felhasználói attribútumok** című rész a **egyszeri bejelentkezési** párbeszédpanel az SAML-jogkivonat attribútum adja meg az előző képen látható módon.  Ezután az alábbi lépéseket:

    | Attribútum neve | Hodnota atributu |
    | -------------- | --------------- |
    | szerepkör           | user.assignedroles |

    a. Megnyitásához a **attribútum hozzáadása** párbeszédpanelen jelölje ki **attribútum hozzáadása**.
    
    ![Egyszeri bejelentkezés konfigurálása ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be azon attribútum nevét, amely a sorhoz látható.
    
    c. Az a **érték** listájához, válassza ki az attribútum értéke, amely a sorhoz látható.

    d. Hagyja a **Namespace** mező üres.
    
    e. Kattintson az **OK** gombra.

6. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozás](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Az egyszeri bejelentkezés konfigurálásához a **Shmoop az iskolák** oldalon kell küldenie a **alkalmazás összevonási metaadatainak URL-címe** , a [Shmoop az iskolák támogatási csoportjának](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja hozzon létre egy tesztfelhasználót Britta Simon nevű az Azure Portalon.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Az iskolák Shmoop tesztfelhasználó létrehozása

Ez a szakasz célja egy Shmoop az iskolák Britta Simon nevű felhasználó létrehozásához. Shmoop az iskolák támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha egy új felhasználó még nem létezik, a rendszer létrehozza Shmoop az iskolák elérésére tett kísérlet során.

>[!NOTE]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, lépjen kapcsolatba a [Shmoop az iskolák támogatási csoportjának](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Shmoop az iskolák Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Shmoop az iskolák, tegye a következőket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Ezután lépjen a **vállalati alkalmazások** a könyvtár nézetben.  Majd **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Shmoop az iskolák**.

    ![Az alkalmazások listáját a Shmoop az iskolák hivatkozás](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra. 

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Amikor kiválaszt a **Shmoop az iskolák** csempére a hozzáférési panelen kell lekérése automatikusan bejelentkezett Shmoop az iskolák alkalmazását.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások integrálása az Azure Active Directory oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

