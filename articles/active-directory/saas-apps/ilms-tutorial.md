---
title: 'Oktatóanyag: Azure Active Directoryval integrált iLMS |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: cdb59a7078b3bbce112356b61e19702fb6ed9df1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228723"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Oktatóanyag: Azure Active Directoryval integrált iLMS

Ebben az oktatóanyagban elsajátíthatja iLMS integrálása az Azure Active Directory (Azure AD).

ILMS integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a iLMS hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a iLMS (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs iLMS, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy iLMS egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből iLMS hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ilms-from-the-gallery"></a>A gyűjteményből iLMS hozzáadása
Az Azure AD integrálása a iLMS konfigurálásához kell hozzáadnia iLMS a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből iLMS hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **iLMS**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/tutorial_ilms_search.png)

5. Az eredmények panelen válassza ki a **iLMS**, majd kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján iLMS.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó iLMS a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a iLMS közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** iLMS a.

Az Azure AD egyszeri bejelentkezést a iLMS tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy iLMS tesztfelhasználó létrehozása](#creating-an-ilms-test-user)**  - kell rendelkeznie egy Britta Simon megfelelője a iLMS, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az iLMS alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés iLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **iLMS** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_samlbase.png)

3. Az a **iLMS tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_url.png)

    a. Az a **azonosítója** szövegmező, illessze be a **azonosítója** értéket másol a **szolgáltató** iLMS felügyeleti portál SAML-beállítások szakasza.

    b. Az a **válasz URL-CÍMEN** szövegmező, illessze be a **végpont URL-** értéket másol a **szolgáltató** szakasz iLMS felügyeleti portál, hogy a következő mintát SAML-beállítások `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

    >[!Note]
    >Ez 123456 példa érték azonosító.

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, illessze be a **végpont URL-** értéket másol a **szolgáltató** szakasz iLMS felügyeleti portálon SAML-beállítások `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`     

5. Igény szerinti kiépítés engedélyezéséhez iLMS alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/4.png)
    
    Hozzon létre **részleg, régió** és **osztás** attribútumok közül, adja hozzá ezek az attribútumok nevét iLMS. A fent látható összes attribútum megadása kötelező.  

    > [!NOTE] 
    > Engedélyezni kell **Un-recognized felhasználói fiók létrehozása** a iLMS ezek az attribútumok hozzárendelését. Kövesse az utasításokat [Itt](http://support.inspiredelearning.com/customer/portal/articles/2204526) attribútumok konfigurálása képet kapjon.

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ---------------| --------------- |    
    | osztály | felhasználó.részleg |
    | régió | User.state |
    | részleg | user.jobtitle |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

7. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse az XML-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_general_400.png)

9. Egy másik webes böngészőablakban, jelentkezzen be a **iLMS felügyeleti portál** rendszergazdaként.

10. Kattintson a **SSO:SAML** alatt **beállítások** lapon nyissa meg a SAML-beállítások, és hajtsa végre a következő lépéseket:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png) 

    a. Bontsa ki a **szolgáltató** szakaszt, és másolja a **azonosító** és **végpont URL-** érték.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

    b. A **identitásszolgáltató** kattintson **metaadatok importálása**.
    
    c. Válassza ki a **metaadatok** az Azure-portálról letöltött fájl **SAML-aláíró tanúsítványa** szakasz.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. Ha később engedélyezni kívánja történő iLMS fiókokat hozhat létre a un JIT-ismeri fel a felhasználók, kövesse az alábbi lépéseket:
        
       - Ellenőrizze **nem felismerhető felhasználói fiók létrehozása**.
       
       ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  Az attribútumok hozzárendelését iLMS attribútumokat az Azure AD-ben. Az attribútum oszlopának adja meg az attribútumok nevét vagy az alapértelmezett értéket.

    e. Ugrás a **üzleti szabályok** lapra, és hajtsa végre a következő lépéseket: 
        
       ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/5.png)

       - Ellenőrizze **Un-recognized régiók létrehozása, az osztályok és a szervezeti egységek** régiók, osztályok és szervezeti egységek, amely már létezik az egyszeri bejelentkezés időpontjában létrehozásához.
        
       - Ellenőrizze **frissítés felhasználói profil során bejelentkezés** adhatja meg, hogy a profil frissül minden egyszeri bejelentkezést. 
        
       - Ha a **"Frissítés üres értékek a nem kötelező mezők a felhasználói profil"** beállítás be van jelölve, a nem kötelező profil mező üres lesz a bejelentkezés után is iLMS profil üres értékek mezőket tartalmaz.
        
       - Ellenőrizze **hiba értesítő E-mail küldése** és adja meg a felhasználó e-mail címét, ahol szeretne kapni a hiba értesítő e-mailt.

11. Kattintson a **mentése** gombra a beállítások mentéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/save.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/ilms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-ilms-test-user"></a>Egy iLMS tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő a felhasználók átadása, miután a felhasználók hitelesítésére az alkalmazás automatikusan létrejönnek. Igény szerinti fog működni, ha rákattint a **Un-recognized felhasználói fiók létrehozása** jelölőnégyzet során iLMS felügyeleti portál a SAML-alapú konfigurációs beállítást.

Ha manuálisan hozzon létre egy felhasználó van szüksége, majd hajtsa végre a következő lépések:

1. Jelentkezzen be rendszergazdaként a iLMS vállalati webhely.

2. Kattintson a **"Felhasználó regisztrálása"** alatt **felhasználók** elemére kattintva nyissa meg **regisztrálása felhasználói** lap. 
   
   ![Alkalmazott hozzáadása](./media/ilms-tutorial/3.png)

3. Az a **"Felhasználó regisztrálása"** lapon, a következő lépésekkel.

    ![Alkalmazott hozzáadása](./media/ilms-tutorial/create_testuser_add.png)

    a. Az a **Keresztnév** szövegmezőhöz Britta az első típusnév.
   
    b. Az a **Vezetéknév** szövegmező, írja be a vezetéknevet Simon.

    c. Az a **E-mail azonosító** szövegmezőhöz Britta Simon fiók e-mail címét.

    d. Az a **régió** legördülő menüben válassza ki a régiót értékét.

    e. Az a **osztás** legördülő menüben válassza ki a részleg értékét.

    f. Az a **részleg** legördülő menüben válassza ki a részleg értékét.

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE] 
    > Elküldheti regisztrációs mail felhasználói kiválasztásával **regisztrációs üzenet küldése** jelölőnégyzetet.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó saját iLMS való hozzáférés biztosítása.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése iLMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **iLMS**.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen iLMS csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az iLMS alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ilms-tutorial/tutorial_general_01.png
[2]: ./media/ilms-tutorial/tutorial_general_02.png
[3]: ./media/ilms-tutorial/tutorial_general_03.png
[4]: ./media/ilms-tutorial/tutorial_general_04.png

[100]: ./media/ilms-tutorial/tutorial_general_100.png

[200]: ./media/ilms-tutorial/tutorial_general_200.png
[201]: ./media/ilms-tutorial/tutorial_general_201.png
[202]: ./media/ilms-tutorial/tutorial_general_202.png
[203]: ./media/ilms-tutorial/tutorial_general_203.png

