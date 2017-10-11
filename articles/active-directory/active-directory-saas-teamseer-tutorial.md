---
title: "Oktatóanyag: Azure Active Directoryval integrált TeamSeer |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és TeamSeer között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 2a5e8f6d1443681c43db95da5cef0b7f2ef92291
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Oktatóanyag: Azure Active Directoryval integrált TeamSeer

Ebben az oktatóanyagban elsajátíthatja TeamSeer integrálása az Azure Active Directory (Azure AD).

TeamSeer integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a TeamSeer hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett TeamSeer (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs TeamSeer, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy TeamSeer egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből TeamSeer hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-teamseer-from-the-gallery"></a>A gyűjteményből TeamSeer hozzáadása
TeamSeer az Azure ad integrálása konfigurálásához szüksége TeamSeer hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből TeamSeer hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **TeamSeer**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_search.png)

5. Az eredmények panelen válassza ki a **TeamSeer**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján TeamSeer

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó TeamSeer a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a TeamSeer közötti kapcsolat kapcsolatot kell létrehozni.

TeamSeer, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TeamSeer tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[TeamSeer tesztfelhasználó létrehozása](#creating-a-teamseer-test-user)**  - való Britta Simon valami TeamSeer, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az TeamSeer alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés TeamSeer, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **TeamSeer** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_samlbase.png)

3. Az a **TeamSeer tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_url.png)

     Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.teamseer.com/<companyid>`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [TeamSeer ügyfél-támogatási csoport](http://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) az értéket be kell olvasni. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_general_400.png)

6. A a **TeamSeer konfigurációs** kattintson **konfigurálása TeamSeer** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_configure.png)

7. Egy másik webes böngészőablakban jelentkezzen be a TeamSeer vállalati webhely rendszergazdaként.

8. Ugrás a **HR Admin**.
   
    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/ic789634.png "HR-rendszergazda")

9. Kattintson a **telepítő**.
   
    ![A telepítő](./media/active-directory-saas-teamseer-tutorial/ic789635.png "beállítása")

10. Kattintson a **SAML szolgáltató részleteinek beállítása**.
   
    ![SAML-alapú beállítások](./media/active-directory-saas-teamseer-tutorial/ic789636.png "SAML-beállítások")

11. A SAML szolgáltató részleteket tartalmazó területen tegye a következőket:
   
    ![SAML-alapú beállítások](./media/active-directory-saas-teamseer-tutorial/ic789637.png "SAML-beállítások")   

    a. Beillesztés a **egyszeri bejelentkezési URL-címe** az értéket a **URL-cím** szövegmező.
          
    b. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, meg a tartalom másolása a vágólapra és illessze be azt a **IdP nyilvános tanúsítvány** szövegmező.

12. A SAML-szolgáltató konfigurációjának befejezéséhez hajtsa végre az alábbi lépéseket:
    
    ![SAML-alapú beállítások](./media/active-directory-saas-teamseer-tutorial/ic789638.png "SAML-beállítások") 

    a. Az a **teszt E-mail címek**, írja be a tesztfelhasználó számára e-mail címét. 
  
    b. Az a **kibocsátó** szövegmező, a szolgáltató kibocsátó URL-címét. 
  
    c. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-teamseer-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-teamseer-test-user"></a>TeamSeer tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TeamSeer bejelentkezni, akkor ki kell építenie a ShiftPlanning. TeamSeer, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **TeamSeer** vállalati hely rendszergazdaként.

2. Hajtsa végre a következő lépéseket:
   
    ![HR Admin](./media/active-directory-saas-teamseer-tutorial/ic789640.png "HR-rendszergazda")  
 
    a. Ugrás a **HR Admin \> felhasználók**.
  
    b. Kattintson a **az új felhasználó varázsló futtatása**.

3. Az a **felhasználó adatait** területen tegye a következőket:
   
    ![Felhasználó adatait](./media/active-directory-saas-teamseer-tutorial/ic789641.png "felhasználó részletei")

    a. Típus a **Utónév**, **vezetékneve**, **felhasználónevet (E-mail címet)** a egy érvényes ki kívánja építeni a a kapcsolódó szövegmezők az AAD-fiókba.
  
    b. Kattintson a **Tovább** gombra.

4. Kövesse a képernyőn megjelenő utasításokat az új felhasználót, és kattintson a **Befejezés**.

>[!NOTE]
>Bármely más TeamSeer felhasználói fiók létrehozása eszközök vagy TeamSeer kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TeamSeer Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése TeamSeer, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TeamSeer**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-teamseer-tutorial/tutorial_teamseer_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamseer-tutorial/tutorial_general_203.png

