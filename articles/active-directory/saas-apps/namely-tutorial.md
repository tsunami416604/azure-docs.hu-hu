---
title: 'Oktatóanyag: Azure Active Directoryval integrált nevezetesen |} Microsoft Docs'
description: Egyszeri bejelentkezés Azure Active Directory közötti konfigurálásával, nevezetesen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: eb1d175a2d0fc687cccac9b5c5bf53bcf397909f
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923508"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Oktatóanyag: Azure Active Directoryval integrált nevezetesen

Ebben az oktatóanyagban elsajátíthatja, nevezetesen integrálása az Azure Active Directory (Azure AD).

Kulcstartó integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki nevezetesen Azure AD-ben
- Engedélyezheti a felhasználók automatikusan el nevezetesen a bejelentkezett (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD konfigurálása, hogy az integrációs kell a következő elemek:

- Az Azure AD szolgáltatásra
- Kulcstartó egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből nevezetesen hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-namely-from-the-gallery"></a>A gyűjteményből nevezetesen hozzáadása
Nevezetesen az Azure AD integrálása konfigurálásához kell hozzáadnia nevezetesen a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Kulcstartó adja hozzá a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **nevezetesen**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/tutorial_namely_search.png)

5. Az eredmények panelen válassza ki a **nevezetesen**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/tutorial_namely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést, nevezetesen "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó nevezetesen egy felhasználó az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a hivatkozás kapcsolatának nevezetesen kell létrehozni.

A kulcstartó, rendelje az értékét a **felhasználónév** az Azure AD-be a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD tesztelése és konfigurálása egyszeri bejelentkezést, Önnek kell végezze el a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Létrehozása egy nevezetesen tesztfelhasználó](#creating-a-namely-test-user)**  – egy partner Britta Simon, hogy a nevezetesen, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a nevezetesen alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD konfigurálása egyszeri bejelentkezést a nevezetesen, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **nevezetesen** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_samlbase.png)

3. Az a **nevezetesen tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.namely.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [nevezetesen ügyfél-támogatási csoport](https://www.namely.com/contact/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_general_400.png)

6. A a **nevezetesen konfigurációs** kattintson **konfigurálása nevezetesen** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_configure.png) 

7. Egy másik böngészőablakban, jelentkezzen be a nevezetesen vállalati hely rendszergazdaként.

8. A felső eszköztáron kattintson **vállalati**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_06.png) 

9. Kattintson a **Beállítások** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_07.png) 

10. Kattintson a **SAML**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_08.png) 

11. Az a **SAML beállítások** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Kattintson a **SAML engedélyezése**. 

    b. A a **identitási szolgáltató egyszeri bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
    
    c. A letöltött tanúsítvány megnyitása a Jegyzettömbben, másolja a tartalmat, és illessze be azt a **szolgáltató identitástanúsítvány** szövegmező.
     
    d. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/namely-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-namely-test-user"></a>Létrehozása egy nevezetesen teszt felhasználó

Ez a szakasz célja, nevezetesen Britta Simon nevű felhasználót létrehozni.

**A felhasználó, nevezetesen Britta Simon meghívta létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a nevezetesen vállalati hely rendszergazdaként.

2. A felső eszköztáron kattintson **személyek**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_10.png) 

3. Kattintson a **Directory** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_11.png) 

4. Kattintson a **adja hozzá az új személy**.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_12.png)

5. Az a **hozzáadása új személy** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. Az a **Utónév** szövegmezőhöz típus **Britta**.

    b. Az a **Vezetéknév** szövegmezőhöz típus **Simon**.

    c. Az a **E-mail** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    d. Kattintson a **Save** (Mentés) gombra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon való hozzáférés biztosítása nevezetesen által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**A következő lépésekkel, a Britta Simon hozzárendelése:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **nevezetesen**.

    ![Egyszeri bejelentkezés konfigurálása](./media/namely-tutorial/tutorial_namely_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Amikor rákattint az nevezetesen csempére a hozzáférési panelen, meg kell beolvasni automatikusan bejelentkezett a nevezetesen alkalmazás számára

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/namely-tutorial/tutorial_general_01.png
[2]: ./media/namely-tutorial/tutorial_general_02.png
[3]: ./media/namely-tutorial/tutorial_general_03.png
[4]: ./media/namely-tutorial/tutorial_general_04.png

[100]: ./media/namely-tutorial/tutorial_general_100.png

[200]: ./media/namely-tutorial/tutorial_general_200.png
[201]: ./media/namely-tutorial/tutorial_general_201.png
[202]: ./media/namely-tutorial/tutorial_general_202.png
[203]: ./media/namely-tutorial/tutorial_general_203.png

