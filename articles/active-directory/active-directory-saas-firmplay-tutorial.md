---
title: "Oktatóanyag: Azure Active Directoryval integrált FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és FirmPlay - a személyzeti osztályon dolgozó tanácsadáson között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
ms.openlocfilehash: e1381f2273ee961a4b72dede8cf8e017814ba909
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Oktatóanyag: Azure Active Directoryval integrált FirmPlay - a személyzeti osztályon dolgozó tanácsadáson

Ebben az oktatóanyagban elsajátíthatja FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon az Azure Active Directoryval (Azure AD) integrálása.

FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáférhet FirmPlay - alkalmazott tanácsadáson személyzeti osztályon az Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

FirmPlay - alkalmazott tanácsadáson személyzeti osztályon, az Azure AD-integrációs konfigurálni kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy FirmPlay - alkalmazott tanácsadáson felvételi egyszeri bejelentkezés engedélyezve van az előfizetésben


> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.


Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Hozzáadás FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés


## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Hozzáadás FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon a gyűjteményből
A FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon az Azure AD-integráció konfigurálása kell hozzáadnia a FirmPlay - alkalmazott tanácsadáson a kezelt SaaS-alkalmazások listájára a gyűjteményből személyzeti osztályon.

**Adja hozzá a FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **FirmPlay - a személyzeti osztályon dolgozó tanácsadáson**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_001.png)

5. Az eredmények panelen válassza ki a **FirmPlay - a személyzeti osztályon dolgozó tanácsadáson**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés használatához az Azure AD meg kell tudni, hogy milyen a párjukhoz felhasználó FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon egy felhasználó számára az Azure ad-ben. Más szóval hivatkozás közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó a FirmPlay - alkalmazott tanácsadáson felvételi kell létrehozni.

A hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon.

Az Azure AD egyszeri bejelentkezést a FirmPlay - tesztelése és konfigurálása személyzeti osztályon, az alkalmazott tanácsadáson kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon tesztfelhasználó létrehozása](#creating-a-firmplay---employee-advocacy-for-recruiting-test-user)**  - való Britta Simon valami FirmPlay: alkalmazott tanácsadáson felvételi, amely csatolva rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és a FirmPlay - alkalmazott tanácsadáson személyzeti osztályon alkalmazáshoz az egyszeri bejelentkezés konfigurálása.

**A következő lépésekkel FirmPlay - alkalmazott tanácsadáson személyzeti osztályon, az Azure AD az egyszeri bejelentkezés konfigurálása:**

1. Az Azure felügyeleti portálján a a **FirmPlay - a személyzeti osztályon dolgozó tanácsadáson** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_01.png)

3. Az a **FirmPlay - tartomány felvétele és URL-címek alkalmazott tanácsadáson** részben, a a **URL-cím bejelentkezési** szövegmező, adja meg a következő minta használatával URL-címe:`https://<your-subdomain>.firmplay.com/`

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_02.png)

    > [!NOTE] 
    > Ne feledje, hogy ez a nem a tényleges érték. Ezt az értéket a tényleges bejelentkezési URL-cím frissíteni kell. Ügyfél [FirmPlay - alkalmazott tanácsadáson személyzeti osztályon támogatási csoport](mailto:engineering@firmplay.com) lekérni ezt az értéket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **hozzon létre új tanúsítvány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_03.png)   

5. A a **új tanúsítvány létrehozása** párbeszédpanel, kattintson a naptár ikonra, és válasszon egy **lejárati dátum**. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_general_300.png)

6. Az a **SAML-aláíró tanúsítványa** szakaszban jelölje be **új tanúsítvány aktiválásához** kattintson **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_04.png)

7. Az előugró **helyettesítő tanúsítvány** ablak, kattintson a **OK**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_general_400.png)

8. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (base64)** , és mentse a tanúsítványfájlt, a számítógépen. 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_05.png) 

9. A a **FirmPlay - konfiguráció felvételi alkalmazott tanácsadáson** kattintson **FirmPlay konfigurálása - a személyzeti osztályon dolgozó tanácsadáson** megnyitásához **bejelentkezés konfigurálása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_06.png) 

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_07.png)

10. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, lépjen kapcsolatba [FirmPlay - alkalmazott tanácsadáson személyzeti osztályon támogatási csoport](mailto:engineering@firmplay.com) és adja meg a következőket: 

    • A letöltött **tanúsítványfájl**

    • A **SAML-alapú egyszeri bejelentkezési szolgáltatás URL-címe**

    • A **SAML entitás azonosítója**

    • A **kijelentkezési URL-címe**
  

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-firmplay-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 



### <a name="creating-a-firmplay---employee-advocacy-for-recruiting-test-user"></a>Egy FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon meghívta FirmPlay - alkalmazott tanácsadáson személyzeti osztályon a felhasználó létrehozása. Adjon együttműködve [FirmPlay - alkalmazott tanácsadáson személyzeti osztályon támogatási csoport](mailto:engineering@firmplay.com) felhasználót is hozzáadhat a FirmPlay - alkalmazott tanácsadáson személyzeti osztályon platform.


### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított saját hozzáférés FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése FirmPlay - alkalmazott tanácsadáson a személyzeti osztályon, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **FirmPlay - a személyzeti osztályon dolgozó tanácsadáson**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-firmplay-tutorial/tutorial_firmplay_50.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    


### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A FirmPlay - alkalmazott tanácsadáson személyzeti osztályon csempe a hozzáférési panelen kattintva meg kell beolvasni automatikusan bejelentkezett a FirmPlay - alkalmazott tanácsadáson személyzeti osztályon alkalmazáshoz való.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-firmplay-tutorial/tutorial_general_203.png