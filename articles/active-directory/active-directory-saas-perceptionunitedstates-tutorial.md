---
title: "Oktatóanyag: Azure Active Directory-integrációval rendelkező Egyensúlyozhatom Egyesült Államok (nem-UltiPro) |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 8e2f9f979f8b94e0c043d4db6e93bd7a53c3dd27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Oktatóanyag: Azure Active Directory-integrációval rendelkező Egyensúlyozhatom Egyesült Államok (nem-UltiPro)

Ebben az oktatóanyagban elsajátíthatja, hogyan Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) integrálása az Azure Active Directory (Azure AD).

Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a Egyensúlyozhatom Egyesült Államok (nem-UltiPro) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett a Egyensúlyozhatom Egyesült Államok (nem-UltiPro) (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) hozzáadása a gyűjteményből
Az integráció Egyensúlyozhatom Amerikai Egyesült államokbeli (nem-UltiPro) konfigurálása az Azure AD-be, meg kell hozzáadnia Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményben Érzete az Amerikai Egyesült Államok (nem-UltiPro) hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro)**, jelölje be **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában egyensúlyozhatom Egyesült Államok (nem-UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Egyensúlyozhatom Egyesült Államok (nem-UltiPro) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) közötti kapcsolat kapcsolatot kell létrehozni.

A Egyensúlyozhatom Egyesült Államok (nem-UltiPro), rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tesztfelhasználó létrehozása](#create-a-perception-united-states-non-ultipro-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon a Egyensúlyozhatom Egyesült Államok (nem-UltiPro), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) alkalmazásban.

**Az Azure AD az egyszeri bejelentkezés konfigurálása a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro), hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Az a **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tartományhoz és URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információkat egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tartományhoz és URL-címek](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím:`https://perception.kanjoya.com/sp`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Az érték nincs valós. Az érték a tényleges válasz URL-címet, az oktatóanyag későbbi részében ismertetett frissíti.
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_400.png)

6. A a **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) konfigurációs** kattintson **Egyensúlyozhatom Egyesült Államok konfigurálása (nem-UltiPro)** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    a. A **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro)** alkalmazáshoz szükséges a **SAML Entitásazonosító** érték, amely másolta, hogy uri-kódolt. Az uri-kódolt érték, amelyet a következő hivatkozásra:**http://www.url-encode-decode.com/**.

    b. Az uri beolvasása után kódolt érték kombinálhatja a a **válasz URL-CÍMEN** ahogyan az alábbi -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Illessze be a fenti értéket a **válasz URL-CÍMEN** textbox **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tartományhoz és URL-címek** szakasz.

    ![Az Amerikai Egyesült Államok (nem UltiPro) konfigurációs érzete](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) vállalati webhely rendszergazdaként.

8. Kattintson az eszköztáron **Fiókbeállítások**.

    ![Az Amerikai Egyesült Államok (nem-UltiPro) felhasználói érzete](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Az a **Fiókbeállítások** lapon, a következő lépésekkel:

    ![Az Amerikai Egyesült Államok (nem-UltiPro) felhasználói érzete](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Az a **vállalatnév** szövegmező, írja be a **vállalati**.
    
    b. Az a **fióknév** szövegmező, írja be a **fiók**.

    c. A **alapértelmezett válaszcím E-mail** szöveg mezőbe írja be az érvényes **E-mail**.

    d. Válassza ki **SSO identitásszolgáltató** , **SAML 2.0**.

10. Az a **SSO konfigurációs** lapon, a következő lépésekkel:

    ![Az Amerikai Egyesült Államok (nem UltiPro) SSOConfig érzete](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Válassza ki **SAML NameID típus** , **E-mail**.

    b. Az a **SSO konfiguráció neve** szövegmező, írja be a **konfigurációs**.
    
    c. A **identitás-szolgáltató neve** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta. 

    d. A **SAML tartomány szövegmező**, írja be a tartományt például  **@contoso.com** .

    e. Kattintson a **töltse fel újra** feltölteni a **metaadatainak XML-kódja** fájlt.

    f. Kattintson a **frissítés**.


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) nevű felhasználót hoz létre. Együttműködve [Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) támogatási csoport](http://www.ultimatesoftware.com/Contact/ContactUs) a felhasználók hozzáadása a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) platform.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférés biztosítása a Egyensúlyozhatom Egyesült Államok (nem-UltiPro) által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**A Egyensúlyozhatom Egyesült Államok (nem-UltiPro) Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro)**.

    ![Az alkalmazások listáját a Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) hivatkozás](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) csempére kattint, meg kell beolvasása automatikusan bejelentkezett az Egyensúlyozhatom az Amerikai Egyesült Államok (nem-UltiPro) alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_203.png

