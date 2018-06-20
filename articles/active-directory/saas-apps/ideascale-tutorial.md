---
title: 'Oktatóanyag: Azure Active Directoryval integrált IdeaScale |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és IdeaScale között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: bf42de8c1fa0e3c67a1a2bed6693b5adc0aae2d7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225711"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Oktatóanyag: Azure Active Directoryval integrált IdeaScale

Ebben az oktatóanyagban elsajátíthatja IdeaScale integrálása az Azure Active Directory (Azure AD).

IdeaScale integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a IdeaScale hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett IdeaScale (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs IdeaScale, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy IdeaScale egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből IdeaScale hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ideascale-from-the-gallery"></a>A gyűjteményből IdeaScale hozzáadása
Az Azure AD integrálása a IdeaScale konfigurálásához kell hozzáadnia IdeaScale a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből IdeaScale hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **IdeaScale**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/tutorial_ideascale_search.png)

5. Az eredmények panelen válassza ki a **IdeaScale**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján IdeaScale

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó IdeaScale a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a IdeaScale közötti kapcsolat kapcsolatot kell létrehozni.

IdeaScale, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a IdeaScale tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy IdeaScale tesztfelhasználó létrehozása](#creating-an-ideascale-test-user)**  - való Britta Simon valami IdeaScale, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az IdeaScale alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés IdeaScale, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **IdeaScale** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. Az a **IdeaScale tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<companyname>.ideascale.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [IdeaScale ügyfél-támogatási csoport](http://support.ideascale.com/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_general_400.png)

6. A a **IdeaScale konfigurációs** kattintson **konfigurálása IdeaScale** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML Entitásazonosító** a a **rövid összefoglaló szakasz**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a IdeaScale vállalati webhely rendszergazdaként.

8. Ugrás a **közösségi beállítások**.
   
    ![Közösségi beállítások](./media/ideascale-tutorial/ic790847.png "közösségi beállításai")

9. Ugrás a **biztonsági \> az egyszeri bejelentkezés beállítások**.
   
    ![Az egyszeri bejelentkezés beállítások](./media/ideascale-tutorial/ic790848.png "az egyszeri bejelentkezés beállításai")

10. Mint **egyszeri-bejelentkezés típusa**, jelölje be **SAML 2.0**.
   
    ![Az egyszeri bejelentkezés típusa](./media/ideascale-tutorial/ic790849.png "az egyszeri bejelentkezés típusa")

11. Az a **egyszeri bejelentkezés beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Az egyszeri bejelentkezés beállítások](./media/ideascale-tutorial/ic790850.png "az egyszeri bejelentkezés beállításai")
   
    a. A **SAML IdP Entitásazonosító** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    b. Másolja a letöltött metaadatfájl Azure-portálon, és illessze be azt a **SAML IdP metaadatok** szövegmező.

    c. A **kijelentkezési sikeres URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    d. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ideascale-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-ideascale-test-user"></a>Egy IdeaScale tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók IdeaScale bejelentkezni, akkor ki kell építenie a IdeaScale. IdeaScale, ha egy kézi tevékenység.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **IdeaScale** vállalati hely rendszergazdaként.

2. Ugrás a **közösségi beállítások**.
   
    ![Közösségi beállítások](./media/ideascale-tutorial/ic790847.png "közösségi beállításai")

3. Ugrás a **alapbeállítások \> tag felügyeleti**.

4. Kattintson a **felvenni abban az esetben**.
   
    ![Tag felügyeleti](./media/ideascale-tutorial/ic790852.png "tag felügyeleti")

5. Az új tag hozzáadása a szakaszban a következő lépésekkel:
   
    ![Adja hozzá az új tag](./media/ideascale-tutorial/ic790853.png "új tag hozzáadása")
   
    a. Az a **E-mail címek** szövegmező, írja be egy érvényes AAD-fiókba, azt szeretné, hogy építse ki e-mail címét.
   
    b. Kattintson a **módosítások mentése**. 
   
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos beolvassa e-mailben található egy hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.
      
>[!NOTE]
>Bármely más IdeaScale felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz IdeaScale által nyújtott API-k.
 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés IdeaScale Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése IdeaScale, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **IdeaScale**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ideascale-tutorial/tutorial_ideascale_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése


Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen IdeaScale csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az IdeaScale alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ideascale-tutorial/tutorial_general_01.png
[2]: ./media/ideascale-tutorial/tutorial_general_02.png
[3]: ./media/ideascale-tutorial/tutorial_general_03.png
[4]: ./media/ideascale-tutorial/tutorial_general_04.png

[100]: ./media/ideascale-tutorial/tutorial_general_100.png

[200]: ./media/ideascale-tutorial/tutorial_general_200.png
[201]: ./media/ideascale-tutorial/tutorial_general_201.png
[202]: ./media/ideascale-tutorial/tutorial_general_202.png
[203]: ./media/ideascale-tutorial/tutorial_general_203.png

