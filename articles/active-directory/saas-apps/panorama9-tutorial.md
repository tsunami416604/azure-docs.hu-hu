---
title: 'Oktatóanyag: Azure Active Directoryval integrált Panorama9 |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Panorama9 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 21ee9267dc5a8bc4563e0ab7a35e4b1cff7fc0b2
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923792"
---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Oktatóanyag: Azure Active Directoryval integrált Panorama9

Ebben az oktatóanyagban elsajátíthatja Panorama9 integrálása az Azure Active Directory (Azure AD).

Panorama9 integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Panorama9 hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Panorama9 (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Panorama9, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Panorama9 egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Panorama9 hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-panorama9-from-the-gallery"></a>A gyűjteményből Panorama9 hozzáadása
Az Azure AD integrálása a Panorama9 konfigurálásához kell hozzáadnia Panorama9 a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Panorama9 hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Panorama9**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/tutorial_panorama9_search.png)

5. Az eredmények panelen válassza ki a **Panorama9**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/tutorial_panorama9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Panorama9

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Panorama9 a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Panorama9 közötti kapcsolat kapcsolatot kell létrehozni.

Panorama9, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Panorama9 tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Panorama9 tesztfelhasználó létrehozása](#creating-a-panorama9-test-user)**  - való Britta Simon valami Panorama9, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Panorama9 alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Panorama9, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Panorama9** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_samlbase.png)

3. Az a **Panorama9 tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://dashboard.panorama9.com/saml/access/3262`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `http://www.panorama9.com/saml20/<tenant-name>`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Panorama9 ügyfél-támogatási csoport](https://support.panorama9.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_general_400.png)

6. A a **Panorama9 konfigurációs** kattintson **konfigurálása Panorama9** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_configure.png) 

5. Egy másik webes böngészőablakban jelentkezzen be a Panorama9 vállalati webhely rendszergazdaként.

6. A felső eszköztáron kattintson **kezelése**, és kattintson a **bővítmények**.
   
   ![Bővítmények](./media/panorama9-tutorial/ic790023.png "bővítmények")
7. Az a **bővítmények** párbeszédpanel, kattintson a **egyszeri bejelentkezés**.
   
   ![Egyszeri bejelentkezés](./media/panorama9-tutorial/ic790024.png "egyszeri bejelentkezés")
8. Az a **beállítások** területen tegye a következőket:
   
   ![Beállítások](./media/panorama9-tutorial/ic790025.png "beállítások")
   
    a. A **identitási szolgáltató URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
   
    b. A **tanúsítvány-ujjlenyomat** szövegmező, illessze be a **ujjlenyomat** tanúsítványt, amely az Azure-portálon másolta értékét.    
         
9. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/panorama9-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-panorama9-test-user"></a>Panorama9 tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Panorama9 bejelentkezni, akkor ki kell építenie Panorama9 be.  

Panorama9, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Panorama9** vállalati hely rendszergazdaként.

2. Kattintson a felső menüben **kezelése**, és kattintson a **felhasználók**.
   
  ![Felhasználók](./media/panorama9-tutorial/ic790027.png "felhasználók")

3. A felhasználók szakaszban kattintson **+** új felhasználó hozzáadásához.

 ![Felhasználók](./media/panorama9-tutorial/ic790028.png "felhasználók")

4. Keresse meg a felhasználói adatok, írja be a felhasználó e-mail címe az egy érvényes Azure Active Directory szeretne kiépíteni a **E-mail** szövegmező.

5. A felhasználók szakaszban kattintson tudomást **mentése**.
   
> [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Panorama9 Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Panorama9, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Panorama9**.

    ![Egyszeri bejelentkezés konfigurálása](./media/panorama9-tutorial/tutorial_panorama9_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Panorama9 csempére kattint, akkor kell beolvasása automatikusan bejelentkezett Panorama9 alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/panorama9-tutorial/tutorial_general_01.png
[2]: ./media/panorama9-tutorial/tutorial_general_02.png
[3]: ./media/panorama9-tutorial/tutorial_general_03.png
[4]: ./media/panorama9-tutorial/tutorial_general_04.png

[100]: ./media/panorama9-tutorial/tutorial_general_100.png

[200]: ./media/panorama9-tutorial/tutorial_general_200.png
[201]: ./media/panorama9-tutorial/tutorial_general_201.png
[202]: ./media/panorama9-tutorial/tutorial_general_202.png
[203]: ./media/panorama9-tutorial/tutorial_general_203.png

