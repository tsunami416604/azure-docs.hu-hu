---
title: "Oktatóanyag: Azure Active Directory-integráció Mimecast személyes portállal |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a személyes Portal Mimecast között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: bf46da35a55608d7e4656c9dd3ad9d5f2253e225
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Oktatóanyag: Azure Active Directory-integráció Mimecast személyes portállal

Ebben az oktatóanyagban elsajátíthatja Mimecast személyes Portal integrálása az Azure Active Directory (Azure AD).

Mimecast személyes Portal integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Mimecast személyes Portal hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Mimecast személyes portál (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Mimecast személyes portállal, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- Egy Mimecast személyes Portal egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Mimecast személyes portál hozzáadását a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Mimecast személyes portál hozzáadását a gyűjteményből
Az Azure AD integrálása a Mimecast személyes Portal konfigurálásához kell hozzáadnia Mimecast személyes Portal a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Mimecast személyes Portal hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Mimecast személyes Portal**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_search.png)

5. Az eredmények panelen válassza ki a **Mimecast személyes Portal**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Mimecast személyes Portal "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Mimecast személyes portálon a felhasználók az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Mimecast személyes portál közötti kapcsolat kapcsolatot kell létrehozni.

Mimecast személyes portálon, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Mimecast személyes portállal tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Mimecast személyes Portal tesztfelhasználó létrehozása](#creating-a-mimecast-personal-portal-test-user)**  - való egy megfelelője a Britta Simon Mimecast személyes portál, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Mimecast személyes portál alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Mimecast személyes portállal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Mimecast személyes Portal** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Az a **Mimecast személyes Portal tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |     
    | ----------------------------------------|
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|
    | |
   
    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:

    | |     
    | --- |
    | `https://webmail-us.mimecast.com/sso/<companyname>`|
    | `https://webmail-uk.mimecast.com/sso/<companyname>`|    
    | `https://webmail-za.mimecast.com/sso/<companyname>`|
    | `https://webmail.mimecast-offshore.com/sso/<companyname>`|
    ||                                                 
    
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Mimecast személyes portál ügyfél-támogatási csoport](https://www.mimecast.com/customer-success/technical-support/) beolvasni ezeket az értékeket. 
 


4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. A a **Mimecast személyes portál konfigurációs** kattintson **Mimecast személyes portál konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Mimecast személyes portálra rendszergazdaként.

8. Ugrás a **szolgáltatások \> alkalmazások**.
   
    ![Alkalmazások](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "alkalmazások")

9. Kattintson a **hitelesítési profilok**.
   
    ![Hitelesítési profilok](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "hitelesítési profilok")

10. Kattintson a **új hitelesítési profil**.
   
    ![Új hitelesítési profil](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "új hitelesítési profil")

11. Az a **hitelesítési profil** területen tegye a következőket:
   
    ![Hitelesítési profil](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "hitelesítési profil")
   
    a. Az a **leírás** szövegmező, adja meg a konfiguráció nevét.
   
    b. Válassza ki **Mimecast személyes portál SAML-alapú hitelesítés kényszerítéséhez**.
   
    c. Mint **szolgáltató**, jelölje be **Azure Active Directory**.
   
    d. A **kiállítójának URL-címe** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
   
    e. A **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    f. A **kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    g. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure portálról letöltött, annak tartalmának másolása a vágólapra és illessze be azt a **Identitástanúsítvány szolgáltató (Metadata)** szövegmező.

    h. Válassza ki **engedélyezése egyszeri bejelentkezéshez**.
   
    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mimecast-personal-portal-test-user"></a>Mimecast személyes Portal tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be személyes Mimecast portálra, akkor ki kell építenie Mimecast személyes portált. Mimecast személyes Portal, ha egy kézi tevékenység.

Regisztrálja a tartományi felhasználók létrehozása előtt kell.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mimecast személyes Portal** rendszergazdaként.

2. Ugrás a **könyvtárak \> belső**.
   
    ![Könyvtárak](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "könyvtárak")

3. Kattintson a **regisztrálni az új tartomány**.
   
    ![Új tartomány regisztrálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "új tartomány regisztrálása")

4. Az új tartomány létrehozása után kattintson **új cím**.
   
    ![Új cím](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "új cím")

5. Új cím párbeszédpanelen a következő lépésekkel egy érvényes Azure AD fiók kiépítése:
   
    ![Mentés](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "mentése")
   
    a. Az a **E-mail cím** szövegmezőhöz típus **E-mail cím** , a felhasználó  **BrittaSimon@contoso.com** .
    
    b. Az a **globális név** szövegmezőhöz típusa a **felhasználónév** , **BrittaSimon**.

    c. Az a **jelszó**, és **jelszó megerősítése** szövegmezőből, típusa a **jelszó** felhasználó.
   
    b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Mimecast személyes portál felhasználói fiók létrehozása eszközök vagy Mimecast személyes portál által szolgáltatott API-k segítségével kiépíteni az Azure AD felhasználói fiókokat. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mimecast személyes portál Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Mimecast személyes Portal, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Mimecast személyes Portal**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Mimecast személyes Portal csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Mimecast személyes portál alkalmazásba. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

