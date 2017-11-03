---
title: "Oktatóanyag: Azure Active Directoryval integrált KnowBe4 biztonsági tájékoztatási képzési |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és KnowBe4 biztonsági tájékoztatási képzési között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 2a81495c54cfe293700aa0ef0d82f13933dcc4aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Oktatóanyag: Azure Active Directoryval integrált KnowBe4 biztonsági tájékoztatási képzési

Ebben az oktatóanyagban elsajátíthatja KnowBe4 biztonsági tájékoztatási képzési integrálása az Azure Active Directory (Azure AD).

KnowBe4 biztonsági tájékoztatási képzési integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér KnowBe4 biztonsági tájékoztatási képzési szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett KnowBe4 biztonsági tájékoztatási képzést (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához KnowBe4 biztonsági tájékoztatási képzéssel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy KnowBe4 biztonsági tájékoztatási képzési egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből KnowBe4 biztonsági tájékoztatási képzési hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>A gyűjteményből KnowBe4 biztonsági tájékoztatási képzési hozzáadása
Az Azure AD integrálása a KnowBe4 biztonsági tájékoztatási képzési konfigurálásához kell hozzáadnia KnowBe4 biztonsági tájékoztatási képzési a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből KnowBe4 biztonsági tájékoztatási képzési hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **KnowBe4 biztonsági tájékoztatási képzési**, jelölje be **KnowBe4 biztonsági tájékoztatási képzési** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel a az alkalmazás.

    ![Az eredmények listájában KnowBe4 biztonsági tájékoztatási képzés](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés KnowBe4 biztonsági tájékoztatási képzési "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó KnowBe4 biztonsági tájékoztatási képzés a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a KnowBe4 biztonsági tájékoztatási képzési közötti kapcsolat kapcsolatot kell létrehozni.

KnowBe4 biztonsági tájékoztatási képzési, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés KnowBe4 biztonsági tájékoztatási képzéssel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[KnowBe4 biztonsági tájékoztatási képzési tesztfelhasználó létrehozása](#create-a-knowbe4-security-awareness-training-test-user)**  - való egy megfelelője a Britta Simon KnowBe4 biztonsági tájékoztatási képzési, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az KnowBe4 biztonsági tájékoztatási képzési alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés KnowBe4 biztonsági tájékoztatási képzéssel megadásához a következő lépésekkel:**

1. Az Azure portálon a a **KnowBe4 biztonsági tájékoztatási képzési** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

3. Az a **KnowBe4 biztonsági tájékoztatási képzési tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk KnowBe4 biztonsági tájékoztatási képzési tartomány és az URL-címek](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE] 
    > Bejelentkezési URL-cím értéke nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [KnowBe4 biztonsági tájékoztatási képzési ügyfél-támogatási csoport](mailto:support@KnowBe4.com) lekérni ezt az értéket. 

    b. Az a **azonosító** szövegmező, írja be a karakterlánc-érték:`KnowBe4`

    > [!NOTE]
    >Ez az kis-és nagybetűket

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-knowbe4-tutorial/tutorial_general_400.png)

6. A a **KnowBe4 biztonsági tájékoztatási képzési konfigurációs** kattintson **konfigurálása KnowBe4 biztonsági tájékoztatási képzési** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![KnowBe4 biztonsági tájékoztatási képzési konfiguráció](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **KnowBe4 biztonsági tájékoztatási képzési** oldalon kell küldeniük a letöltött **tanúsítvány (Raw)**, **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** való [KnowBe4 biztonsági tájékoztatási képzési ügyfél-támogatási csoport](mailto:support@KnowBe4.com).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-knowbe4-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>KnowBe4 biztonsági tájékoztatási képzési tesztfelhasználó létrehozása

Ez a szakasz célja KnowBe4 biztonsági tájékoztatási képzési Britta Simon nevű felhasználót létrehozni. KnowBe4 biztonsági tájékoztatási képzési támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az KnowBe4 biztonsági tájékoztatási képzési elérésére, ha még nem létezik tett kísérlet során. 

>[!NOTE]
>Hozza létre a felhasználó manuálisan kell, ha szeretné-e lépjen kapcsolatba a [KnowBe4 biztonsági tájékoztatási képzési támogatási csoport](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés KnowBe4 biztonsági tájékoztatási képzési Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése KnowBe4 biztonsági tájékoztatási képzési, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **KnowBe4 biztonsági tájékoztatási képzési**.

    ![Az alkalmazások listáját a KnowBe4 biztonsági tájékoztatási képzési hivatkozás](./media/active-directory-saas-knowbe4-tutorial/tutorial_knowbe4_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.
  
Ha a hozzáférési panelen KnowBe4 biztonsági tájékoztatási képzési csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az KnowBe4 biztonsági tájékoztatási képzési alkalmazáshoz. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowbe4-tutorial/tutorial_general_203.png

