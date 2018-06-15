---
title: 'Oktatóanyag: Azure Active Directoryval integrált SumoLogic |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SumoLogic között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 79db92a5ae9de6bb8f2d425c692f4d3e04ee1d3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351086"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Oktatóanyag: Azure Active Directoryval integrált SumoLogic

Ebben az oktatóanyagban elsajátíthatja SumoLogic integrálása az Azure Active Directory (Azure AD).

SumoLogic integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a SumoLogic hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett SumoLogic (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs SumoLogic, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SumoLogic egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SumoLogic hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sumologic-from-the-gallery"></a>A gyűjteményből SumoLogic hozzáadása
Az Azure AD integrálása a SumoLogic konfigurálásához kell hozzáadnia SumoLogic a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SumoLogic hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **SumoLogic**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_search.png)

5. Az eredmények panelen válassza ki a **SumoLogic**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján SumoLogic.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SumoLogic a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SumoLogic közötti kapcsolat kapcsolatot kell létrehozni.

SumoLogic, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a SumoLogic tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SumoLogic tesztfelhasználó létrehozása](#creating-a-sumologic-test-user)**  - való Britta Simon valami SumoLogic, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SumoLogic alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés SumoLogic, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SumoLogic** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. Az a **SumoLogic tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenantname>.SumoLogic.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [SumoLogic ügyfél-támogatási csoport](https://www.sumologic.com/contact-us/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_general_400.png)

6. A a **SumoLogic konfigurációs** kattintson **konfigurálása SumoLogic** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a SumoLogic vállalati webhely rendszergazdaként.

8. Ugrás a **kezelése \> biztonsági**.
   
    ![Kezelése](./media/active-directory-saas-sumologic-tutorial/ic778556.png "kezelése")

9. Kattintson a **SAML**.
   
    ![Globális biztonsági beállításokat](./media/active-directory-saas-sumologic-tutorial/ic778557.png "globális biztonsági beállításokat")

10. Az a **konfiguráció válasszon vagy hozzon létre egy új** listára, válassza ki **az Azure AD**, és kattintson a **konfigurálása**.
   
    ![Konfigurálja a SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778558.png "SAML 2.0 konfigurálása")

11. Az a **konfigurálása a SAML 2.0-s** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Konfigurálja a SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778559.png "SAML 2.0 konfigurálása")
   
    a. Az a **Konfigurációnévvel** szövegmezőhöz típus **az Azure AD**. 

    b. Válassza ki **hibakeresési mód**.

    c. Az a **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta. 

    d. A a **Authn kérelem URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    e. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be a teljes tanúsítványt **X.509 tanúsítvány** szövegmező.

    f. Mint **E-mail attribútum**, jelölje be **használható SAML tulajdonos**.  

    g. Válassza ki **Szolgáltató kezdeményezett bejelentkezési konfigurációs**.

    h. Az a **bejelentkezési elérési** szövegmezőhöz típus **Azure** kattintson **mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sumologic-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sumologic-test-user"></a>SumoLogic tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók SumoLogic bejelentkezni, akkor ki kell építenie SumoLogic.  

* SumoLogic, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **SumoLogic** bérlő.

2. Ugrás a **kezelése \> felhasználók**.
   
    ![Felhasználók](./media/active-directory-saas-sumologic-tutorial/ic778561.png "felhasználók")

3. Kattintson a **Hozzáadás** parancsra.
   
    ![Felhasználók](./media/active-directory-saas-sumologic-tutorial/ic778562.png "felhasználók")

4. Az a **új felhasználó** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Új felhasználó](./media/active-directory-saas-sumologic-tutorial/ic778563.png "új felhasználó") 
 
    a. Írja be a kapcsolódó információkat kiépítése a kívánt Azure AD-fiókot, a **Utónév**, **Vezetéknév**, és **E-mail** szövegmezőből.
  
    b. Szerepkör kiválasztása.
  
    c. Mint **állapot**, jelölje be **aktív**.
  
    d. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Bármely más SumoLogic felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz SumoLogic által nyújtott API-k. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SumoLogic Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése SumoLogic, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SumoLogic**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen SumoLogic csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SumoLogic alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_203.png

