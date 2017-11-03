---
title: "Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Salesforce védőfal között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Oktatóanyag: Azure Active Directoryval integrált Salesforce védőfal

Ebben az oktatóanyagban elsajátíthatja Salesforce védőfal integrálása az Azure Active Directory (Azure AD).

Salesforce védőfal integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Salesforce védőfal hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Salesforce védőfal (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce védőfal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Salesforce védőfal egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Salesforce védőfal hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce védőfal hozzáadása a gyűjteményből
Az Azure AD integrálása a Salesforce védőfal konfigurálásához kell hozzáadnia Salesforce védőfal a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Vegye fel a Salesforce védőfal a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Salesforce védőfal**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. Az eredmények panelen válassza ki a **Salesforce védőfal**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és a vizsgálat az Azure AD egyszeri bejelentkezést a Salesforce védőfal "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Salesforce védőfal tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Salesforce védőfal közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a Salesforce védőfal.

Az Azure AD egyszeri bejelentkezést a Salesforce védőfal tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Salesforce védőfal tesztfelhasználó létrehozása](#creating-a-salesforce-sandbox-test-user)**  - való Britta Simon egy megfelelője a Salesforce védőfal, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Salesforce védőfal alkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Salesforce védőfal** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Az a **Salesforce védőfal tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     Az a **bejelentkezési URL-cím** szövegmező, írja be az értéket a következő minta használatával:`https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > Ez az érték nem a valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Salesforce védőfal ügyfél-támogatási csoport](https://help.salesforce.com/support) lekérni ezt az értéket.


4. Ha már beállította egyszeri bejelentkezés Salesforce védőfal egy másik példány a könyvtárban, majd konfigurálnia kell a **azonosító** ugyanazt az értéket, hogy a **bejelentkezési URL-cím**. 
    
    >[!Note]
    >A **azonosító** mező található ellenőrzésével a **megjelenítése speciális beállítások** jelölőnégyzetet a **alkalmazás URL-cím konfigurálása** lap a párbeszédpanel 


5. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. A a **Salesforce védőfal konfigurációs** kattintson **konfigurálása Salesforce védőfal** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Új lap megnyitása a böngészőben, és jelentkezzen be a Salesforce védőfal rendszergazdai fiókjával.

9. Kattintson a felső menüben **telepítő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. A bal oldali navigációs ablaktábláján kattintson **biztonsági vezérlők**, és kattintson a **egyszeri bejelentkezési beállítások**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. Egyszeri bejelentkezés beállítások csoportjában hajtsa végre a következő lépéseket: ![konfigurálása egyszeri bejelentkezéshez](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Válassza ki **SAML engedélyezett**. 

     b.  Kattintson az **Új** lehetőségre.

12. A SAML egyszeri bejelentkezés beállítások szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a.In a szövegmezőben adja meg a konfiguráció nevét (pl.: *SPSSOWAAD\_teszt*). 

    b. Beillesztés **SMAL Entitásazonosító** be értéket a **kibocsátó** szövegmező.

    c. Az a **entitásazonosító** szövegmezőhöz típus **https://test.salesforce.com** Salesforce védőfal elsősorban a könyvtárhoz hozzáadandó esetén. Ha már van egy példánya Salesforce védőfal, majd a a **Entitásazonosító** írja be a **URL-cím bejelentkezési**, amely a következő formátumban kell lennie:`http://company.my.salesforce.com`  
 
    d. Kattintson a **Tallózás** a letöltött tanúsítvány feltöltése.  

    e. Mint **SAML identitástípus**, jelölje be **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**.
 
    f. Mint **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentifier elemében van**.

    g. Beillesztés **egyszeri bejelentkezési URL-címe** azokat a **Identity Provider bejelentkezési URL-cím** szövegmező. 

    h. SFDC nem támogatja az SAML jelentkezzen ki.  A probléma megoldásához, illessze be a "https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0" be azt a **Identity Provider kijelentkezési URL-cím** szövegmező.

    i. Mint **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP POST**. 

    j. Kattintson a **Save** (Mentés) gombra.

### <a name="enable-your-domain"></a>A tartomány
Jelen szakaszban feltételezzük, hogy már létrehozta a tartományhoz.  További információkért lásd: [meghatározása saját tartomány neve](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Ahhoz, hogy a tartomány, hajtsa végre a következő lépéseket:**

1. A bal oldali navigációs ablaktáblán kattintson **tartományok**, és kattintson a **saját tartomány.**
   
     ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Győződjön meg arról, hogy a tartomány megfelelően van konfigurálva. 

2. Az a **bejelentkezési oldal beállításainak** területen kattintson **szerkesztése**, majd, mint **hitelesítési szolgáltatás**, válassza ki a nevét a SAML egyszeri bejelentkezés beállítása a fenti szakaszban leírt, és végül kattintson **mentése**.
   
   ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

Amint egy tartományhoz, konfigurálva van, a felhasználók használjon bejelentkezni a Salesforce védőfal tartomány URL-CÍMÉT.  

Ahhoz, hogy az URL-cím értékét, kattintson az előző szakaszban létrehozott SSO profilra.    

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Megjelenítendő felhasználót tartalmazó listát Ugrás **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>A Salesforce védőfal tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon nevű Salesforce védőfal jön létre. Salesforce védőfal támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.
Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a Salesforce védőfal, egy új létrejön a Salesforce védőfal elérésére tett kísérlet során.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon hozzáférést biztosít a Salesforce védőfal által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Salesforce védőfal, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Salesforce védőfal**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Az SSO-beállítások tesztelésére, nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további tudnivalókért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

