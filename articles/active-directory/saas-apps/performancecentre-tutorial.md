---
title: 'Oktatóanyag: Azure Active Directoryval integrált PerformanceCentre |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és PerformanceCentre között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: e9611d5c8891d6d2e3570f84dc5621bb35d322ea
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923692"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Oktatóanyag: Azure Active Directoryval integrált PerformanceCentre

Ebben az oktatóanyagban elsajátíthatja PerformanceCentre integrálása az Azure Active Directory (Azure AD).

PerformanceCentre integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a PerformanceCentre hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett PerformanceCentre (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs PerformanceCentre, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy PerformanceCentre egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből PerformanceCentre hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-performancecentre-from-the-gallery"></a>A gyűjteményből PerformanceCentre hozzáadása
Az Azure AD integrálása a PerformanceCentre konfigurálásához kell hozzáadnia PerformanceCentre a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből PerformanceCentre hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **PerformanceCentre**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

5. Az eredmények panelen válassza ki a **PerformanceCentre**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján PerformanceCentre.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó PerformanceCentre a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a PerformanceCentre közötti kapcsolat kapcsolatot kell létrehozni.

PerformanceCentre, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a PerformanceCentre tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[PerformanceCentre tesztfelhasználó létrehozása](#creating-a-performancecentre-test-user)**  - való Britta Simon valami PerformanceCentre, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az PerformanceCentre alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés PerformanceCentre, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **PerformanceCentre** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. Az a **PerformanceCentre tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `http://companyname.performancecentre.com/saml/SSO`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [PerformanceCentre ügyfél-támogatási csoport](https://www.performancecentre.com/contact-us/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_general_400.png)

6. A a **PerformanceCentre konfigurációs** kattintson **konfigurálása PerformanceCentre** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Bejelentkezés a **PerformanceCentre** vállalati hely rendszergazdaként.

8. A lap bal oldalán kattintson **konfigurálása**.
   
    ![Az Azure AD-egyszeri bejelentkezés][10]

9. A lap bal oldalán kattintson **vegyes**, és kattintson a **egyszeri bejelentkezés**.
   
    ![Az Azure AD-egyszeri bejelentkezés][11]

10. Mint **protokoll**, jelölje be **SAML**.
   
    ![Az Azure AD-egyszeri bejelentkezés][12]

11. Nyissa meg a letöltött metaadat-fájlt a Jegyzettömbben, másolja a tartalmat, illessze be azt a **Identity Provider metaadatok** szövegmező, és kattintson a **mentése**.
   
    ![Az Azure AD-egyszeri bejelentkezés][13]

12. Ellenőrizze, hogy az értékek a **entitás alap URL-cím** és **entitás azonosító URL-cím** helyes-e.
    
     ![Az Azure AD-egyszeri bejelentkezés][14]

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-performancecentre-test-user"></a>PerformanceCentre tesztfelhasználó létrehozása

Ez a szakasz célja PerformanceCentre Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta PerformanceCentre létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a PerformanceCentre vállalati webhely.

2. A bal oldali menüben kattintson a **Interrelate**, és kattintson a **létrehozása résztvevő**.
   
    ![Felhasználó létrehozása][400]

3. Az a **Interrelate - résztvevő létrehozása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználó létrehozása][401]
    
    a. Írja be a szükséges attribútumokat a Britta Simon kapcsolódó szövegmezőből.
    
    >[!IMPORTANT]
    >Britta tartozó felhasználónév attribútum PerformanceCentre kell lennie az Azure AD ugyanaz, mint a felhasználó nevét.
    
    b. Válassza ki **ügyfél rendszergazda** , **válassza ki a szerepkör**.
    
    c. Kattintson a **Save** (Mentés) gombra. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PerformanceCentre Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése PerformanceCentre, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **PerformanceCentre**.

    ![Egyszeri bejelentkezés konfigurálása](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.  

Ha a hozzáférési panelen PerformanceCentre csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az PerformanceCentre alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

