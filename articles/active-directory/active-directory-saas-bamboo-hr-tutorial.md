---
title: "Oktatóanyag: Azure Active Directoryval integrált BambooHR |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és BambooHR között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: jeedes
ms.openlocfilehash: 37a560ed6ea507084a5bbb7b022d2905746d2ba1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Oktatóanyag: Azure Active Directoryval integrált BambooHR

Ebben az oktatóanyagban elsajátíthatja BambooHR integrálása az Azure Active Directory (Azure AD).

BambooHR integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a BambooHR hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett BambooHR (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs BambooHR, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy BambooHR egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből BambooHR hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-bamboohr-from-the-gallery"></a>A gyűjteményből BambooHR hozzáadása
Az Azure AD integrálása a BambooHR konfigurálásához kell hozzáadnia BambooHR a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből BambooHR hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **BambooHR**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_search.png)

5. Az eredmények panelen válassza ki a **BambooHR**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján BambooHR

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó BambooHR a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a BambooHR közötti kapcsolat kapcsolatot kell létrehozni.

BambooHR, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a BambooHR tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[BambooHR tesztfelhasználó létrehozása](#creating-a-bamboohr-test-user)**  - való Britta Simon valami BambooHR, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az BambooHR alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés BambooHR, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **BambooHR** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. Az a **BambooHR tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<company>.bamboohr.com`
    
    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [BambooHR ügyfél-támogatási csoport](https://www.bamboohr.com/contact.php) lekérni ezt az értéket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. A a **BambooHR konfigurációs** kattintson **konfigurálása BambooHR** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. Egy másik webes böngészőablakban jelentkezzen be a BambooHR vállalati webhely rendszergazdaként.

7. A kezdőlapon hajtsa végre a következő lépéseket:
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "egyszeri bejelentkezés")   

    a. Kattintson a **alkalmazások**.
   
    b. Kattintson a bal oldali alkalmazások menüben **egyszeri bejelentkezés**.
   
    c. Kattintson a **SAML-alapú egyszeri bejelentkezést**.

8. Az a **SAML-alapú egyszeri bejelentkezést** területen tegye a következőket:
   
    ![SAML-alapú egyszeri bejelentkezést](./media/active-directory-saas-bamboo-hr-tutorial/ic796692.png "SAML-alapú egyszeri bejelentkezést.")
   
    a. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** be értéket a **Egyszeri bejelentkezési URL-cím** szövegmező.
      
    b. Nyissa meg a Jegyzettömbben az Azure portálról letöltött base-64 kódolású tanúsítvány, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező
   
    c. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-bamboohr-test-user"></a>BambooHR tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók BambooHR bejelentkezni, akkor ki kell építenie a BambooHR.  

BambooHR, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **BambooHR** hely rendszergazdaként.

2. A felső eszköztáron kattintson **beállítások**.
   
    ![Beállítás](./media/active-directory-saas-bamboo-hr-tutorial/ic796694.png "beállítás")

3. Kattintson a **áttekintése**.

4. A bal oldali ablaktáblában lépjen **biztonsági \> felhasználók**.

5. Írja be a felhasználónevet, jelszót és egy érvényes szeretné azokat a kapcsolódó szövegmezők rendelkezés AAD-fiókhoz tartozó e-mail cím.

6. Kattintson a **Save** (Mentés) gombra.
        
>[!NOTE]
>Bármely más BambooHR felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz BambooHR által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés BambooHR Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése BambooHR, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **BambooHR**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen BambooHR csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az BambooHR alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

