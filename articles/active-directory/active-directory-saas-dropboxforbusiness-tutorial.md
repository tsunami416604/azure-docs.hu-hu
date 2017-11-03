---
title: "Oktatóanyag: Azure Active Directoryval integrált vállalati Dropbox |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a vállalati Dropbox között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: a56a5af171eaca259db29f25fee4331a77313420
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Oktatóanyag: Azure Active Directoryval integrált vállalati Dropbox

Ebben az oktatóanyagban elsajátíthatja a vállalati Dropbox integrálása az Azure Active Directory (Azure AD).

A vállalati Dropbox integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Dropbox vállalati hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a dropbox alkalmazásba vállalati (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Dropbox vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Dropbox üzleti egyszeri bejelentkezést az előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A vállalati Dropbox hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-dropbox-for-business-from-the-gallery"></a>A vállalati Dropbox hozzáadása a gyűjteményből
A dropbox-bA az Azure AD-be a vállalati integráció konfigurálásához kell hozzáadnia a vállalati Dropbox a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a vállalati Dropbox a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Dropbox vállalati**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_search.png)

5. Az eredmények panelen válassza ki a **vállalati Dropbox**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Dropbox vállalati "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Dropbox vállalati megfelelőjére felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a vállalati Dropbox közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Dropbox vállalati.

Az Azure AD egyszeri bejelentkezést a vállalati Dropbox tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Dropbox az üzleti tesztfelhasználó létrehozása](#creating-a-dropbox-for-business-test-user)**  - való Britta Simon egy megfelelője a Dropbox vállalati, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a dropboxban üzleti alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD egyszeri bejelentkezést a vállalati Dropbox, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **vállalati Dropbox** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. Az a **Dropbox üzleti tartomány és az URL-címek** területen tegye a következőket:

    a. Jelentkezzen be a Dropbox üzleti bérlő számára. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "egyszeri bejelentkezés konfigurálása")
   
    b. A bal oldali navigációs ablaktábláján kattintson **felügyeleti konzol**. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "egyszeri bejelentkezés konfigurálása")
   
    c. Az a **felügyeleti konzol**, kattintson a **hitelesítési** a bal oldali navigációs ablaktáblán. 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "egyszeri bejelentkezés konfigurálása")
   
    d. Az a **egyszeri bejelentkezés** szakaszban jelölje be **egyszeri bejelentkezés engedélyezése**, és kattintson a **további** bontsa ki az ebben a szakaszban.  
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "egyszeri bejelentkezés konfigurálása")
   
    e. Az URL-Címének másolása mellett **is jelentkeznek be az e-mail cím beírásával vagy azok lépjen közvetlenül**. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
    f. Az Azure portálon a a **bejelentkezési URL-cím** szövegmezőhöz beilleszteni az URL-címet.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url.png)

     Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.dropbox.com/sso/<id>`

    > [!NOTE] 
    > Ez az érték nincs valós értékének. Frissítse az értéket a tényleges bejelentkezési URL-címet kap az egyszeri bejelentkezés szakasz. Ügyfél [Dropbox üzleti ügyfél-támogatási csoport](https://www.dropbox.com/business/contact) lekérni ezt az értéket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. A a **üzleti konfiguráció Dropbox** kattintson **Dropbox konfigurálása a vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **vállalati Dropbox** oldalán, nyissa meg a Dropbox üzleti bérlő, a a **egyszeri bejelentkezés** szakasza a **hitelesítési** lapon, hajtsa végre az alábbi lépéseket: 
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "egyszeri bejelentkezés konfigurálása")
   
    a. Kattintson a **szükséges**.
   
    b. Az Azure portálon a a **bejelentkezés konfigurálása** ablakban, a Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** értékét, és illessze be azt a **bejelentkezési URL** szövegmező.

    c. Kattintson a **tanúsítvány kiválasztása**, majd keresse meg a **Base64 kódolású tanúsítvány fájl**.

    d. Kattintson a **módosítások mentése** a DropBox üzleti bérlő konfigurálásának befejezéséhez.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png) 

2.  Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-dropbox-for-business-test-user"></a>A Dropbox az üzleti tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a vállalati Dropbox jön létre. Vállalati Dropbox támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.

Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Dropbox vállalati, egy új jön létre, a vállalati Dropbox elérésére tett kísérlet során.

>[!Note]
>Ha a felhasználót manuálisan kell létrehozni, lépjen kapcsolatba kell [Dropbox üzleti ügyfél-támogatási csoport](https://www.dropbox.com/business/contact) 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés a vállalati Dropbox Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**A dropbox alkalmazásba vállalati Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Dropbox vállalati**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Amikor az üzleti csempe a hozzáférési panelen a Dropbox kattint, üzleti alkalmazás kapja meg a Dropbox bejelentkezési oldalán.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-dropboxforbusiness-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

