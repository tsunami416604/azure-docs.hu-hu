---
title: "Oktatóanyag: Azure Active Directoryval integrált Lecorpio |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Lecorpio között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.openlocfilehash: 15eded8b676dfea018f041aed75b4f706bc1a55b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lecorpio"></a>Oktatóanyag: Azure Active Directoryval integrált Lecorpio

Ebben az oktatóanyagban elsajátíthatja Lecorpio integrálása az Azure Active Directory (Azure AD).

Lecorpio integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Lecorpio hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Lecorpio (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Lecorpio, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Lecorpio egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Lecorpio hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-lecorpio-from-the-gallery"></a>A gyűjteményből Lecorpio hozzáadása
Az Azure AD integrálása a Lecorpio konfigurálásához kell hozzáadnia Lecorpio a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Lecorpio hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Lecorpio**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_search.png)

5. Az eredmények panelen válassza ki a **Lecorpio**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Lecorpio

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Lecorpio a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Lecorpio közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Lecorpio a.

Az Azure AD egyszeri bejelentkezést a Lecorpio tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Lecorpio tesztfelhasználó létrehozása](#creating-a-lecorpio-test-user)**  - való Britta Simon valami Lecorpio, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Lecorpio alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Lecorpio, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Lecorpio** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_samlbase.png)

3. Az a **Lecorpio tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával:`https://<instance name>.lecorpio.com/<customer name>`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<instance name>.lecorpio.com/<customer name>`

    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Itt javasoljuk, hogy az azonosító a karakterlánc egyedi értéket használja. Ügyfél [Lecorpio ügyfél-támogatási csoport](mailto:info@lecorpio.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-lecorpio-tutorial/tutorial_general_400.png)

6. Egyszeri bejelentkezés konfigurálása **Lecorpio** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [Lecorpio támogatási csoport](mailto:info@lecorpio.com).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-lecorpio-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lecorpio-test-user"></a>Lecorpio tesztfelhasználó létrehozása

Ebben a szakaszban egy Lecorpio Britta Simon nevű felhasználót hoz létre. 

Ügyfél [Lecorpio ügyfél-támogatási csoport](mailto:info@lecorpio.com) a felhasználók hozzáadása az Lecorpio alkalmazásban.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Lecorpio Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Lecorpio, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Lecorpio**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-lecorpio-tutorial/tutorial_lecorpio_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Lecorpio csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Lecorpio alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lecorpio-tutorial/tutorial_general_203.png

