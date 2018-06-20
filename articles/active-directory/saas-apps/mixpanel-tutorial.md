---
title: 'Oktatóanyag: Azure Active Directoryval integrált Mixpanel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Mixpanel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 0052abf3dc3aae6be51d66d86a71830547106a23
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226595"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Oktatóanyag: Azure Active Directoryval integrált Mixpanel

Ebben az oktatóanyagban elsajátíthatja Mixpanel integrálása az Azure Active Directory (Azure AD).

Mixpanel integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Mixpanel hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Mixpanel (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Mixpanel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Mixpanel egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Mixpanel hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-mixpanel-from-the-gallery"></a>A gyűjteményből Mixpanel hozzáadása
Az Azure AD integrálása a Mixpanel konfigurálásához kell hozzáadnia Mixpanel a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Mixpanel hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Mixpanel**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/tutorial_mixpanel_search.png)

5. Az eredmények panelen válassza ki a **Mixpanel**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Mixpanel.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Mixpanel a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Mixpanel közötti kapcsolat kapcsolatot kell létrehozni.

Mixpanel, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Mixpanel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Mixpanel tesztfelhasználó létrehozása](#creating-a-mixpanel-test-user)**  - való Britta Simon valami Mixpanel, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Mixpanel alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Mixpanel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Mixpanel** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

3. Az a **Mixpanel tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_url.png)

     Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://mixpanel.com/login/`

    > [!NOTE] 
    > Regisztrálja a [ https://mixpanel.com/register/ ](https://mixpanel.com/register/) állíthat be a bejelentkezési hitelesítő adatokat, és lépjen kapcsolatba a [Mixpanel támogatási csoport](mailto:support@mixpanel.com) ahhoz, hogy a bérlő SSO beállításait. Is letölthető az URL-cím bejelentkezési érték szükség esetén a Mixpanel támogatási csoportjához. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_general_400.png)

6. A a **Mixpanel konfigurációs** kattintson **konfigurálása Mixpanel** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_configure.png) 

7. Egy másik böngészőablakban bejelentkezés Mixpanel Alkalmazásmódosítások rendszergazdaként.

8. A lap alján, kattintson a kevés **áttételi** ikonra a bal oldali sarokban. 
   
    ![Mixpanel egyszeri bejelentkezés](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

9. Kattintson a **biztonságos hozzáférését** fülre, majd **beállításainak módosítása**.
   
    ![Mixpanel beállítások](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

10. A a **módosítania kell a tanúsítványt** párbeszédpanel lap, kattintson a **fájl kiválasztása** a letöltött tanúsítvány feltöltése, majd **tovább**.
   
    ![Mixpanel beállítások](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

11.  A hitelesítési URL-cím beviteli mezőben szereplő a **módosítsa a hitelesítési URL-címet** párbeszédpanel lapon, és beillesztheti értékének **SAML-alapú egyszeri bejelentkezési URL-címe** másolta az Azure-portálról, amely majd **Következő**.
   
   ![Mixpanel beállítások](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

12. Kattintson a **Done** (Kész) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mixpanel-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mixpanel-test-user"></a>Mixpanel tesztfelhasználó létrehozása

Ez a szakasz célja Mixpanel Britta Simon nevű felhasználót létrehozni. 

1. Jelentkezzen be rendszergazdaként a Mixpanel vállalati webhely.

2. Kattintson a lap alján a gombra kevés fogaskerék nyissa meg a bal oldali sarokban a **beállítások** ablak.

3. Kattintson a **Team** fülre.

4. Az a **csapattag** szövegmező, írja be a Britta meg e-mail címét az Azure-ban.
   
    ![Mixpanel beállítások](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Kattintson a **meghívása**. 

> [!Note]
> A felhasználó kap egy e-mailt a profil beállításához.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mixpanel Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Mixpanel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Mixpanel**.

    ![Egyszeri bejelentkezés konfigurálása](./media/mixpanel-tutorial/tutorial_mixpanel_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Mixpanel csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Mixpanel alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/mixpanel-tutorial/tutorial_general_203.png

