---
title: 'Oktatóanyag: Azure Active Directoryval integrált PolicyStat |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és PolicyStat között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: d4ee17ef360012b0996d10155308a0732ff15f6c
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923458"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Oktatóanyag: Azure Active Directoryval integrált PolicyStat

Ebben az oktatóanyagban elsajátíthatja PolicyStat integrálása az Azure Active Directory (Azure AD).

PolicyStat integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a PolicyStat hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett PolicyStat (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs PolicyStat, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy PolicyStat egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből PolicyStat hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-policystat-from-the-gallery"></a>A gyűjteményből PolicyStat hozzáadása
Az Azure AD integrálása a PolicyStat konfigurálásához kell hozzáadnia PolicyStat a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből PolicyStat hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **PolicyStat**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/tutorial_policystat_search.png)

5. Az eredmények panelen válassza ki a **PolicyStat**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján PolicyStat.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó PolicyStat a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a PolicyStat közötti kapcsolat kapcsolatot kell létrehozni.

PolicyStat, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a PolicyStat tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[PolicyStat tesztfelhasználó létrehozása](#creating-a-policystat-test-user)**  - való Britta Simon valami PolicyStat, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az PolicyStat alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés PolicyStat, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **PolicyStat** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

3. Az a **PolicyStat tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.policystat.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [PolicyStat ügyfél-támogatási csoport](http://www.policystat.com/support/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

5. Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez PolicyStat fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.

    A PolicyStat alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban, amelyhez egyéni attribútum leképezései hozzáadása a **SAML-jogkivonat attribútumok** konfigurációs.  

     Az alábbi képernyőfelvételen látható példa erre.

     ![Attribútumok](./media/policystat-tutorial/tutorial_policystat_attribute.png "attribútumok")

6. A kötelező attribútum-leképezésekhez hozzáadásához hajtsa végre az alábbi lépéseket:

    | Attribútum neve    |   Attribútum értéke |
    |------------------- | -------------------- |
    | egyedi azonosítója | ExtractMailPrefix([mail]) |
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. Az a **attribútumnév** szövegmezőhöz típus **uid**.

    c. Az a **attribútumérték** szövegmező, jelölje be **ExtractMailPrefix()**.    
   
    d. Az a **Mail** listáról válassza ki **User.mail**.
    
    e. Kattintson a **Ok**

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_general_400.png)

8. Egy másik webes böngészőablakban jelentkezzen be a PolicyStat vállalati webhely rendszergazdaként.

9. Kattintson a **Admin** fülre, majd **egyszeri bejelentkezés konfigurációs** bal oldali navigációs ablaktáblán.
   
    ![Rendszergazda menü](./media/policystat-tutorial/ic808633.png "Rendszergazda menü")

10. Az a **telepítő** szakaszban jelölje be **engedélyezése egyszeri bejelentkezéshez integrációs**.
   
    ![Az egyszeri bejelentkezés konfigurációs](./media/policystat-tutorial/ic808634.png "az egyszeri bejelentkezés konfigurálása")

11. Kattintson a **attribútumok konfigurálása**, majd a a **attribútumok konfigurálása** területen tegye a következőket:
   
    ![Az egyszeri bejelentkezés konfigurációs](./media/policystat-tutorial/ic808635.png "az egyszeri bejelentkezés konfigurálása")
   
    a. Az a **felhasználónév attribútum** szövegmezőhöz típus **uid**.

    b. Az a **Keresztnév attribútum** szövegmezőhöz típus **Keresztnév** felhasználó **Britta**.

    c. Az a **utolsó Name attribútum** szövegmezőhöz típus **Vezetéknév** felhasználó **Simon**.

    d. Az a **E-mail attribútum** szövegmezőhöz típus **emailaddress** felhasználó **BrittaSimon@contoso.com**.

    e. Kattintson a **módosítások mentése**.

12. Kattintson a **a kiállító terjesztési hely metaadatok**, majd a a **a kiállító terjesztési hely metaadatok** területen az alábbi lépésekkel:
   
    ![Az egyszeri bejelentkezés konfigurációs](./media/policystat-tutorial/ic808636.png "az egyszeri bejelentkezés konfigurálása")
   
    a. Nyissa meg a letöltött metaadatfájl, másolja a tartalmat, és illessze be azt a **a Identity Provider metaadatok** szövegmező.

    b. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/policystat-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-policystat-test-user"></a>PolicyStat tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók PolicyStat bejelentkezni, akkor ki kell építenie PolicyStat be.  

PolicyStat csak az idő a felhasználók átadása támogatja. Ez azt jelenti, hogy nem kell a felhasználók manuális hozzáadása PolicyStat. A felhasználók automatikusan az első bejelentkezés SSO keresztül a rendszer hozzáadják.

>[!NOTE]
>Bármely más PolicyStat felhasználói fiók létrehozása eszközök vagy PolicyStat kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PolicyStat Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése PolicyStat, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **PolicyStat**.

    ![Egyszeri bejelentkezés konfigurálása](./media/policystat-tutorial/tutorial_policystat_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen PolicyStat csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az PolicyStat alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

