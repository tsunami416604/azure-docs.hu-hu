---
title: 'Oktatóanyag: Azure Active Directoryval integrált vászonra Lms |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a vászonra LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: jeedes
ms.openlocfilehash: 56fe4679448fec1db67f826ff70cf8768699e507
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34342800"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Oktatóanyag: Azure Active Directoryval integrált vászonra LMS

Ebben az oktatóanyagban elsajátíthatja vászonra integrálása az Azure Active Directory (Azure AD).

Vászonra integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a vászonra
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett vászon (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a vásznon, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A vászon egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Felvétele a vászonra a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-canvas-from-the-gallery"></a>Felvétele a vászonra a gyűjteményből
Az Azure AD integrálása a vászon konfigurálásához kell hozzáadnia vászonra a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a vászonra a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **vászonra**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_search.png)

5. Az eredmények panelen válassza ki a **vászonra**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a vászon "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a vászonra a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a vászon közötti kapcsolat kapcsolatot kell létrehozni.

A vásznon, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a vászon tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Vászonra tesztfelhasználó létrehozása](#creating-a-canvas-test-user)**  - való Britta Simon egy megfelelője a vásznon, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a vászonra alkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a vásznon, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **vászonra** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_samlbase.png)

3. Az a **vászonra tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenant-name>.instructure.com`

    b. Az a **azonosító** szövegmező, írja be az értéket a következő minta használatával: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [vászonra ügyfél-támogatási csoport](https://community.canvaslms.com/community/help) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_400.png)

6. Az a **vászonra konfigurációs** területen kattintson **konfigurálása vászonra** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **jelszó URL-cím módosítása, Sign-Out URL-címe, SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_configure.png) 
 
7. Egy másik webes böngészőablakban jelentkezzen be a vászonra vállalati webhely rendszergazdaként.

8. Ugrás a **tanfolyamokat \> fiókok felügyelt \> Microsoft**.
   
    ![Vászonra](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "vászonra")

9. Jelölje ki a bal oldali navigációs ablaktáblán, **hitelesítési**, és kattintson a **új SAML-Config hozzáadása**.
   
    ![Hitelesítési](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "hitelesítés")

10. Az aktuális integrációs oldalon hajtsa végre az alábbi lépéseket:
   
    ![Aktuális integrációs](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "aktuális integráció")

    a. A **IdP Entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    b. A **URL-cím napló** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. A **napló kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    d. A **módosítás jelszó hivatkozásra** szövegmezőhöz illessze be az értékét **jelszó URL-cím módosítása** ami Azure-portálon másolta. 

    e. A **tanúsítvány-ujjlenyomat** szövegmező, illessze be a **ujjlenyomat** érték tanúsítvány, amely az Azure-portálon másolta.      
        
    f. Az a **bejelentkezési attribútum** listáról válassza ki **NameID**.

    g. Az a **azonosító formátuma** listáról válassza ki **emailAddress**.

    h. Kattintson a **hitelesítési beállításainak mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-canvas-lms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-canvas-test-user"></a>Vászonra tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a vászonra, akkor ki kell építenie a vászonra.

Esetén a vásznon a felhasználók átadása kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **vászonra** bérlő.

2. Ugrás a **tanfolyamokat \> fiókok felügyelt \> Microsoft**.
   
   ![Vászonra](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "vászonra")

3. Kattintson a **felhasználók**.
   
   ![Felhasználók](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "felhasználók")

4. Kattintson a **új felhasználó hozzáadása**.
   
   ![Felhasználók](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "felhasználók")

5. A hozzáadása egy új felhasználó párbeszédpanel lap hajtsa végre a következő lépéseket:
   
   ![Felhasználó hozzáadása](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "felhasználó hozzáadása")
   
   a. Az a **teljes nevét** szövegmező, írja be például a felhasználó nevét **BrittaSimon**.

   b. Az a **E-mail** szövegmező, adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

   c. Az a **bejelentkezési** szövegmező, írja be a felhasználó az Azure AD e-mail címét például **brittasimon@contoso.com**.

   d. Válassza ki **E-mail a felhasználó a fiók létrehozása**.

   e. Kattintson a **felhasználó hozzáadása**.

>[!NOTE]
>Bármely más vászonra felhasználói fiók létrehozása eszközök, vagy rendelkezés AAD felhasználói fiókokhoz vászonra által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó hozzáférés biztosítása a vászonra.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése a vásznon, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **vászonra**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-canvas-lms-tutorial/tutorial_canvaslms_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A hozzáférési panelen a vászon csempére kattintva, meg kell beolvasni automatikusan bejelentkezett a vászon alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-canvas-lms-tutorial/tutorial_general_203.png

