---
title: "Oktatóanyag: Azure Active Directoryval integrált munkahelyi által Facebook-on |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a munkahely által Facebook között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 1590a66f215f0c093d24ff602c0ad951ba1e1eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Oktatóanyag: Azure Active Directoryval integrált munkahelyi által Facebook-on

Ebben az oktatóanyagban elsajátíthatja által Facebook munkahelyi integrálása az Azure Active Directory (Azure AD).

Munkahelyi által Facebook integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáféréssel rendelkezik a munkahelyi Facebook által szabályozható
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett munkahelyi Facebook (egyszeri bejelentkezés) által az Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs munkahelyi által Facebook, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A munkahelyi Facebook egyszeri bejelentkezés által engedélyezett előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Munkahelyi által Facebook hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Munkahelyi által Facebook hozzáadása a gyűjteményből
Az Azure AD integrálása a munkahely által Facebook konfigurálásához kell hozzáadnia munkahelyi Facebook által a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a munkahelyi Facebook által a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a ** [Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **által Facebook munkahelyi**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

5. Az eredmények panelen válassza ki a **által Facebook munkahelyi**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés a munkahelyi által Facebook "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó által Facebook munkahelyi Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a munkahelyi Facebook által közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** munkahelyi Facebook által.

Az Azure AD az egyszeri bejelentkezés a munkahelyi által Facebook tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on) ** – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Újrahitelesítés gyakoriságának beállítása](#configuring-reauthentication-frequency) ** - bekéri a SAML-ellenőrzés a munkahelyi konfigurálásához.
3. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user) ** – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[A munkahelyi Facebook teszt felhasználó létrehozása](#creating-a-workplace-by-facebook-test-user) ** - való Britta Simon egy megfelelője a Facebook, a felhasználó az Azure AD-ábrázolását kapcsolódó által munkahelyi.
5. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user) ** - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on) ** – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és egyszeri bejelentkezés konfigurálása a munkahelyi Facebook-alkalmazás.

**Konfigurálása az Azure AD az egyszeri bejelentkezés munkahelyi által Facebook, a következő lépésekkel:**

1. Az Azure portálon a a **által Facebook munkahelyi** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. Az a **Facebook-tartomány és az URL-címek munkahelyi** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<instancename>.facebook.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://www.facebook.com/company/<instancename>`

    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Facebook ügyfél-támogatási csoport által munkahelyi](https://workplace.fb.com/faq/) beolvasni ezeket az értékeket. 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_400.png)

6. Az a **Facebook konfigurációja munkahelyi** területen kattintson **munkahelyi konfigurálása által a Facebook** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/config.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a munkahelyi Facebook vállalati hely rendszergazdaként.
  
   > [!NOTE] 
   > A SAML-hitelesítési folyamat részeként munkahelyi előfordulhat, hogy kihasználhassák a lekérdezési karakterláncok legfeljebb 2,5 kilobájt méretű ahhoz, hogy az Azure AD-paraméterekkel.

8. Az a **vállalati irányítópult**, navigáljon a **hitelesítési** fülre.

9. A **SAML-alapú hitelesítés**, jelölje be **SSO csak** a legördülő listából.

10. Adjon meg az értékeket, átmásolva **Facebook konfigurációja munkahelyi** szakasza a megfelelő mezőkbe az Azure-portálon:

    *   A **SAML-alapú URL-cím** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.
    *   A **SAML kiállítójának URL-címe szövegmező**, illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta.
    *   A **SAML kijelentkezési átirányítási** (nem kötelező), illessze be az értékét **Sign-Out URL-cím**, amely az Azure-portálon másolta.
    *   Nyissa meg a **base-64 kódolású tanúsítvány** Azure portálról letöltött fájlt, másolja a vágólapra a tartalmát, és illessze be azt a **SAML tanúsítvány** szövegmező.

11. Szükség lehet a célközönség URL-címet, címzett URL-címet, és ACS (helyességi feltétel fogyasztói szolgáltatás) URL-cím alatt felsorolva a **SAML-alapú konfigurációs** szakasz.

12. A szakasz alján görgessen, majd kattintson a **teszt SSO** gombra. Ennek eredményeképp a egy előugró ablakban jelenik meg az Azure AD bejelentkezési oldal jelenik meg. Adja meg a hitelesítő adatait hitelesítéséhez szokásos módon. 

    **Hibaelhárítás:** ellenőrizze az e-mail cím, az Azure AD vissza, megegyezik a munkahelyi fiókkal jelentkezett be az ad vissza.

13. Ha a vizsgálat sikeresen befejeződött, görgessen a lap alján, és kattintson a **mentése** gombra.

14. Minden felhasználó használja a munkahelyi most számára jelenik meg az Azure AD bejelentkezési oldalt a hitelesítéshez.

15. **SAML kijelentkezési átirányítási (nem kötelező)** - 

    Ha szeretné, opcionálisan egy SAML kijelentkezési URL-címek konfigurálása, mutasson az Azure AD kijelentkezési oldalon használható. Ha ezt a beállítást engedélyezve és konfigurálva van, a felhasználó már nem jutnak a munkahelyi kijelentkezési lapra. Ehelyett a felhasználó hozzá lett adva a SAML kijelentkezési átirányítási beállítást a URL-címre irányítja.


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="configuring-reauthentication-frequency"></a>Újrahitelesítés gyakoriság beállítása

Beállíthatja a munkahelyi kérjen egy SAML-ellenőrzést minden nap, három nap, hét, két hét, hónap vagy egyáltalán nem.

> [!NOTE] 
>A mobilalkalmazás az SAML-ellenőrzését minimális értéke egy hét.

A SAML alaphelyzetbe állítja az összes felhasználó számára a gombra kattintva is kényszeríthető: szükséges SAML-hitelesítés az összes felhasználó számára.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>A munkahelyi Facebook teszt felhasználó létrehozása

Ebben a szakaszban Britta Simon nevű felhasználó létrehozta a munkaterületen Facebook-on. Munkahelyi Facebook által támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van.

Nincs olyan művelet, ebben a szakaszban. Ha a felhasználó nem létezik a munkahely által Facebook, egy új hozta létre munkahelyi elérésére tett kísérlet során Facebook-on.

>[!Note]
>Ha a felhasználót manuálisan kell létrehozni, lépjen kapcsolatba kell [munkahelyi Facebook ügyfél-támogatási csoport szerint](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban Britta Simon munkahelyi Facebook által biztosított hozzáférés szerint használandó Azure egyszeri bejelentkezés engedélyezése.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése munkahelyi által Facebook, a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **által Facebook munkahelyi**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ha azt szeretné, az egyszeri bejelentkezés beállításainak ellenőrzéséhez nyissa meg a hozzáférési Panel.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)
* [A felhasználók átadása konfigurálása](active-directory-saas-workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workplacebyfacebook-tutorial/tutorial_general_203.png

