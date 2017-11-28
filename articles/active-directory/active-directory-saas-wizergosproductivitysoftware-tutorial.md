---
title: "Oktatóanyag: Azure Active Directory-integráció Wizergos termelékenység szoftverrel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a termelékenység szoftver Wizergos között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: jeedes
ms.openlocfilehash: 6bcdf8d1a7b275b6075d9fd50924de98b65d9c32
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Oktatóanyag: Azure Active Directory-integráció Wizergos termelékenység szoftverrel

Ebben az oktatóanyagban elsajátíthatja Wizergos termelékenység szoftver integrálása az Azure Active Directory (Azure AD).

Wizergos termelékenység szoftver integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Wizergos termelékenység szoftver szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Wizergos termelékenység szoftverre (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Wizergos termelékenység szoftverrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Wizergos termelékenység szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Wizergos termelékenység szoftver hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>A gyűjteményből Wizergos termelékenység szoftver hozzáadása
Az Azure AD integrálása a Wizergos termelékenység szoftver konfigurálásához kell hozzáadnia Wizergos termelékenység szoftver a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Wizergos termelékenység szoftver hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Wizergos termelékenység szoftver**, jelölje be **Wizergos termelékenység szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Wizergos termelékenység szoftver](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Wizergos termelékenység szoftverrel.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Wizergos termelékenység szoftver a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Wizergos termelékenység szoftver közötti kapcsolat kapcsolatot kell létrehozni.

Wizergos termelékenység szoftverfrissítési, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Wizergos termelékenység szoftverrel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Wizergos termelékenység szoftver tesztfelhasználó létrehozása](#create-a-wizergos-productivity-software-test-user)**  - való egy megfelelője a Britta Simon Wizergos termelékenység szoftver, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Wizergos termelékenység alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Wizergos termelékenység szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Wizergos termelékenység szoftver** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_samlbase.png)

3. Az a **Wizergos termelékenység szoftver tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Wizergos termelékenység szoftver tartomány és az URL-címek](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_url.png)

    Az a **azonosító** szövegmező, írja be az URL-cím:`http://www.wizergos.net`

4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_400.png)

6. A a **Wizergos termelékenység szoftverkonfigurációt** kattintson **Wizergos termelékenység szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Wizergos termelékenység szoftverkonfigurációjának összeállítása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_configure.png) 

7. Egy másik webes böngészőablakban bejelentkezés a Wizergos termelékenység szoftver bérlő rendszergazdaként.

8. A Hamburg menüben válassza ki a **Admin**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)

9. A rendszergazda lap bal oldali menüben válassza ki **hitelesítési** , majd kattintson a **az Azure AD**.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)

10. Hajtsa végre a következő lépéseket **hitelesítési** szakasz.

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
    
    a. Kattintson a **FELTÖLTÉSE** gomb az Azure AD a letöltött tanúsítvány feltöltése.
    
    b. Az a **kiállítójának URL-címe** szövegmező, illessze be a **SAML Entitásazonosító** érték, amely az Azure-portálon másolta.
    
    c. Az a **egyszeri bejelentkezési URL-cím** szövegmező, illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure-portálon másolta.
    
    d. Az a **egyetlen Sign-Out URL-címet** szövegmező, illessze be a **Sign-Out URL-cím** érték, amely az Azure-portálon másolta.
    
    e. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-wizergos-productivity-software-test-user"></a>Wizergos termelékenység szoftver tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon meghívta Wizergos termelékenység szoftver hoz létre. Adjon együttműködve [Wizergos termelékenység szoftver támogatási csoport](mailTo:support@wizergos.com) a felhasználók hozzáadása a Wizergos termelékenység szoftver platform.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Wizergos termelékenység szoftver használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Wizergos termelékenység szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Wizergos termelékenység szoftver**.

    ![Az alkalmazások listáját a Wizergos termelékenység szoftver hivatkozás](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Wizergos termelékenység szoftver csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Wizergos termelékenység szoftverfrissítések alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png

