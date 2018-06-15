---
title: 'Oktatóanyag: Azure Active Directoryval integrált cukor CRM |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és cukor CRM között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: d4133b5881d25b9481e08ba0597485b62e0c52e5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352633"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Oktatóanyag: Azure Active Directoryval integrált cukor CRM

Ebben az oktatóanyagban elsajátíthatja cukor CRM integrálása az Azure Active Directory (Azure AD).

Cukor CRM integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a cukor CRM-hez
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a cukor CRM-hez (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a cukor CRM, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- Egy cukor CRM egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből cukor CRM hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sugar-crm-from-the-gallery"></a>A gyűjteményből cukor CRM hozzáadása
Az Azure AD integrálása a cukor CRM konfigurálásához kell hozzáadnia cukor CRM a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből cukor CRM hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **cukor CRM**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_search.png)

5. Az eredmények panelen válassza ki a **cukor CRM**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés cukor CRM "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó cukor CRM a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a cukor CRM közötti kapcsolat kapcsolatot kell létrehozni.

A cukor CRM, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a cukor CRM tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Cukor CRM tesztfelhasználó létrehozása](#creating-a-sugar-crm-test-user)**  - való egy megfelelője a Britta Simon cukor CRM-hez, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az cukor CRM alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés cukor CRM, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **cukor CRM** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

3. Az a **cukor CRM tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [cukor CRM ügyfél-támogatási csoport](https://support.sugarcrm.com/) az értéket be kell olvasni. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_400.png)

6. A a **cukor CRM konfigurációs** kattintson **konfigurálása cukor CRM** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a cukor CRM vállalati webhely rendszergazdaként.

8. Ugrás a **Admin**.
   
    ![Felügyeleti](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "rendszergazda")

9. Az a **felügyeleti** kattintson **jelszókezelés**.
   
    ![Felügyeleti](./media/active-directory-saas-sugarcrm-tutorial/ic795889.png "felügyeleti")

10. Válassza ki **SAML-alapú hitelesítés engedélyezéséhez**.
   
    ![Felügyeleti](./media/active-directory-saas-sugarcrm-tutorial/ic795890.png "felügyeleti")

11. Az a **SAML-alapú hitelesítés** területen tegye a következőket:
   
    ![SAML-alapú hitelesítés](./media/active-directory-saas-sugarcrm-tutorial/ic795891.png "SAML-alapú hitelesítés")  
 
    a. A a **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
  
    b. A a **SLO URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.
  
    c. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be a teljes tanúsítványt **X.509 tanúsítvány** szövegmező.
  
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sugar-crm-test-user"></a>Cukor CRM tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók cukor CRM bejelentkezni, akkor ki kell építenie a cukor CRM-hez.

Cukor CRM, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **cukor CRM** vállalati hely rendszergazdaként.

2. Ugrás a **Admin**.
   
    ![Felügyeleti](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "rendszergazda")

3. Az a **felügyeleti** kattintson **felhasználókezelés**.
   
    ![Felügyeleti](./media/active-directory-saas-sugarcrm-tutorial/ic795893.png "felügyeleti")

4. Ugrás a **felhasználók \> új felhasználó létrehozása**.
   
    ![Új felhasználó létrehozása](./media/active-directory-saas-sugarcrm-tutorial/ic795894.png "új felhasználó létrehozása")

5. Az a **felhasználói profil** lapra, hajtsa végre a következő lépéseket:
   
    ![Új felhasználó](./media/active-directory-saas-sugarcrm-tutorial/ic795895.png "új felhasználó")

    a. Típus a **felhasználónév**, **Vezetéknév**, és **e-mail cím** a kapcsolódó szövegmezők be egy érvényes Azure Active Directory felhasználó.
  
6. Mint **állapot**, jelölje be **aktív**.

7. A jelszó lapon hajtsa végre a következő lépéseket:
   
    ![Új felhasználó](./media/active-directory-saas-sugarcrm-tutorial/ic795896.png "új felhasználó")

    a. A kapcsolódó szövegmezőbe írja be a jelszót.

    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más cukor CRM felhasználói fiók létrehozása eszközök, vagy rendelkezés AAD felhasználói fiókokhoz cukor CRM által nyújtott API-k. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon használandó által biztosított hozzáférés cukor CRM, Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése cukor CRM, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **cukor CRM**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen cukor CRM csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az cukor CRM-alkalmazás.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_203.png

