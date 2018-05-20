---
title: 'Oktatóanyag: Azure Active Directoryval integrált Mimecast felügyeleti konzol |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a felügyeleti konzol Mimecast között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: bef0312a3ee44a441f44eb2ae7e4292b966cec3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Oktatóanyag: Azure Active Directoryval integrált Mimecast felügyeleti konzol

Ebben az oktatóanyagban elsajátíthatja Mimecast felügyeleti konzol integrálása az Azure Active Directory (Azure AD).

Felügyeleti konzol Mimecast integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Mimecast felügyeleti konzol szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Mimecast felügyeleti konzol (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Mimecast felügyeleti konzol, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A felügyeleti konzol Mimecast egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Mimecast felügyeleti konzol hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>A gyűjteményből Mimecast felügyeleti konzol hozzáadása
Az Azure AD integrálása a Mimecast felügyeleti konzol konfigurálásához kell hozzáadnia Mimecast felügyeleti konzol a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Mimecast felügyeleti konzol a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Mimecast felügyeleti konzol**, jelölje be **Mimecast felügyeleti konzol** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Mimecast felügyeleti konzol](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés Mimecast felügyeleti konzol "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Mimecast felügyeleti konzoljában a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Mimecast felügyeleti konzol közötti kapcsolat kapcsolatot kell létrehozni.

Mimecast felügyeleti konzolon, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Mimecast felügyeleti konzol tesztelése és konfigurálása, kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Felügyeleti konzol Mimecast tesztfelhasználó létrehozása](#create-a-mimecast-admin-console-test-user)**  - való egy megfelelője a Britta Simon Mimecast felügyeleti konzol, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a felügyeleti konzol Mimecast alkalmazásban egyszeri bejelentkezés konfigurálása.

**Mimecast felügyeleti konzol konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Mimecast felügyeleti konzol** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. Az a **Mimecast felügyeleti konzol tartományát és URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Mimecast felügyeleti konzol tartományát és URL-címek](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, írja be az URL-cím:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > A bejelentkezési URL-címet az adott régió.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. A a **Mimecast felügyeleti konzol konfiguráció** kattintson **konfigurálása Mimecast felügyeleti konzol** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Mimecast felügyeleti konzol konfiguráció](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Mimecast felügyeleti konzolt rendszergazdaként.

8. Ugrás a **szolgáltatások \> alkalmazás**.

    ![Szolgáltatások](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "szolgáltatások")

9. Kattintson a **hitelesítési profilok**.

    ![Hitelesítési profilok](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "hitelesítési profilok")
    
10. Kattintson a **új hitelesítési profil**.

    ![Új hitelesítési profilok](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "új hitelesítési profilok")

11. Az a **hitelesítési profil** területen tegye a következőket:

    ![Hitelesítési profil](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "hitelesítési profil")
    
    a. Az a **leírás** szövegmező, adja meg a konfiguráció nevét.
    
    b. Válassza ki **Mimecast felügyeleti konzol az SAML-alapú hitelesítés kényszerítéséhez**.
    
    c. Mint **szolgáltató**, jelölje be **Azure Active Directory**.
    
    d. Beillesztés **SAML Entitásazonosító**, amely az Azure-portálról másolta a **kiállítójának URL-címe** szövegmező.
    
    e. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálról másolta a **bejelentkezési URL-cím** szövegmező.

    f. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálról másolta a **kijelentkezési URL-cím** szövegmező.
    
    >[!NOTE]
    >A bejelentkezési URL-címe és kijelentkezési URL-cím értéke ugyanazok a Mimecast felügyeleti konzolon.
    
    g. Nyissa meg a Jegyzettömbben az Azure portálról letöltött a base-64 tanúsítvány, távolítsa el az első sorban ("*--*") és az utolsó sort ("*--*"), másolja be azt a maradék tartalmat a vágólapra, majd illessze be azt a **Identitástanúsítvány szolgáltató (Metadata)** szövegmező.
    
    h. Válassza ki **engedélyezése egyszeri bejelentkezéshez**.
    
    i. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Felügyeleti konzol Mimecast tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mimecast felügyeleti konzol bejelentkezni, akkor ki kell építenie Mimecast felügyeleti konzolhoz. Mimecast felügyeleti konzolon, ha egy kézi tevékenység.

* Regisztrálja a tartományi felhasználók létrehozása előtt kell.

**Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mimecast felügyeleti konzol** rendszergazdaként.
2. Ugrás a **könyvtárak \> belső**.
   
   ![Könyvtárak](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "könyvtárak")
3. Kattintson a **regisztrálni az új tartomány**.
   
   ![Új tartomány regisztrálása](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "új tartomány regisztrálása")
4. Az új tartomány létrehozása után kattintson **új cím**.
   
   ![Új cím](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "új cím")
5. Új cím párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Mentés](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "mentése")
   
   a. Típus a **E-mail cím**, **globális név**, **jelszó**, és **jelszó megerősítése** attribútumok egy érvényes Azure ad meg fiók szeretné a kapcsolódó szövegmezők kiépíteni.

   b. Kattintson a **Save** (Mentés) gombra.

>[!NOTE]
>Mimecast felügyeleti konzol felhasználói fiók létrehozása eszközök vagy Mimecast felügyeleti konzol által nyújtott API-k segítségével kiépíteni az Azure AD felhasználói fiókokat. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Mimecast felügyeleti konzol használatához.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Mimecast felügyeleti konzol, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Mimecast felügyeleti konzol**.

    ![Az alkalmazások listáját a Mimecast felügyeleti konzol hivatkozás](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Mimecast felügyeleti konzol csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Mimecast felügyeleti konzol alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png

