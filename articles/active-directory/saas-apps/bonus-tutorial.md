---
title: 'Oktatóanyag: Azure Active Directoryval integrált Bonusly |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Bonusly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: d396ccbd8594e525f9f0f9dc4dabc8805fa2364b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927545"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Oktatóanyag: Azure Active Directoryval integrált Bonusly

Ebben az oktatóanyagban elsajátíthatja Bonusly integrálása az Azure Active Directory (Azure AD).

Bonusly integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Bonusly hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Bonusly (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Bonusly, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Bonusly egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Bonusly hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-bonusly-from-the-gallery"></a>A gyűjteményből Bonusly hozzáadása
Az Azure AD integrálása a Bonusly konfigurálásához kell hozzáadnia Bonusly a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Bonusly hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Bonusly**, jelölje be **Bonusly** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Bonusly](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Bonusly.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Bonusly a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Bonusly közötti kapcsolat kapcsolatot kell létrehozni.

Bonusly, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Bonusly tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Bonusly tesztfelhasználó létrehozása](#create-a-bonusly-test-user)**  - való Britta Simon valami Bonusly, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Bonusly alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Bonusly, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Bonusly** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

3. Az a **Bonusly tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információkat bonusly tartomány- és URL-címek](./media/bonus-tutorial/tutorial_bonusly_url.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges válasz URL-címet. Ügyfél [Bonusly támogatási csoport](https://Bonusly/contact) az értéket be kell olvasni.
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** érték a tanúsítványból.

    ![A tanúsítvány letöltési hivatkozását](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/bonus-tutorial/tutorial_general_400.png)

6. Az a **Bonusly konfigurációs** kattintson **Bonusly konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Bonusly konfiguráció](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

7. Egy másik böngészőablakban, jelentkezzen be a **Bonusly** bérlő.

8. A felső eszköztáron kattintson **beállítások**, majd válassza ki **integrációja és az alkalmazások**.
   
    ![Bonusly közösségi szakasz](./media/bonus-tutorial/ic773686.png "Bonusly")
9. A **egyszeri bejelentkezés**, jelölje be **SAML**.

10. Az a **SAML** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Bonusly Saml párbeszédpanel lap](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. Az a **IdP SSO célként megadott URL** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
   
    b. Az a **IdP kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta. 

    c. A a **IdP bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    d. Beillesztés a **ujjlenyomat** az Azure portálon átmásolja értéket a **tanúsítvány-ujjlenyomat** szövegmező.
   
11. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/bonus-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/bonus-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/bonus-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/bonus-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-bonusly-test-user"></a>Bonusly tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Bonusly bejelentkezni, akkor ki kell építenie a Bonusly. Bonusly, ha egy kézi tevékenység.

>[!NOTE]
>Bármely más Bonusly felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Bonusly által nyújtott API-k.
>  

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. A webböngésző ablakának jelentkezzen be a Bonusly bérlő.

2. Kattintson a **beállítások**.
 
    ![Beállítások](./media/bonus-tutorial/ic781041.png "beállítások")

3. Kattintson a **felhasználók és a prémium** fülre.
   
    ![Felhasználók és a prémium](./media/bonus-tutorial/ic781042.png "felhasználók és a prémium")

4. Kattintson a **felhasználók kezelése**.
   
    ![Felhasználók kezelése](./media/bonus-tutorial/ic781043.png "felhasználók kezelése")

5. Kattintson a **felhasználó hozzáadása**.
   
    ![Felhasználó hozzáadása](./media/bonus-tutorial/ic781044.png "felhasználó hozzáadása")

6. Az a **felhasználó hozzáadása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználó hozzáadása](./media/bonus-tutorial/ic781045.png "felhasználó hozzáadása")  

    a. Az a **Utónév** szövegmező, írja be például a felhasználó utónevét **Britta**.

    b. Az a **Vezetéknév** szövegmező, írja be például a felhasználó vezetéknevét **Simon**.
 
    c. Az a **E-mail** szövegmező, adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    d. Kattintson a **Save** (Mentés) gombra.
   
     >[!NOTE]
     >Az Azure AD fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Bonusly Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Bonusly, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Bonusly**.

    ![Az alkalmazások listáját a Bonusly hivatkozásra](./media/bonus-tutorial/tutorial_bonusly_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha Bonusly csempére kattint a hozzáférési panelen, meg kell beolvasni automatikusan bejelentkezett az Bonusly alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

