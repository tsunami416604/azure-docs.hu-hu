---
title: 'Oktatóanyag: Azure Active Directoryval integrált ThousandEyes |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ThousandEyes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: a08023e4853d999e5e339f6fd54fa9d968c4fec1
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923848"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Oktatóanyag: Azure Active Directoryval integrált ThousandEyes

Ebben az oktatóanyagban elsajátíthatja ThousandEyes integrálása az Azure Active Directory (Azure AD).

ThousandEyes integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ThousandEyes hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett ThousandEyes (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs ThousandEyes, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ThousandEyes egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ThousandEyes hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-thousandeyes-from-the-gallery"></a>A gyűjteményből ThousandEyes hozzáadása
Az Azure AD integrálása a ThousandEyes konfigurálásához kell hozzáadnia ThousandEyes a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből ThousandEyes hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **ThousandEyes**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. Az eredmények panelen válassza ki a **ThousandEyes**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján ThousandEyes.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ThousandEyes a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a ThousandEyes közötti kapcsolat kapcsolatot kell létrehozni.

ThousandEyes, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a ThousandEyes tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[ThousandEyes tesztfelhasználó létrehozása](#creating-a-thousandeyes-test-user)**  - való Britta Simon valami ThousandEyes, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ThousandEyes alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés ThousandEyes, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ThousandEyes** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Az a **ThousandEyes tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://app.thousandeyes.com/login/sso`

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_general_400.png)

6. A a **ThousandEyes konfigurációs** kattintson **konfigurálása ThousandEyes** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a **ThousandEyes** vállalati hely rendszergazdaként.

8. Kattintson a felső menüben **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/ic790066.png "beállítások")

9. Kattintson a **fiók**

    ![Fiók](./media/thousandeyes-tutorial/ic790067.png "fiók")

10. Kattintson a **biztonsági és hitelesítési** fülre.

    ![Biztonsági és hitelesítési](./media/thousandeyes-tutorial/ic790068.png "biztonsági és hitelesítési")

11. Az a **telepítő egyszeri bejelentkezés** területen tegye a következőket:

    ![Egyszeri bejelentkezés beállítása](./media/thousandeyes-tutorial/ic790069.png "egyszeri bejelentkezés beállítása")

    a. Válassza ki **egyszeri bejelentkezés engedélyezése**.

    b. A **bejelentkezési lap URL-címet** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    c. A **kijelentkezési URL-címe** szövegmezőhöz Beillesztés **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    d. **Identity Provider kibocsátó** szövegmezőhöz Beillesztés **SAML Entitásazonosító**, amely az Azure-portálon másolta.

    e. A **ellenőrző tanúsítvány**, kattintson a **fájl kiválasztása**, majd töltse fel az Azure-portálról letöltött tanúsítvány.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-thousandeyes-test-user"></a>ThousandEyes tesztfelhasználó létrehozása

Ez a szakasz célja ThousandEyes Britta Simon nevű felhasználót létrehozni. ThousandEyes támogatja az automatikus a felhasználók átadása, amely alapértelmezés szerint van engedélyezve. További részletek találhatók [Itt](thousandeyes-provisioning-tutorial.md) automatikus felhasználólétesítés konfigurálásához.

**Ha szeretne létrehozni a felhasználót manuálisan, akkor hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a ThousandEyes vállalati webhely rendszergazdaként.

2. Kattintson a **beállítások**.

    ![Beállítások](./media/thousandeyes-tutorial/IC790066.png "beállítások")

3. Kattintson a **fiók**.

    ![Fiók](./media/thousandeyes-tutorial/IC790067.png "fiók")

4. Kattintson a **fiókok és a felhasználók** fülre.

    ![Fiókok és a felhasználók](./media/thousandeyes-tutorial/IC790073.png "fiókok és a felhasználók")

5. Az a **felhasználók hozzáadása és fiókok** területen tegye a következőket:

    ![Felhasználói fiókok hozzáadása](./media/thousandeyes-tutorial/IC790074.png "felhasználói fiókok hozzáadása")

    a. A **neve** szövegmező, a felhasználó típusát a nevét, például **Britta Simon**.

    b. A **E-mail** szövegmezőben, az e-mailt a felhasználó típusát, például **brittasimon@contoso.com**.

    b. Kattintson a **adja hozzá az új felhasználói fiókhoz**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt hivatkozással erősítse meg, és aktiválja a fiókot.

> [!NOTE]
> Bármely más ThousandEyes felhasználói fiók létrehozása eszközök vagy API-k által biztosított ThousandEyes kiépítését Azure Active Directory felhasználói fiókokat.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ThousandEyes Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése ThousandEyes, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ThousandEyes**.

    ![Egyszeri bejelentkezés konfigurálása](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen ThousandEyes csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az ThousandEyes alkalmazására.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)
* [A felhasználók átadása konfigurálása](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
