---
title: "Oktatóanyag: Azure Active Directoryval integrált Mindflash |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Mindflash között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 90de7b6a82d88f9407a35fbfebe8a652928d76cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Oktatóanyag: Azure Active Directoryval integrált Mindflash

Ebben az oktatóanyagban elsajátíthatja Mindflash integrálása az Azure Active Directory (Azure AD).

Mindflash integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Mindflash hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Mindflash (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Mindflash, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Mindflash egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Mindflash hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-mindflash-from-the-gallery"></a>A gyűjteményből Mindflash hozzáadása
Az Azure AD integrálása a Mindflash konfigurálásához kell hozzáadnia Mindflash a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Mindflash hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Mindflash**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_search.png)

5. Az eredmények panelen válassza ki a **Mindflash**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Mindflash.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Mindflash a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Mindflash közötti kapcsolat kapcsolatot kell létrehozni.

Mindflash, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Mindflash tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Mindflash tesztfelhasználó létrehozása](#creating-a-mindflash-test-user)**  - való Britta Simon valami Mindflash, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Mindflash alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Mindflash, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Mindflash** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_samlbase.png)

3. Az a **Mindflash tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.mindflash.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.mindflash.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Mindflash ügyfél-támogatási csoport](https://www.mindflash.com/contact/) beolvasni ezeket az értékeket. 
 


4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mindflash-tutorial/tutorial_general_400.png)

6. Egyszeri bejelentkezés konfigurálása **Mindflash** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [Mindflash támogatási csoport](https://www.mindflash.com/contact/).

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mindflash-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mindflash-test-user"></a>Mindflash tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mindflash bejelentkezni, akkor ki kell építenie Mindflash be. Mindflash, ha egy kézi tevékenység.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Mindflash** vállalati hely rendszergazdaként.

2. Ugrás a **felhasználók kezelése**.
   
    ![Felhasználók kezelése](./media/active-directory-saas-mindflash-tutorial/ic787140.png "felhasználók kezelése")

3. Kattintson a **felhasználó hozzáadása**, és kattintson a **új**.

4. Az a **az új felhasználók hozzáadása** területen az alábbi lépésekkel egy érvényes Azure AD fiók kiépítése:
   
    ![Új felhasználók hozzáadása az](./media/active-directory-saas-mindflash-tutorial/ic787141.png "új felhasználók hozzáadása")
   
    a. Az a **Utónév** szövegmezőhöz típus **Utónév** , a felhasználó **Britta**.

    b. Az a **Vezetéknév** szövegmezőhöz típus **Vezetéknév** , a felhasználó **Simon**.
    
    c. Az a **E-mail** szövegmezőhöz típus **E-mail cím** , a felhasználó  **BrittaSimon@contoso.com** .

    b. Kattintson az **Add** (Hozzáadás) parancsra.

>[!NOTE]
>Bármely más Mindflash felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Mindflash által nyújtott API-k. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mindflash Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Mindflash, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Mindflash**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mindflash-tutorial/tutorial_mindflash_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Mindflash csempére kattint, Mindflash alkalmazás bejelentkezési oldalán szerezheti be.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mindflash-tutorial/tutorial_general_203.png

