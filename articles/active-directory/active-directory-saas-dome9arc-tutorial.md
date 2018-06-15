---
title: 'Oktatóanyag: Azure Active Directoryval integrált Dome9 ív |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Dome9 ív között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: de164f6a439fd68b4746dc96c43ff5cb0740ffe8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344146"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Oktatóanyag: Azure Active Directoryval integrált Dome9 ívvé

Ebben az oktatóanyagban elsajátíthatja Dome9 ív integrálása az Azure Active Directory (Azure AD).

Dome9 ív integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Dome9 ív szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Dome9 ív (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs Dome9 ív konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy Dome9 ív egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Dome9 ív hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-dome9-arc-from-the-gallery"></a>A gyűjteményből Dome9 ív hozzáadása
Az Azure AD integrálása a Dome9 ív konfigurálásához kell hozzáadnia Dome9 ív a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Dome9 ív hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Dome9 ív**, jelölje be **Dome9 ív** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Dome9 ívvé](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Dome9 ív.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Dome9 ív a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Dome9 ív közötti kapcsolat kapcsolatot kell létrehozni.

Dome9 Arc, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Dome9 ív tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Dome9 ív tesztfelhasználó létrehozása](#create-a-dome9-arc-test-user)**  - való egy megfelelője a Britta Simon Dome9 ív, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Dome9 ív alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Dome9 ív, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Dome9 ív** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Az a **Dome9 ív tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Dome9 ív tartomány és az URL-címek](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím: `https://secure.dome9.com/`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > A vállalat név-érték a dome9 felügyeleti portál, amelynek az ismertetése, az oktatóanyag későbbi részében fogja kiválasztani.

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Dome9 ív tartomány és az URL-címek](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Dome9 ív ügyfél-támogatási csoport](https://dome9.com/about/contact-us/) beolvasni ezeket az értékeket. 

5. A Dome9 ív alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés attb konfigurálása](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve  | Attribútum értéke | 
    | --------------- | --------------- | 
    | tagsági | User.assignedroles | 
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása attb hozzáadása](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Szerkesztés attb egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

7. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. A a **Dome9 ív konfigurációs** kattintson **Dome9 ív konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Dome9 ív konfiguráció](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. Egy másik webes böngészőablakban jelentkezzen be a Dome9 ív vállalati webhely rendszergazdaként.

11. Kattintson a **Profilbeállítások** a jobb felső sarkában, és kattintson a **Fiókbeállítások**. 

    ![Dome9 ív konfiguráció](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Navigáljon a **SSO** majd **engedélyezése**.

    ![Dome9 ív konfiguráció](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. Az egyszeri bejelentkezés konfigurációs szakaszban a következő lépésekkel:

    ![Dome9 ív konfiguráció](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Adja meg a vállalat nevét az **Fiókazonosító** szövegmező. Ez az érték van a válasz URL-cím szerepel az Azure portál URL-címe részben használhatók.

    b. Az a **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon űrlap másolta.

    c. Az a **Idp végponti URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon űrlap másolta.

    d. Nyissa meg a letöltött Base64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező.

    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-dome9-arc-test-user"></a>Dome9 ív tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Dome9 ív bejelentkezni, akkor ki kell építenie alkalmazásba. Dome9 ív támogatja a létesítést just-in-time, de az adott megfelelően működjön, a felhasználónak kell válassza ki az adott **szerepkör** és azonos hozzárendelése a felhasználóhoz.

   >[!Note] 
   >A **szerepkör** létrehozása és egyéb részletek forduljon [Dome9 ív ügyfél-támogatási csoport](https://dome9.com/about/contact-us/).

**Egy felhasználói fiókot manuálisan kiépítéséhez, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Dome9 ív vállalati webhely.

2. Kattintson a **felhasználók és szerepkörök** majd **felhasználók**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Kattintson a **felhasználó hozzáadása**.

    ![Alkalmazott hozzáadása](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. Az a **felhasználó létrehozása** területen tegye a következőket:
    
    ![Alkalmazott hozzáadása](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. Az a **E-mail** szövegmezőben, az e-mailt a felhasználó típusát, például Brittasimon@contoso.com.

    b. Az a **Keresztnév** szövegmezőhöz Britta például a felhasználó első nevét.

    c. Az a **Vezetéknév** szövegmezőhöz típus Simon például a felhasználó vezetékneve.

    d. Ellenőrizze **egyszeri Bejelentkezéses felhasználói** , **a**.

    e. Kattintson a **létrehozása**.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Dome9 ív Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Dome9 ív, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Dome9 ív**.

    ![Az alkalmazások listáját a Dome9 ív hivatkozás](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Dome9 ív csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Dome9 ív alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

