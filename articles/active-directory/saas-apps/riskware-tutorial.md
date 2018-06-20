---
title: 'Oktatóanyag: Azure Active Directoryval integrált Riskware |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Riskware között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2062ba20b09e8e42fd094fbff628f5e76b5ea00f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225789"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Oktatóanyag: Azure Active Directoryval integrált Riskware

Ebben az oktatóanyagban elsajátíthatja Riskware integrálása az Azure Active Directory (Azure AD).

Riskware integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Riskware szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Riskware (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Riskware, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Riskware egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Riskware hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-riskware-from-the-gallery"></a>A gyűjteményből Riskware hozzáadása
Az Azure AD integrálása a Riskware konfigurálásához kell hozzáadnia Riskware a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Riskware hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Riskware**, jelölje be **Riskware** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Riskware](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Riskware.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Riskware a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Riskware közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Riskware tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Riskware tesztfelhasználó létrehozása](#create-a-riskware-test-user)**  - való Britta Simon valami Riskware, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Riskware alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Riskware, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Riskware** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. Az a **Riskware tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Riskware tartomány és az URL-címek](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | TERMÉK| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` | 
    |||

    b. Az a **azonosítója (entitás azonosítója)** szövegmezőhöz URL-címet írja be:
    | Környezet| Az URL-minta|
    |--|--|
    | Felhasználói tesztelés| `https://riskcloud.net/uat` |
    | TERMÉK| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` | 
    |||

    > [!NOTE] 
    > Nincs valós a bejelentkezési URL-címével. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Riskware ügyfél-támogatási csoport](mailto:support@pansoftware.com.au) az értéket be kell olvasni.

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/riskware-tutorial/tutorial_general_400.png)

6. A a **Riskware konfigurációs** kattintson **konfigurálása Riskware** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Riskware konfiguráció](./media/riskware-tutorial/tutorial_riskware_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Riskware vállalati webhely rendszergazdaként.

8. Kattintson a jobb felső sarokban **karbantartási** karbantartási lapjának megnyitásához. 

    ![Riskware konfigurációk karbantartása](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. A karbantartási oldalon kattintson **hitelesítési**.

    ![Riskware konfigurációs authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. A **hitelesítési** lapon, a következő lépésekkel:

    ![Riskware konfigurációs authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Válassza ki **típus** , **SAML** hitelesítéshez.

    b. Az a **kód** szövegmező, írja be a kódját, például AZURE_UAT.

    c. Az a **leírás** szövegmező, írja be a leírását, például AZURE konfigurációs egyszeri bejelentkezéshez.

    d. A **egyszeri bejelentkezési oldalon** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    e. A **lap Kijelentkezés** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.

    f. Az a **Post űrlapmező** szövegmezőhöz a feladás egy vagy több választ, amely tartalmazza a SAML SamlResponse például szerepel mező neve

    g. Az a **XML-identitás címkenév** szövegmezőhöz attribútum, amely tartalmazza a SAML-válasz NameID hasonlóan az egyedi azonosító.

    h. Nyissa meg a letöltött **metaadatainak XML-kódja** Azure-portálon a Jegyzettömbben, a tanúsítványt a metaadat-fájlt másolja és illessze be azt a **tanúsítvány** szövegmező
    
    i. A **ügyfél URL-címe** szövegmezőhöz illessze be az értékét **válasz URL-CÍMEN**, amely a támogatási csapat kap.

    j. A **kibocsátó** szövegmezőhöz illessze be az értékét **azonosítója**, amely a támogatási csapat kap.

    > [!Note]
    > Ügyfél [Riskware ügyfél-támogatási csoport](mailto:support@pansoftware.com.au) beolvasni ezeket az értékeket

    k. Válassza ki **használata utáni** átadni a SAML-kérelmek post paraméterként.

    l. Válassza ki **használható SAML-kérelmek** továbbítani SSO engedélyezése SAML-kérelmek az SP kezdeményezett.

    m. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/riskware-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/riskware-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/riskware-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/riskware-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-riskware-test-user"></a>Riskware tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Riskware bejelentkezni, akkor ki kell építenie a Riskware. A Riskware egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a biztonsági-rendszergazdájaként Riskware.

2. Kattintson a jobb felső sarokban **karbantartási** karbantartási lapjának megnyitásához. 

    ![Riskware konfigurációs tart fenn.](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. A karbantartási oldalon kattintson **személyek**.

    ![Riskware konfigurációs személyek](./media/riskware-tutorial/tutorial_riskware_people.png)

4. Az a **részletek** lapra, hajtsa végre a következő lépéseket:
    
    ![Riskware konfiguráció részletei](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Válassza ki **személy típusa** , például alkalmazott.

    b. A **Utónév** szövegmező, írja be például a felhasználó utónevét **Britta**.

    c. A **vezetékneve** szövegmező, írja be például a felhasználó vezetéknevét **Simon**.

5. Az a **biztonsági** lapra, hajtsa végre a következő lépéseket:    

    ![Riskware konfigurációs biztonság](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. A **hitelesítési** szakaszban jelölje be a **hitelesítési** , például AZURE konfigurációs módját, amely be kell állítania az egyszeri bejelentkezés.

    b. A **bejelentkezési adatait** ebben a szakaszban a **felhasználói azonosító** szövegmezőhöz adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.

    c. Az a **jelszó** szövegmező, adja meg a felhasználó jelszavát.

6. Az a **szervezet** lapra, hajtsa végre a következő lépéseket:

    ![Riskware konfigurációs szervezeti](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. A **szervezet** területen válassza ki az adott szervezet **Level1** szervezet.
    
    b. A **személy elsődleges munkahelyi** részben, a a **hely** szövegmezőhöz meg azt a helyet.

    c. A **alkalmazott** szakaszban jelölje be **alkalmazott állapota** alkalmi, például.

7. Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Riskware Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Riskware, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Riskware**.

    ![Az alkalmazások listáját a Riskware hivatkozás](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Riskware csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Riskware alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

