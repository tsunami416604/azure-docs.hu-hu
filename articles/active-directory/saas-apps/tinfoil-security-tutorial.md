---
title: 'Oktatóanyag: Azure Active Directory integrációja a TINFOIL SECURITY |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a TINFOIL SECURITY között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 3cd9d0c24ecb7dda01a4d36c013ebd4f6a484ddc
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923735"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Oktatóanyag: A TINFOIL SECURITY Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a TINFOIL SECURITY integrálása az Azure Active Directory (Azure AD).

A TINFOIL SECURITY integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a TINFOIL SECURITY Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a TINFOIL SECURITY (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a TINFOIL SECURITY, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A TINFOIL SECURITY egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A TINFOIL SECURITY hozzáadása a gyűjteményből
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-tinfoil-security-from-the-gallery"></a>A TINFOIL SECURITY hozzáadása a gyűjteményből
Az Azure AD integrálása a TINFOIL SECURITY konfigurálásához kell hozzáadnia a TINFOIL SECURITY a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A TINFOIL SECURITY hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **TINFOIL SECURITY**, jelölje be **TINFOIL SECURITY** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A TINFOIL SECURITY gyűjteményből](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a TINFOIL SECURITY "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a TINFOIL SECURITY tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a TINFOIL SECURITY közötti kapcsolat kapcsolatot kell létrehozni.

A TINFOIL SECURITY, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TINFOIL SECURITY tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A TINFOIL SECURITY tesztfelhasználó létrehozása](#create-a-tinfoil-security-test-user)**  - való Britta Simon egy megfelelője a TINFOIL SECURITY, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a TINFOIL SECURITY alkalmazásban egyszeri bejelentkezés konfigurálása.

**A TINFOIL SECURITY konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **TINFOIL SECURITY** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![SAML-alapú bejelentkezés](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. Az a **TINFOIL SECURITY tartomány és az URL-címek** szakaszban, a felhasználó nem rendelkezik, az alkalmazás már előre integrálva van az Azure-ral bármely lépések végrehajtásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** érték.

    ![SAML-aláíró tanúsítványa szakasz](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. A kötelező attribútum-leképezésekhez hozzáadásához hajtsa végre az alábbi lépéseket:
    
    ![Attribútumok](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "attribútumok")
    
    | Attribútum neve    |   Attribútum értéke |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Kattintson a **hozzáadása a felhasználói attribútum**.
    
    ![Hozzáadás attribútum](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "attribútumok")
    
    ![Hozzáadás attribútum](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "attribútumok")
    
    b. Az a **attribútumnév** szövegmezőhöz típus **accountid**.
    
    c. Az a **attribútumérték** szövegmező, illessze be a Fiókazonosítót értéket, amelyet később az oktatóanyag fog kapni.
    
    d. Kattintson az **OK** gombra.    

6. Kattintson a **mentése** gombra.

    ![Mentés gombja](./media/tinfoil-security-tutorial/tutorial_general_400.png)

7. A a **TINFOIL SECURITY Configuration** kattintson **konfigurálása a TINFOIL SECURITY** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![A TINFOIL SECURITY Configuration](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a TINFOIL SECURITY vállalati webhely rendszergazdaként.

9. A felső eszköztáron kattintson **saját fiók**.
   
    ![Irányítópult](./media/tinfoil-security-tutorial/ic798971.png "irányítópult")

10. Kattintson a **biztonsági**.
   
    ![Biztonsági](./media/tinfoil-security-tutorial/ic798972.png "biztonsági")

11. Az a **egyszeri bejelentkezés** konfigurációs lapján tegye a következőket:
   
    ![Egyszeri bejelentkezés](./media/tinfoil-security-tutorial/ic798973.png "egyszeri bejelentkezés")
   
    a. Válassza ki **SAML engedélyezése**.
   
    b. Kattintson a **kézi konfigurálás**.
   
    c. A **SAML Post URL** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** amely másolta Azure-portálon
   
    d. A **SAML-alapú tanúsítvány-ujjlenyomat** szövegmezőhöz illessze be az értékét **ujjlenyomat** , amely a másolt **SAML-aláíró tanúsítványa** szakasz.
  
    e. Másolás **a Fiókazonosító** értékét, és illessze be az értéket **attribútumérték** szövegmező alatt **attribútum hozzáadása** szakaszban az Azure portálon.
   
    f. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok -> minden felhasználó ](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Felhasználó](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-tinfoil-security-test-user"></a>A TINFOIL SECURITY tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a TINFOIL SECURITY, akkor ki kell építenie a TINFOIL SECURITY. A TINFOIL SECURITY esetén kézi tevékenység.

**Ahhoz, hogy egy felhasználó kiépített, hajtsa végre a következő lépéseket:**

1. Ha a felhasználó a vállalati fiók része, akkor [a TINFOIL SECURITY támogatási csoportjához](https://www.tinfoilsecurity.com/contact) létrehozott felhasználói fiók eléréséhez.

2. Ha a felhasználó egy rendszeres TINFOIL SECURITY Szolgáltatottszoftver-felhasználó, majd a felhasználó adhat hozzá a felhasználói helyek bármelyikének közreműködő. Ezzel elindítja a folyamat a TINFOIL SECURITY új felhasználói fiók létrehozása a megadott e-mail felkérést küldeni.

> [!NOTE]
> A TINFOIL SECURITY felhasználói fiók létrehozása eszközök vagy a TINFOIL SECURITY által nyújtott API-k segítségével kiépíteni az Azure AD felhasználói fiókokat.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít a TINFOIL SECURITY által használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**A TINFOIL SECURITY Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TINFOIL SECURITY**.

    ![a TINFOIL SECURITY kiválasztása](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen a TINFOIL SECURITY csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a TINFOIL SECURITY alkalmazásba. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

