---
title: 'Oktatóanyag: Azure Active Directoryval integrált RunMyProcess |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és RunMyProcess között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2533a09f4bfa3000ba3d3300978aefbaec742f09
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926124"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Oktatóanyag: Azure Active Directoryval integrált RunMyProcess

Ebben az oktatóanyagban elsajátíthatja RunMyProcess integrálása az Azure Active Directory (Azure AD).

RunMyProcess integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a RunMyProcess hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett RunMyProcess (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs RunMyProcess, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy RunMyProcess egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat:[próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből RunMyProcess hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-runmyprocess-from-the-gallery"></a>A gyűjteményből RunMyProcess hozzáadása
Az Azure AD integrálása a RunMyProcess konfigurálásához kell hozzáadnia RunMyProcess a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből RunMyProcess hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **RunMyProcess**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

5. Az eredmények panelen válassza ki a **RunMyProcess**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján RunMyProcess.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó RunMyProcess a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a RunMyProcess közötti kapcsolat kapcsolatot kell létrehozni.

RunMyProcess, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a RunMyProcess tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[RunMyProcess tesztfelhasználó létrehozása](#creating-a-runmyprocess-test-user)**  - való Britta Simon valami RunMyProcess, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az RunMyProcess alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés RunMyProcess, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **RunMyProcess** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

3. Az a **RunMyProcess tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > Az érték nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [RunMyProcess ügyfél-támogatási csoport](mailto:support@runmyprocess.com) az értéket be kell olvasni. 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_general_400.png)

6. A a **RunMyProcess konfigurációs** kattintson **konfigurálása RunMyProcess** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

7. Egy másik webes böngészőablakban bejelentkezés a RunMyProcess bérlő rendszergazdaként.

8. A bal oldali navigációs panelen kattintson a **fiók** válassza **konfigurációs**.
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

9. Ugrás a **hitelesítési módszer** szakaszt, és hajtsa végre a következő lépések:
   
    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Mint **metódus**, jelölje be **SSO Samlv2**. 

    b. Az a **SSO átirányítási** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    c. Az a **kijelentkezési átirányítási** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    d. Az a **név Azonosítóformátumának** szövegmező, írja be az értéket a **azonosító formátuma** , **urn: oasis: nevek: tc: SAML:1.1:nameid-formátum: emailAddress**.

    e. A letöltött fájlt másolja és illessze be azt a **tanúsítvány** szövegmező. 
 
    f. Kattintson a **mentése** ikonra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-runmyprocess-test-user"></a>RunMyProcess tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók RunMyProcess bejelentkezni, akkor ki kell építenie a RunMyProcess. RunMyProcess, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a RunMyProcess vállalati webhely.

2. Kattintson a **fiók** válassza **felhasználók** a bal oldali navigációs panelen, majd kattintson a **új felhasználói**.
   
    ![Új felhasználó](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "új felhasználó")

3. Az a **felhasználói beállítások** területen tegye a következőket:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "profil") 
  
    a. Típus a **neve** és **E-mail** egy érvényes Azure AD-fiókot szeretné azokat a kapcsolódó szövegmezők kiépítéséhez. 

    b. Válasszon egy **IDE nyelvi**, **nyelvi**, és **profil**. 

    c. Válassza ki **fiók létrehozása e-mail küldése me**. 

    d. Kattintson a **Save** (Mentés) gombra.
   
    >[!NOTE]
    >Bármely más RunMyProcess felhasználói fiók létrehozása eszközök vagy API-k által biztosított RunMyProcess kiépítését Azure Active Directory felhasználói fiókokat. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés RunMyProcess Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése RunMyProcess, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **RunMyProcess**.

    ![Egyszeri bejelentkezés konfigurálása](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen RunMyProcess csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az RunMyProcess alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

