---
title: 'Oktatóanyag: Azure Active Directoryval integrált Teamphoria |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Teamphoria között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 6bd549926ce2a39ca983c3160a6f11f4c55f2146
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923830"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Oktatóanyag: Azure Active Directoryval integrált Teamphoria

Ebben az oktatóanyagban elsajátíthatja Teamphoria integrálása az Azure Active Directory (Azure AD).

Teamphoria integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Teamphoria hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Teamphoria (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Teamphoria, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Teamphoria egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.
Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Teamphoria hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-teamphoria-from-the-gallery"></a>A gyűjteményből Teamphoria hozzáadása
Az Azure AD integrálása a Teamphoria konfigurálásához kell hozzáadnia Teamphoria a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Teamphoria hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure Portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra.

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Teamphoria**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

5. Az eredmények panelen válassza ki a **Teamphoria**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Teamphoria.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Teamphoria a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Teamphoria közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Teamphoria tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Teamphoria tesztfelhasználó létrehozása](#creating-a-teamphoria-test-user)**  - való Britta Simon valami Teamphoria, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Teamphoria alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Teamphoria** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Az a **Teamphoria tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-CÍMÉT a következő mintát: `https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > A bejelentkezési URL-cím értéke nincs valós. Ez az érték a tényleges bejelentkezési URL-címet frissíteni kell. Ügyfél [Teamphoria ügyfél-támogatási csoport](https://www.teamphoria.com/) lekérni a bejelentkezési URL-CÍMÉT.

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_general_400.png)

6. A a **Teamphoria konfigurációs** kattintson **konfigurálása Teamphoria** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. Egyszeri bejelentkezés konfigurálása **Teamphoria** oldalán, jelentkezzen be rendszergazdaként a Teamphoria alkalmazás.

8. Ugrás a **rendszergazdai beállítások** lehetőséget a bal oldali eszköztár, valamint az konfigurálása lapon kattintson a **egyetlen SIGN-ON** az SSO konfigurációs ablak megnyitásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/admin_sso_configure.png)

9. Kattintson a **hozzáadása új IDENTITÁSSZOLGÁLTATÓ** az egyszeri bejelentkezés beállítások hozzáadásával a képernyő jobb felső sarokban beállítást.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/add_new_identity_provider.png)

10. Adja meg a mezők a részleteket lásd az alábbi-

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **MEGJELENÍTENDŐ név**: a beépülő modul megjelenítendő nevét adja meg a felügyelet lapon.

    b. **GOMB neve**: a lap használatával történő Egyszeri bejelentkezéshez a bejelentkezési lapon megjelenő nevét.

    c. **TANÚSÍTVÁNY**: Nyissa meg a tanúsítványt a Jegyzettömbben, Azure-portálról letöltött ugyanazt a tartalom másolása és illessze be ide a mezőbe.

    d. **A belépési pont**: illessze be a **SAML-alapú egyszeri bejelentkezési URL-címe** kimásolt Azure-portálról.

    e. Váltás is **ON** , majd kattintson a **mentése**.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="creating-a-teamphoria-test-user"></a>Teamphoria tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Teamphoria bejelentkezni, akkor ki kell építenie Teamphoria be. Teamphoria, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Teamphoria vállalati webhely.

2. Kattintson a **ADMIN** a bal oldali eszköztáron és a beállítások a **kezelése** lapon kattintson a **felhasználók** a felhasználók számára a rendszergazda lap megnyitásához.

    ![Alkalmazott hozzáadása](./media/teamphoria-tutorial/admin_manage_users.png)

3. Kattintson a **manuális MEGHÍVÁSA** lehetőséget.

    ![Felkérése](./media/teamphoria-tutorial/admin_manage_add_users.png)

4. Ezen a lapon hajtsa végre a következő művelet.
    
    ![Felkérése](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Az a **E-mail cím** szövegmezőhöz a **e-mail cím** a BrittaSimon.

    b. Az a **UTÓNÉV** szövegmezőhöz típus **Britta**.

    c. Az a **Vezetéknév** szövegmezőhöz típus **Simon**.

    d. Kattintson a **MEGHÍVÁSA 1 felhasználó**. Fogadja el a rendszer létrehozása a meghívott felhasználó felhasználónak kell.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Teamphoria Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200]

**Britta Simon hozzárendelése Teamphoria, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **Teamphoria**.

    ![Egyszeri bejelentkezés konfigurálása](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
