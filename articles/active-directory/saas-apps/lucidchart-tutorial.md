---
title: 'Oktatóanyag: Azure Active Directoryval integrált Lucidchart |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Lucidchart között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 5bf8d65834dd41646730dbcf2b8d2bcc441993e0
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35925381"
---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Oktatóanyag: Azure Active Directoryval integrált Lucidchart

Ebben az oktatóanyagban elsajátíthatja Lucidchart integrálása az Azure Active Directory (Azure AD).

Lucidchart integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Lucidchart hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Lucidchart (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Lucidchart, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Lucidchart egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Lucidchart hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-lucidchart-from-the-gallery"></a>A gyűjteményből Lucidchart hozzáadása
Az Azure AD integrálása a Lucidchart konfigurálásához kell hozzáadnia Lucidchart a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Lucidchart hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Lucidchart**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/tutorial_lucidchart_search.png)

5. Az eredmények panelen válassza ki a **Lucidchart**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/tutorial_lucidchart_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Lucidchart.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Lucidchart a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Lucidchart közötti kapcsolat kapcsolatot kell létrehozni.

Lucidchart, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Lucidchart tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Lucidchart tesztfelhasználó létrehozása](#creating-a-lucidchart-test-user)**  - való Britta Simon valami Lucidchart, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Lucidchart alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Lucidchart, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Lucidchart** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/lucidchart-tutorial/tutorial_lucidchart_samlbase.png)

3. Az a **Lucidchart tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/lucidchart-tutorial/tutorial_lucidchart_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://chart2.office.lucidchart.com/saml/sso/azure`

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/lucidchart-tutorial/tutorial_lucidchart_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/lucidchart-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban jelentkezzen be a Lucidchart vállalati webhely rendszergazdaként.

7. Kattintson a felső menüben **Team**.
   
    ![Team](./media/lucidchart-tutorial/ic791190.png "csapata")

8. Kattintson a **alkalmazások \> SAML kezelése**.
   
    ![SAML kezelése](./media/lucidchart-tutorial/ic791191.png "SAML kezelése")

9. Az a **SAML-alapú hitelesítési beállítások** párbeszédpanel lapon, a következő lépésekkel:
   
    a. Válassza ki **SAML-alapú hitelesítés engedélyezéséhez**, és kattintson a **nem kötelező**.

    ![SAML-alapú hitelesítési beállítások](./media/lucidchart-tutorial/ic791192.png "SAML-alapú hitelesítési beállítások")
 
    b. Az a **tartomány** szövegmező, írja be a tartomány, és kattintson **módosítás tanúsítvány**.

    ![Tanúsítvány módosítása](./media/lucidchart-tutorial/ic791193.png "tanúsítványának módosítása")
 
    c. Nyissa meg a letöltött metaadatfájl, másolja a tartalmat, és illessze be azt a **metaadatok feltöltése** szövegmező.

    ![Töltse fel a metaadatok](./media/lucidchart-tutorial/ic791194.png "metaadatok feltöltése")
 
    d. Válassza ki **az új felhasználók automatikus hozzáadása a csoporthoz**, és kattintson a **módosítások mentése**.

    ![Módosítások mentése](./media/lucidchart-tutorial/ic791195.png "módosítások mentése")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lucidchart-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lucidchart-test-user"></a>Lucidchart tesztfelhasználó létrehozása

Nincs művelet elem ahhoz, hogy a felhasználó Lucidchart történő konfigurálásához.  Ha egy hozzárendelt felhasználó megpróbál bejelentkezni, a hozzáférési panelen Lucidchart, Lucidchart ellenőrzi, hogy a felhasználó létezik-e.  

Nincs még nincs felhasználói fiók érhető el, ha a Lucidchart automatikusan létrehozni.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Lucidchart Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Lucidchart, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Lucidchart**.

    ![Egyszeri bejelentkezés konfigurálása](./media/lucidchart-tutorial/tutorial_lucidchart_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Lucidchart csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Lucidchart alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lucidchart-tutorial/tutorial_general_01.png
[2]: ./media/lucidchart-tutorial/tutorial_general_02.png
[3]: ./media/lucidchart-tutorial/tutorial_general_03.png
[4]: ./media/lucidchart-tutorial/tutorial_general_04.png

[100]: ./media/lucidchart-tutorial/tutorial_general_100.png

[200]: ./media/lucidchart-tutorial/tutorial_general_200.png
[201]: ./media/lucidchart-tutorial/tutorial_general_201.png
[202]: ./media/lucidchart-tutorial/tutorial_general_202.png
[203]: ./media/lucidchart-tutorial/tutorial_general_203.png

