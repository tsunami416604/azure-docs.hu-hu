---
title: 'Oktatóanyag: Azure Active Directoryval integrált CloudPassage |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és CloudPassage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 6377eeb06705528f567aaf6f29461db0023c27b4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223127"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Oktatóanyag: Azure Active Directoryval integrált CloudPassage

Ebben az oktatóanyagban elsajátíthatja CloudPassage integrálása az Azure Active Directory (Azure AD).

CloudPassage integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a CloudPassage hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett CloudPassage (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs CloudPassage, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy CloudPassage egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből CloudPassage hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-cloudpassage-from-the-gallery"></a>A gyűjteményből CloudPassage hozzáadása
Az Azure AD integrálása a CloudPassage konfigurálásához kell hozzáadnia CloudPassage a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből CloudPassage hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **CloudPassage**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. Az eredmények panelen válassza ki a **CloudPassage**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján CloudPassage

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó CloudPassage a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a CloudPassage közötti kapcsolat kapcsolatot kell létrehozni.

CloudPassage, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a CloudPassage tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[CloudPassage tesztfelhasználó létrehozása](#creating-a-cloudpassage-test-user)**  - való Britta Simon valami CloudPassage, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az CloudPassage alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés CloudPassage, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **CloudPassage** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. Az a **CloudPassage tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-cím: `https://portal.cloudpassage.com/saml/consume/accountid`. Kaphat a érték ehhez az attribútumhoz kattintva **egyszeri bejelentkezés beállítása dokumentáció** a a **egyszeri bejelentkezési beállítások** a CloudPassage portál szakaszban.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN, és a bejelentkezési URL-cím. Ügyfél [CloudPassage ügyfél-támogatási csoport](https://www.cloudpassage.com/company/contact/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. A CloudPassage alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Az alábbi képernyőfelvételen látható egy példa a.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:

    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | Utónév |User.givenName |
    | Vezetéknév |User.surname |
    | e-mailben |user.mail |
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_general_400.png)
    
8. A a **CloudPassage konfigurációs** kattintson **konfigurálása CloudPassage** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. Egy másik böngészőablakban bejelentkezés webhelyekhez CloudPassage vállalati rendszergazdaként.

10. Kattintson a felső menüben **beállítások**, és kattintson a **helyfelügyelet**. 
   
    ![Egyszeri bejelentkezés konfigurálása][12]

11. Kattintson a **hitelesítési beállítások** fülre. 
   
    ![Egyszeri bejelentkezés konfigurálása][13]

12. Az a **egyszeri bejelentkezési beállítások** területen tegye a következőket: 
   
    ![Egyszeri bejelentkezés konfigurálása][14]

    a. Válassza ki **engedélyezése egyetlen sign-on(SSO) (egyszeri bejelentkezés beállítása dokumentáció)** jelölőnégyzetet.
    
    b. Beillesztés **SAML Entitásazonosító** azokat a **SAML kiállítójának URL-címe** szövegmező.
  
    c. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** azokat a **SAML végponti URL-cím** szövegmező.
  
    d. Beillesztés **Sign-Out URL-cím** azokat a **kijelentkezési kezdőlapja** szövegmező.
  
    e. A letöltött tanúsítvány megnyitása a Jegyzettömbben, a letöltött tanúsítvány tartalmának másolása a vágólapra és illessze be azt a **x 509 tanúsítvány** szövegmező.
  
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cloudpassage-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-cloudpassage-test-user"></a>CloudPassage tesztfelhasználó létrehozása

Ez a szakasz célja CloudPassage Britta Simon nevű felhasználót létrehozni.

**A felhasználó Britta Simon meghívta CloudPassage létrehozásához hajtsa végre az alábbi lépéseket:**

1. Bejelentkezés a **CloudPassage** vállalati hely rendszergazdaként. 

2. A felső eszköztáron kattintson **beállítások**, és kattintson a **helyfelügyelet**. 
   
   ![CloudPassage tesztfelhasználó létrehozása][22] 

3. Kattintson a **felhasználók** fülre, majd **új felhasználó hozzáadása**. 
   
   ![CloudPassage tesztfelhasználó létrehozása][23]

4. Az a **új felhasználó hozzáadása** területen tegye a következőket: 
   
   ![CloudPassage tesztfelhasználó létrehozása][24]
    
    a. Az a **Keresztnév** szövegmező, írja be a Britta. 
  
    b. Az a **Vezetéknév** szövegmezőhöz Simon írja be.
  
    c. A a **felhasználónév** szövegmezőhöz a **E-mail** szövegmező és a **írja be újra E-mail** szövegmező, írja be a Britta tartozó felhasználónevet az Azure ad-ben.
  
    d. Mint **hozzáférési típus**, jelölje be **Halo Portal hozzáférés engedélyezése**.
  
    e. Kattintson a **Hozzáadás** parancsra.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés CloudPassage Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése CloudPassage, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **CloudPassage**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen CloudPassage csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az CloudPassage alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/cloudpassage-tutorial/tutorial_general_203.png

