---
title: 'Oktatóanyag: Azure Active Directoryval integrált Yardi tanulási |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Yardi tanulási között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7ea58b54-ec5b-4576-8586-814b11d0f4fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 3d3c44f7b2035a3e0d0fb97bae69c67305b99978
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923804"
---
# <a name="tutorial-azure-active-directory-integration-with-yardi-elearning"></a>Oktatóanyag: Azure Active Directoryval integrált Yardi tanulási

Ebben az oktatóanyagban elsajátíthatja Yardi tanulási integrálása az Azure Active Directory (Azure AD).

Yardi integrálása az Azure ad-val tanulási tesz lehetővé a következő előnyöket biztosítja:

- Megadhatja a Yardi tanulási hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Yardi tanulási (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs Yardi tanulási konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy Yardi tanulási egyszeri bejelentkezés engedélyezett előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Yardi tanulási hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-yardi-elearning-from-the-gallery"></a>A gyűjteményből Yardi tanulási hozzáadása
Az Azure AD integrálása a Yardi tanulási konfigurálásához kell hozzáadnia Yardi tanulási a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Yardi tanulási hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Yardi tanulási**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/tutorial_yardielearning_search.png)

5. Az eredmények panelen válassza ki a **Yardi tanulási**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/tutorial_yardielearning_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Yardi tanulási

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Yardi tanulási a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Yardi tanulási közötti kapcsolat kapcsolatot kell létrehozni.

Yardi tanulási, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Yardi tanulási tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Yardi tanulási tesztfelhasználó létrehozása](#creating-a-yardi-elearning-test-user)**  - Britta Simon egy partner, a felhasználó az Azure AD ábrázolását kapcsolódó Yardi tanulási rendelkezik.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Yardi tanulási alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Yardi tanulási, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Yardi tanulási** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/yardielearning-tutorial/tutorial_yardielearning_samlbase.png)

3. Az a **Yardi tanulási tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/yardielearning-tutorial/tutorial_yardielearning_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.yardielearning.com/login`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.yardielearning.com/trust`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Yardi tanulási ügyfél támogatási csoport](mailto:elearning@yardi.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/yardielearning-tutorial/tutorial_yardielearning_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/yardielearning-tutorial/tutorial_general_400.png)

8. Egyszeri bejelentkezés konfigurálása **Yardi tanulási** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [Yardi tanulási támogatási csoport](mailto:elearning@yardi.com). 

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/yardielearning-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-yardi-elearning-test-user"></a>Yardi tanulási tesztfelhasználó létrehozása

Ez a szakasz célja Yardi tanulási Britta Simon nevű felhasználót létrehozni. Yardi tanulási támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az Yardi tanulási elérésére, ha még nem létezik tett kísérlet során. 

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha szeretné-e lépjen kapcsolatba a [Yardi tanulási támogatási csoport](mailto:elearning@yardi.com).

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó Yardi tanulási való hozzáférés biztosítása.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Yardi tanulási, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Yardi tanulási**.

    ![Egyszeri bejelentkezés konfigurálása](./media/yardielearning-tutorial/tutorial_yardielearning_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Yardi tanulási csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Yardi tanulási alkalmazáshoz. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yardielearning-tutorial/tutorial_general_01.png
[2]: ./media/yardielearning-tutorial/tutorial_general_02.png
[3]: ./media/yardielearning-tutorial/tutorial_general_03.png
[4]: ./media/yardielearning-tutorial/tutorial_general_04.png

[100]: ./media/yardielearning-tutorial/tutorial_general_100.png

[200]: ./media/yardielearning-tutorial/tutorial_general_200.png
[201]: ./media/yardielearning-tutorial/tutorial_general_201.png
[202]: ./media/yardielearning-tutorial/tutorial_general_202.png
[203]: ./media/yardielearning-tutorial/tutorial_general_203.png

