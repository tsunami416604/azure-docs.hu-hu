---
title: "Oktatóanyag: Azure Active Directoryval integrált Asana |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Asana között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: a2f0cecb93cc382bcfd710c44eb70f80ae67f9b6
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Oktatóanyag: Azure Active Directoryval integrált Asana

Ebben az oktatóanyagban elsajátíthatja Asana integrálása az Azure Active Directory (Azure AD).

Asana integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Asana hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Asana (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Asana, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Asana egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Asana hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-asana-from-the-gallery"></a>A gyűjteményből Asana hozzáadása
Az Azure AD integrálása a Asana konfigurálásához kell hozzáadnia Asana a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Asana hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Asana**, jelölje be **Asana** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Asana

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Asana a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Asana közötti kapcsolat kapcsolatot kell létrehozni.

Asana, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Asana tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy Asana tesztfelhasználó](#create-an-asana-test-user)**  - való Britta Simon valami Asana, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Asana alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Asana, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Asana** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. Az a **Asana tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Asana tartomány és az URL-címek](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőhöz típus URL-címe:`https://app.asana.com/`

    b. Az a **azonosító** szövegmezőhöz objektumtípus-érték:`https://app.asana.com/`
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. A a **Asana konfigurációs** kattintson **konfigurálása Asana** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Asana konfiguráció](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. Egy másik böngészőablakban bejelentkezés az Asana alkalmazására. Asana SSO konfigurálásához a munkaterület beállításához kattintson a képernyő jobb felső sarkában a munkaterület neve való hozzáféréshez. Kattintson a  **\<a munkaterület neve\> beállítások**. 
   
    ![Asana egyszeri bejelentkezési beállítások](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. Az a **szervezeti beállítások** ablak, kattintson a **felügyeleti**. Kattintson a **tagok SAML keresztül kell bejelentkezniük** engedélyezése az egyszeri bejelentkezés konfigurálása. Hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés szervezeti beállítások konfigurálása](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. Az a **bejelentkezési URL-címe** szövegmező, illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe**.

     b. Az Azure portálról letöltött tanúsítvány kattintson a jobb gombbal, majd nyissa meg a tanúsítványfájlt, a Jegyzettömb vagy az előnyben részesített szövegszerkesztőben. Másolja a tartalmat a kezdő és záró tanúsítvány címe között, és illessze be a **X.509 tanúsítvány** szövegmező.

9. Kattintson a **Save** (Mentés) gombra. Ugrás a [Asana útmutató egyszeri bejelentkezés beállításával kapcsolatos](https://asana.com/guide/help/premium/authentication#gl-saml) Ha segítségre van szüksége további.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-asana-test-user"></a>Hozzon létre egy Asana tesztfelhasználó számára

Ebben a szakaszban egy Asana Britta Simon nevű felhasználót hoz létre.

1. A **Asana**, navigáljon a **csapatok** szakaszt, a bal oldali panelen. Kattintson a plusz jelre gombra. 
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. Írja be az e-mailt britta.simon@contoso.com a szövegmezőbe, és válassza ki a **meghívása**.

3. Kattintson a **küldése a meghívás**. Az új felhasználó kap egy e-mail az az e-mail fiókjába. Egyenként kell létrehozni, és ellenőrizze a fiók.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Asana Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése Asana, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Asana**.

    ![Az alkalmazások listáját a Asana hivatkozás](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja az Azure AD az egyszeri bejelentkezés tesztelése.

Ugrás a Asana bejelentkezési lapot. Az E-mail cím szövegmezőjének helyezze be az e-mail cím britta.simon@contoso.com. A jelszó szövegmezőjét hagyja üresen, és kattintson a **bejelentkezés**. Az Azure AD bejelentkezési oldalára irányítja. Végezze el az Azure AD hitelesítő adatait. Most jelentkezett Asana.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
