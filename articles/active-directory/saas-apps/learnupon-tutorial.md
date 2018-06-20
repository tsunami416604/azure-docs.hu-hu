---
title: 'Oktatóanyag: Azure Active Directoryval integrált LearnUpon |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és LearnUpon között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 7a50d1f17293bb86c69ece10e05af0017b1fabf9
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219455"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Oktatóanyag: Azure Active Directoryval integrált LearnUpon

Ebben az oktatóanyagban elsajátíthatja LearnUpon integrálása az Azure Active Directory (Azure AD).

LearnUpon integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a LearnUpon hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett LearnUpon (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs LearnUpon, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy LearnUpon egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből LearnUpon hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-learnupon-from-the-gallery"></a>A gyűjteményből LearnUpon hozzáadása
Az Azure AD integrálása a LearnUpon konfigurálásához kell hozzáadnia LearnUpon a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből LearnUpon hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **LearnUpon**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/tutorial_learnupon_search.png)

5. Az eredmények panelen válassza ki a **LearnUpon**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján LearnUpon.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó LearnUpon a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a LearnUpon közötti kapcsolat kapcsolatot kell létrehozni.

LearnUpon, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a LearnUpon tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[LearnUpon tesztfelhasználó létrehozása](#creating-a-learnupon-test-user)**  - való Britta Simon valami LearnUpon, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az LearnUpon alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés LearnUpon, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **LearnUpon** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. Az a **LearnUpon tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Ne feledje, hogy ez a nem a tényleges érték. Ez az érték a tényleges válasz URL-címet frissíteni kell. Ez az érték ügyfél megszerezni [LearnUpon támogatási csoport](https://www.learnupon.com/features/support/).



4. Az a **SAML-aláíró tanúsítványa** szakaszban, keresse meg a **ujjlenyomat** -ez nem kerülnek be a LearnUpon SAML-beállításokat.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_general_400.png)

6. A a **LearnUpon konfigurációs** kattintson **konfigurálása LearnUpon** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Nyisson meg egy másik böngészőben példány és a bejelentkezési LearnUpon be rendszergazdai fiókkal. 

8. Kattintson a **beállítások** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_06.png)

9. Kattintson a **egyszeri bejelentkezés – SAML**, és kattintson a **általános beállítások** SAML-beállítások konfigurálása.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

10. Az a **általános beállítások** területen tegye a következőket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Válassza ki **engedélyezett**.

    b. Válassza ki **verzió** , **2.0**.

    c. Válassza ki **feltételek kihagyása** , **nem**.

    d. Az a **SAML-jogkivonat utáni param neve** szövegmezőhöz kérelem feladás egy vagy több paramétert az SAML-alapú ügyfél URL-címe nevét a fenti típus, amely tartalmazza a SAML-előfeltétel ellenőrzése és a hitelesített – például a **SAMLResponse**.

    e. Az a **azonosító formátuma** szövegmező, hol található a SAML-előfeltétel a felhasználók azonosítója (E-mail címet) jelző érték található – például típus **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.
  
    f. Az a **azonosítása szolgáltató helye** szövegmező, írja be az értéket, amely jelzi, ahol a felhasználók kapnak, ha az Azure portál bejelentkezési képernyőjéről a feltöltött ikonra kattintanak.
  
    g. Az a **jelentkezzen ki az URL-cím** szövegmező, illessze be a **Sign-Out URL-cím** , amely az Azure portálról másolta.
    
    h. Kattintson a **ujját megrendelése kezelése**, majd töltse fel az ujjlenyomat a letöltött tanúsítvány.

11. Kattintson a **felhasználói beállítások**, majd végezze el az alábbi lépéseket:
   
     ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. Az a **Keresztnév azonosító formátuma** szövegmezőben, az érték, amely közli, amennyiben az a SAML-előfeltétel a felhasználók Keresztnév található – például típusa: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. Az a **utolsó azonosító formátuma** szövegmezőben, az érték, amely közli, amennyiben az a SAML-előfeltétel a felhasználók Vezetéknév található – például típusa: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-learnupon-test-user"></a>LearnUpon tesztfelhasználó létrehozása

Ez a szakasz célja LearnUpon Britta Simon nevű felhasználót létrehozni. LearnUpon támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az LearnUpon elérésére, ha még nem létezik tett kísérlet során. [Az Azure AD-egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Ha hozzon létre manuálisan egy felhasználó van szüksége, forduljon a kell [LearnUpon támogatási csoport](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés LearnUpon Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése LearnUpon, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **LearnUpon**.

    ![Egyszeri bejelentkezés konfigurálása](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen LearnUpon csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az LearnUpon alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

